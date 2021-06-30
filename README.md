# Laravel Datatables Server Side Processing

To Integrate in your project please follow below steps.

## Step:1 Laravel Application Setup

Install Laravel in your system. If you haven't laravel application setup then run below command to install. 

```
composer create-project --prefer-dist laravel/laravel blog
```

## Step:2 Install Yajra Datatables

After Installation, we will install yajra datatable package,:

```
composer require yajra/laravel-datatables-oracle
```

Set service providers and alias on `config/app.php`:
```
.....
'providers' => [
	....
	Yajra\DataTables\DataTablesServiceProvider::class,
]
'aliases' => [
	....
	'DataTables' => Yajra\DataTables\Facades\DataTables::class,
]
....
```

## Step:3 Create Dummy Records

First, Run Migration to create tables and the create record via tinker

Run Following commands,
```
php artisan migrate
```
```
php artisan tinker

>>> App\Models\User::factory()->count(25)->create();
```


## Step:4 Set Route

On `routes/web.php`, Add Route

```
Route::get('/users', [App\Http\Controllers\UserController::class, 'index'])->name('users.index');
```


## Step:5 Create Controller

Create Controller called  `UserController` and then add following code
```
use DataTables;


    public function index(Request $request)
    {
        $users = User::latest()->get();

        if (request()->ajax()) {

            return Datatables::of($users)
                ->addIndexColumn()

                ->addColumn('action', function($row){

//                    $btn = '<a href="/user/show/'.$row->id.'"  class="edit btn btn-primary btn-sm">View</a>';
                    $btn = '<a href="#"  class="edit btn btn-primary btn-sm">View</a>';

                    return $btn;
                })
                ->rawColumns(['action'])
                ->make(true);
        }
        return view('users');
    }
```



## Step:4 Create View

Create view inside `resources/views`, called `users.blade.php`

```

@extends('layouts.app')

@section('head')
    <link href="https://cdn.datatables.net/1.10.24/css/dataTables.bootstrap4.min.css" rel="stylesheet">
@endsection

@section('content')
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header">Users</div>


                    <div class="card-body">
                        <table id="example" class="table table-striped table-bordered" style="width:100%">
                            <thead>
                            <tr>
                                <th scope="col">#</th>
                                <th scope="col">Name</th>
                                <th scope="col">Email</th>
                                <th scope="col">Phone</th>
                                <th scope="col">Action</th>
                            </tr>
                            </thead>
                            <tbody>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
@endsection

@section('js')

    <!-- Data table -->
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.0/jquery.validate.js"></script>
    <script src="https://cdn.datatables.net/1.10.16/js/jquery.dataTables.min.js"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js"></script>
    <script src="https://cdn.datatables.net/1.10.19/js/dataTables.bootstrap4.min.js"></script>

    <script>

        $(document).ready(function() {

            $('#example').DataTable({
                processing: true,
                serverSide: true,
                ajax: {
                    url: "{{ route('users.index') }}",
                },
                columns: [
                    {data: 'id', name: 'id'},
                    {data: 'name', name: 'name'},
                    {data: 'email', name: 'email'},
                    {data: 'phone', name: 'phone'},
                    {data: 'action', name: 'action', orderable: false, searchable: false},
                ]
            });

        });
    </script>
@endsection

```
on `layouts/app.blade.php`
Please make sure that you should remove `<script src="{{ asset('/js/app.js') }}"></script>` script from head tag because it will duplicate the Jquery Plugin
and at the bottom you should yield javascript section like this.

```
@yield('js')
```
and on Head tag you should yield head section like this,
```
<head>
    @yield('head')
</head>
```

And Last, start the application: by running,

```
php artisan serve
```
and visit [http://127.0.0.1:8000/](http://127.0.0.1:8000/) to see the application in action.
