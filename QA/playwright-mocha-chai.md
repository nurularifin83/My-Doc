# 1. Create Project Folder
   
```
mkdir your-folder-name
cd  your-folder-name
```

# 2. Initialize Node.js Project
```
npm init -y
```

This will generate a ``package.json`` file.

# 3. Install Required Dependencies
We’ll install:
* Playwright (for browser automation)
* Mocha (test framework)
* Chai (assertion library)
  
```
npm install playwright mocha chai --save-dev
```

and also install the report
```
npm install --save-dev mochawesome
```

# 4. Create Folder Structure
```
mkdir tests
touch tests/mentoring.spec.js
touch README.md
```
Now your structure should look like:
```
your-folder-name/
├── node_modules/
├── tests/
│   └── mentoring.spec.js
├── package.json
└── README.md
```

#  5. Write Test Code
Write your code in ``tests/mentoring.spec.js``.

# 6. Update ``package.json`` Scripts
Edit your ``package.json`` file to add a test script:
```
{
  "name": "your project name",
  "version": "1.0.0",
  "description": "your description",
  "main": "index.js",
  "scripts": {
    "test": "mocha tests/*.spec.js --reporter mochawesome --timeout=60000"
  },
  "keywords": [
    "QA",
    "Automation",
    "test",
    "Playwright",
    "mocha"
  ],
  "author": "Your name",
  "license": "ISC",
  "devDependencies": {
    "chai": "^5.2.0",
    "mocha": "^11.5.0",
    "mochawesome": "^7.1.3",
    "playwright": "^1.52.0"
  }
}
```

# 7. Run Your Test
```
npm test
```
If everything is set correctly, Playwright will launch a browser and run the mentoring test scenarios.

#  ===================================================== #
## GitHub Actions (CI)
This repository is integrated with GitHub Actions. On every push or pull request to ``main`` branch, all tests will run automatically.

See ``.github/workflows/node.js.yml`` for configuration.

```
name: Playwright Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "20"

      - name: Install dependencies
        run: npm install

      - name: Install browser dependencies (Playwright)
        run: npx playwright install-deps

      - name: Install browsers
        run: npx playwright install

      - name: Run tests
        run: npx mocha tests/*.spec.js --reporter mochawesome --timeout=60000

      - name: Upload mochawesome report
        uses: actions/upload-artifact@v4
        with:
          name: mochawesome-report
          path: mochawesome-report
```
