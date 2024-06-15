# How to set width for Datatable with CSS
> 1. Here your HTML table:
```
<div class="table-responsive">
    <table id="example" class="table table-striped table-bordered" style="width:100%">
        <thead>
            <tr>
                <th>#</th>
                <th>Course Name </th>
                <th>Username </th> 
                <th>Comment </th> 
            </tr>
        </thead>
        <tbody>
            
            @foreach ($review as $key => $item) 
            <tr>
                <td>{{ $key+1 }}</td>
                <td>{{ $item['course']['course_name'] }}</td> 
                <td>{{ $item['user']['name'] }}</td> 
                <td>{{ $item->comment }}</td> 
            </tr>
            @endforeach
                
        </tbody>
            
    </table>
</div>
```
> 2. Put the following CSS to set width for your Datatable:
```
<style type="text/css">
table#example th:nth-child(4) {
    width: 100%;
    max-width: 50%;
    word-break: break-all;
    white-space: pre-line;
}

table#example td:nth-child(4){
    width: 100%;
    max-width: 50%;
    word-break: break-all;
    white-space: pre-line;
}
</style>
```
> 3. Done. You're good to go.
