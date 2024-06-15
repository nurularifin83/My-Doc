# Edit data with javascript in Laravel
> 1. Add the following code to your ```routes/web.php```:
```
Route::get('/edit/blog/category/{id}','EditBlogCategory');
```
> 2. Add the following code to your Controller:
```
public function EditBlogCategory($id){

    $categories = BlogCategory::find($id);
    return response()->json($categories);
}
```
> 3. Add the following code on your blade
```
<!-- Edit Modal -->
<div class="modal fade" id="category" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">Edit Blog Category</h5>
                
            </div>
            <div class="modal-body"> 
        <form action="{{ route('blog.category.update') }}" method="post">
        @csrf

        <input type="hidden" name="cat_id" id="cat_id">

        <div class="form-group col-md-12">
            <label for="input1" class="form-label">Blog Category Name</label>
            <input type="text" name="category_name" class="form-control" id="cat">
        </div>
                
            </div>
            <div class="modal-footer"> 
                <button type="submit" class="btn btn-primary">Save changes</button>
            </div>
        </form>
        </div>
    </div>
</div>
```
> 4. Add the following JQuery code in your blade below your html code
```
<script>
function categoryEdit(id){
    $.ajax({
        type: 'GET',
        url: '/edit/blog/category/'+id,
        dataType: 'json',
        success:function(data){
            $('#cat').val(data.category_name);
            $('#cat_id').val(data.id);

        }
    })

}
</script>
```
> 5. Call your ```ajax function``` and put on Edit button like the following code:
```
@foreach ($category as $key=> $item) 
<tr>
    <td>{{ $key+1 }}</td>
    <td> {{ $item->category_name }}  </td>
    <td>{{ $item->category_slug }}</td> 
    <td>
        <button type="button" class="btn btn-info px-5" data-bs-toggle="modal" data-bs-target="#category" id="{{ $item->id }}" onclick="categoryEdit(this.id)">Edit</button>                
    </td>
</tr>
@endforeach
```
> 5. Done. You're good to go.