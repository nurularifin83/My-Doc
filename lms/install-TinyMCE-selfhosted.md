> 1. Install tinymce visit [this link](https://www.tiny.cloud/docs/tinymce/latest/npm-projects/)  or just type: ```npm install tinymce```

> 2. Import TinyMCE Files
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

> 3. Add TinyMCE Init Code
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

> 4. Build Your Assets Using Vite
_Build the assets of your Laravel application using Vite by running the following command in your terminal:_

```npm run build```

> 5. Add TinyMCE to Blade by Using the @vite Directive
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