# 1. How to install TinyMCE selfhosted in laravel
>1. Install tinymce visit [this link](https://www.tiny.cloud/docs/tinymce/latest/npm-projects/)  or just type: ```npm install tinymce```

>2. Import TinyMCE Files
_In the ```resources/js/app.js``` file, add the following lines at the top to import the required JS and CSS files for TinyMCE: ```resources/js/app.js```_

```
import './bootstrap';
import 'tinymce/tinymce';
import 'tinymce/skins/ui/oxide/skin.min.css';
import 'tinymce/skins/content/default/content.min.css';
import 'tinymce/skins/content/default/content.css';
import 'tinymce/icons/default/icons';
import 'tinymce/themes/silver/theme';
import 'tinymce/models/dom/model';
```

>3. Add TinyMCE Init Code
_Now add the following init code to the bottom of your ```app.js```_

```
// .. After imports init TinyMCE ..
window.addEventListener('DOMContentLoaded', () => {
    tinymce.init({
        selector: 'textarea',

        /* TinyMCE configuration options */
        skin: false,
        content_css: false
    });
});
```

>4. Build Your Assets Using Vite
_Build the assets of your Laravel application using Vite by running the following command in your terminal:_

```
npm run build
```

>5. Add TinyMCE to Blade by Using the @vite Directive
_Now, in your Blade file, add the following line to use Vite for including the ```app.js``` file that was built using npm: ```@vite(['resources/js/app.js'])```._

***Xample:***

```
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Laracoding.com TinyMCE Example</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    @vite(['resources/js/app.js'])
</head>
<body>
    <h2>Laracoding.com - TinyMCE Example</h2>
    <form action="" method="get">
        <div class="mb-3">
            <label for="pwd">TinyMCE input:</label>
            <textarea class="tinyMce" name="user-bio"></textarea>
        </div>
        <button type="submit" class="btn btn-primary">Submit</button>
    </form>
</body>
</html>
```

> [!NOTE]
> Code in app.js after completed
```
<script>
    
import './bootstrap';
// Tinymce
import 'tinymce/tinymce';
import 'tinymce/skins/ui/oxide/skin.min.css';
import 'tinymce/skins/content/default/content.min.css';
import 'tinymce/skins/content/default/content.css';
import 'tinymce/icons/default/icons';
import 'tinymce/themes/silver/theme';
import 'tinymce/models/dom/model';

import Alpine from 'alpinejs';

window.Alpine = Alpine;

Alpine.start();

// TinyMCE
window.addEventListener('DOMContentLoaded', () => {
    tinymce.init({
        selector: 'textarea#editor',
        height: 680,
        skin: false,
        content_css: false
    });

    tinymce.init({
        selector: 'textarea#smalleditor',
        height: 350,
        skin: false,
        content_css: false
    });
});

</script>
```

Basic setup for [TinyMCE:](https://www.tiny.cloud/docs/tinymce/latest/basic-setup/)

# 2. Integrating TinyMCE In Laravel 10 Using Vite
> 1. ```npm install --save tinymce```
> 2. in ```vite.config.js``` add this code ```'resources/js/tinymce.js'``` in ```input:```
> 3. Create file ```tinymce.js``` in folder ```resources/js/``` and add the following code:

```
/* Import TinyMCE */
import tinymce from 'tinymce';

/* Default icons are required. After that, import custom icons if applicable */
import 'tinymce/icons/default/icons.min.js';

/* Required TinyMCE components */
import 'tinymce/themes/silver/theme.min.js';
import 'tinymce/models/dom/model.min.js';

/* Import a skin (can be a custom skin instead of the default) */
import 'tinymce/skins/ui/oxide/skin.js';

/* Import plugins */
import 'tinymce/plugins/advlist';
import 'tinymce/plugins/code';
import 'tinymce/plugins/emoticons';
import 'tinymce/plugins/emoticons/js/emojis';
import 'tinymce/plugins/link';
import 'tinymce/plugins/lists';
import 'tinymce/plugins/table';

/* content UI CSS is required */
import 'tinymce/skins/ui/oxide/content.js';

/* The default content CSS can be changed or replaced with appropriate CSS for the editor content. */
import 'tinymce/skins/content/default/content.js';

window.addEventListener('DOMContentLoaded', () => {
    tinymce.init({
        selector: 'textarea#tinymce',
        height: 500,
        plugins: 'advlist code emoticons link lists table',
        toolbar: 'bold italic | bullist numlist | link emoticons',
        skin_url: 'default',
        content_css: 'default',
    });
});
```
> 4. The last one is to link your TinyMCE with this code put on your header app: ```@vite(['resources/js/tinymce.js'])```
> 5. run this code ```npm run build```
> 6. Congrats you're done