# Add data post with _Fetch_ Javascript.

> Your Route
```
Route::controller(CourseController::class)->group(function(){
    Route::post('/save-lecture/','SaveLecture')->name('save-lecture');
});    
```
> Your HTML Code
```
<div class="container">
    <h6>Lecture Title </h6>
    <input type="text" class="form-control" placeholder="Enter Lecture Title">
    <textarea class="form-control mt-2" placeholder="Enter Lecture Content" rows="10" id="smalleditor"></textarea>
    <h6 class="mt-3">Add Video Url</h6>
    <input type="text" name="url" class="form-control" placeholder="Add URL">

    <button class="btn btn-primary mt-3" onclick="saveLecture('${courseId}',${sectionId},'${containerId}')" >Save Lecture</button>
    <button class="btn btn-secondary mt-3" onclick="hideLectureContainer('${containerId}')">Cancel</button>
</div>
```

> Your Fetch javascript code
```
<script>
    const saveLecture = (courseId, sectionId, containerId) => {
        const lectureContainer = document.getElementById(containerId);
        const lectureTitle = lectureContainer.querySelector('input[type="text"]').value;
        const lectureContent = lectureContainer.querySelector('textarea').value;
        const lectureUrl = lectureContainer.querySelector('input[name="url"]').value;

        // Request option
        const requestOptions = {
            method: 'POST',
            mode: "cors",
            cache: "no-cache",
            credentials: "same-origin",
            headers: {
                'Content-Type': 'application/json',
                'X-CSRF-TOKEN': '{{ csrf_token() }}',
            },
            body: JSON.stringify({
                course_id: courseId,
                section_id: sectionId,
                lecture_title: lectureTitle,
                lecture_url: lectureUrl,
                content: lectureContent,
            }),
        };
        
        fetch('/save-lecture', requestOptions)
        .then(response => {
            if(!response.ok){
                throw new Error('Network response was not ok.');
            }
            return response.json()
        })
        .then(data => {
            console.log(data);

            lectureContainer.style.display = 'none';
            location.reload();
            
            // Start Message 
            const Toast = Swal.mixin({
                toast: true,
                position: 'top-end',
                icon: 'success', 
                showConfirmButton: false,
                timer: 6000 
            })
            if ($.isEmptyObject(data.error)) {  
                Toast.fire({
                type: 'success',
                title: data.success, 
                })
            }else{
                Toast.fire({
                    type: 'error',
                    title: data.error, 
                    })
                }
        })
        .catch(error => {
            console.error('There was a problem with the fetch operation: '+error);
        });
    }
</script>
```

> Your controller method

```
public function SaveLecture(Request $request)
{
    $lecture = new CourseLecture();
    $lecture->course_id = $request->course_id;
    $lecture->section_id = $request->section_id;
    $lecture->lecture_title = $request->lecture_title;
    $lecture->url = $request->lecture_url;
    $lecture->content = $request->content;
    $lecture->save();

    return response()->json(['success' => 'Saved.']);
}
```