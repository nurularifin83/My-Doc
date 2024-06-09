# How To Use [Validate JQuery](https://jqueryvalidation.org/) with _Form action and type button is submit_ in your Laravel
> 1. Download Plugin and linked to your footer. Look like below
```
<script src="{{ asset('backend') }}/assets/js/jquery.validate.js"></script>
</body>
</html>
```
> 2. After that, create a blade file and put the following code:
```
<form id="myForm" action="{{ route('user.question') }}" method="POST" class="pt-4">
    @csrf

    <input type="hidden" name="course_id" value="{{ $course->course_id }}">
    <input type="hidden" name="instructor_id" value="{{ $course->instructor_id }}">

    <div class="custom-control-wrap">
        <div class="custom-control custom-radio mb-3 pl-0">
            <input type="text" name="subject" id="subject" class="form-control form--control pl-3" placeholder="Title or summary" value="{{ old('subject') }}">
        </div>

        <div class="custom-control custom-radio mb-3 pl-0">
            <textarea class="form-control form--control pl-3" name="question" id="question" rows="4" placeholder="Details (optional)">{{ old('question') }}</textarea>
        </div>
        
    </div>
    <div class="btn-box text-center">
        <button type="submit" class="btn theme-btn w-100" id="submit-question">Submit Question <i class="la la-arrow-right icon ml-1"></i></button>
    </div>

</form>
```
> 3. Add javascript code to make it work
```
 <script type="text/javascript">
    $(document).ready(function() {
    
        $('#myForm').validate({
            rules: {
                subject: { _//Your textfield name_  
                    required : true,
                }
            },
            messages :{
                subject: {
                    required : 'Please provide a title or summary.',
                }
            },
            errorElement : 'span', 
            errorPlacement: function (error,element) {
                error.addClass('invalid-feedback');
                element.closest('.mb-3').append(error);
            },
            highlight : function(element, errorClass, validClass){
                $(element).addClass('is-invalid');
            },
            unhighlight : function(element, errorClass, validClass){
                $(element).removeClass('is-invalid');
            },
        })
    })
</script>
```
> 4. Done. You're good to go. For more [visit this link](https://jqueryvalidation.org/documentation/)

# How to use [Validate JQuery](https://jqueryvalidation.org/) without _form action, method, and type button submit_ in Laravel
> 1. Download Plugin and linked to your footer. Look like below
```
<script src="{{ asset('backend') }}/assets/js/jquery.validate.js"></script>
</body>
</html>
```
> 2. After that, create a blade file and put the following code:
```
<form id="myForm" class="pt-4">

    <input type="hidden" name="course_id" value="{{ $course->course_id }}">
    <input type="hidden" name="instructor_id" value="{{ $course->instructor_id }}">

    <div class="custom-control-wrap">
        <div class="custom-control custom-radio mb-3 pl-0">
            <input type="text" name="subject" id="subject" class="form-control form--control pl-3" placeholder="Title or summary" value="{{ old('subject') }}">
        </div>

        <div class="custom-control custom-radio mb-3 pl-0">
            <textarea class="form-control form--control pl-3" name="question" id="question" rows="4" placeholder="Details (optional)">{{ old('question') }}</textarea>
        </div>
    </div>
    <div class="btn-box text-center">
        <button type="button" class="btn theme-btn w-100" id="submit-question">Submit Question <i class="la la-arrow-right icon ml-1"></i></button>
    </div>

</form>
```
> 3. Add javascript code to make it work
```
 <script type="text/javascript">
    $(document).ready(function() {
    
        $('#myForm').validate({
            rules: {
                subject: { _//Your textfield name_  
                    required : true,
                }
            },
            messages :{
                subject: {
                    required : 'Please provide a title or summary.',
                }
            },
            errorElement : 'span', 
            errorPlacement: function (error,element) {
                error.addClass('invalid-feedback');
                element.closest('.mb-3').append(error);
            },
            highlight : function(element, errorClass, validClass){
                $(element).addClass('is-invalid');
            },
            unhighlight : function(element, errorClass, validClass){
                $(element).removeClass('is-invalid');
            },
        })
    })

    $('#submit-question').on('click', function() {
        $('#myForm').valid();
    })
</script>
```
> 4. Done. You're good to go. For more [visit this link](https://jqueryvalidation.org/documentation/)