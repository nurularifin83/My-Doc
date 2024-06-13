# Error: _Top-level await is not available in the configured target environment ("chrome87", "edge88", "es2020", "firefox78", "safari14" + 2 overrides)_
## How to fixed:
> 1. Add this code in ```vite.config.js```
```
esbuild: {
    /**
        * Prevents ESBuild to throw when using a feature not supported by the
        * list of supported browsers coming from the `browserslist` file.
        */
    supported: {
        'top-level-await': true,
    },
},
```
Final code after added above code
```
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            input: [
                'resources/css/app.css',
                'resources/js/app.js',
                'resources/js/tinymce.js',
                'resources/js/script.js',
                'resources/js/instructor/script.js',
            ],
            refresh: true,
        }),
    ],
    esbuild: {
        /**
         * Prevents ESBuild to throw when using a feature not supported by the
         * list of supported browsers coming from the `browserslist` file.
         */
        supported: {
            'top-level-await': true,
        },
    },
});
```
> 2. Done. You're good to go.