# How to get TinyMCE value textarea
> Try:
```
var editor='content_textarea';
var content =tinyMCE.activeEditor.getContent();
alert(content);
```
Or if you don't like that, you can do:

```
tinyMCE.triggerSave();
var content =$('#content_textarea').val();
alert(content);
```