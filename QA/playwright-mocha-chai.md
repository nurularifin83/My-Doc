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
  "name": "dealls-qa-test",
  "version": "1.0.0",
  "description": "UI automation test for Dealls mentoring feature using Playwright + Mocha",
  "scripts": {
    "test": "mocha tests/*.spec.js --timeout=60000"
  },
  "devDependencies": {
    "chai": "^4.3.7",
    "mocha": "^10.2.0",
    "playwright": "^1.44.0"
  }
}
```

# 7. Run Your Test
```
npm test
```
If everything is set correctly, Playwright will launch a browser and run the mentoring test scenarios.

#  8. Done
