# How to use [sweetalert2](https://sweetalert2.github.io/#download) on Laravel as old-school alternative
> 1. Installation. Put this code on your header ```<script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>```.
> 2. Put this code in your ```script.js```:

```
// Confirm delete message
$(function(){
    $(document).on('click','#delete',function(e){
        e.preventDefault();
        var link = $(this).attr("href");
        const swalWithBootstrapButtons = Swal.mixin({
            customClass: {
              confirmButton: "btn btn-primary",
              cancelButton: "btn btn-danger ms-3",
            },
            buttonsStyling: false
          });
          swalWithBootstrapButtons.fire({
            title: "Are you sure?",
            text: "You won't be able to revert this!",
            icon: "warning",
            showCancelButton: true,
            confirmButtonText: "Yes, delete it!",
            cancelButtonText: "No, cancel!",
          }).then((result) => {
            if (result.isConfirmed) {
                window.location.href = link
                swalWithBootstrapButtons.fire({
                    title: "Deleted!",
                    text: "Your data has been deleted.",
                    icon: "success"
                });
            }
        });
    });
    
});
```

> 3. In blade on _delete button_ put the following code ```id="delete"```. Look like this: ```<a href="{{ route('delete.course',$item->id) }}" class="btn btn-danger" id="delete"></a>```
> 4. Done. You're good to go. For more [visit this link](https://sweetalert2.github.io/#usage)

# How to use [sweetalert2](https://sweetalert2.github.io/#download) on Laravel use NPM
1. Installation. Run this code ```npm install sweetalert2```
2. Create a file named ```script.js``` in folder _resources/js/_. And then put this code:

```
import Swal from 'sweetalert2'

// To make Swal can active averywhere:
window.Swal = Swal;

$(function(){
    $(document).on('click','#delete',function(e){
        e.preventDefault();
        var link = $(this).attr("href");
        const swalWithBootstrapButtons = Swal.mixin({
            customClass: {
              confirmButton: "btn btn-primary",
              cancelButton: "btn btn-danger ms-3",
            },
            buttonsStyling: false
          });
          swalWithBootstrapButtons.fire({
            title: "Are you sure?",
            text: "You won't be able to revert this!",
            icon: "warning",
            showCancelButton: true,
            confirmButtonText: "Yes, delete it!",
            cancelButtonText: "No, cancel!",
          }).then((result) => {
            if (result.isConfirmed) {
                window.location.href = link
                swalWithBootstrapButtons.fire({
                    title: "Deleted!",
                    text: "Your data has been deleted.",
                    icon: "success"
                });
            }
        });
    });  
});
```

3. Add this code ```'resources/js/script.js'``` on your ```vite.config.js```.
4. Run this code ```npm run build```.
5. Add this code ```@vite(['resources/js/script.js'])``` on your footer.
6. The last one is to add ```id="delete"``` on your blade delete button. Button after adding _ID_ ```<a href="{{ route('delete.course',$item->id) }}" class="btn btn-danger" id="delete"></a>```.
7. Done. You're good to go. For more [visit this link](https://sweetalert2.github.io/#usage)