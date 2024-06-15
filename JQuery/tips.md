### How to reload a div without reloading the entire page?
> 1. ```$("#mydiv").load(location.href + " #mydiv");```.
> 2. ```$('#mydiv').load(location.href + " #mydiv>*","");```.

### For the best practice always use this for button click $.ajax ```$(document).on("click", ".status-toggle", function() {})```.