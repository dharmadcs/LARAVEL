{{ LARAVEL starter kit guide by DCS }}

=============================================================================================
@routes : web.php

    Route::get('/, function(){
        return view('welcome', [
            "title" => "homepage"
        ])
    }')


=============================================================================================
@views

isi bagian html msing2
    header.php
    navbar.php
    footer.php

----------------------------------------------------------------
main.blade.php

    *file badan utama yng terdiri dari template diatas
    @include('dashboard.layouts.header')
    @include('dashboard.layouts.navbar')

    *panggil isi halaman berbeda
    @yield('container')

    @include('dashboard.layouts.footer')

----------------------------------------------------------------
index.php

    *panggil file main
    @extends('dashboard.layouts.main')

    @section('container')
    <...isi html...>
    @endsection

----------------------------------------------------------------

*tampilkan page active
    {{ Request::is ('dashboard/posts*') ? 'active' : '' }}

----------------------------------------------------------------

*pesan error pada form/bootstrap
    return redirect('/dashboard/posts')->with('success', 'New Post Added');

    @if(session()->has('success'))
        <div class="alert alert-success alert-dismissible fade show" role="alert">
            {{ session('success') }}
        <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
        </div>
    @endif

----------------------------------------------------------------

*js delete button
    onclick="return confirm('Are Yout Sure?')"

----------------------------------------------------------------

*cara mudah $++ iteration
    {{ $loop->iteration }}

----------------------------------------------------------------

*js img-preview

    @if ($post->image)
    <img src="{{ asset('storage/' . $post->image) }}" class="img-preview img-fluid mb-3 col-sm-8 d-block">      
    @else
    <img class="img-preview img-fluid mb-3 col-sm-8">
    @endif

    <input class="..... onchange="previewImage()">

    <script>
        function previewImage(){
            const image = document.querySelector('#image');
            const imgPreview = document.querySelector('.img-preview');

            imgPreview.style.display = 'block';

            const ofReader = new FileReader();
            ofReader.readAsDataURL(image.files[0]);

            ofReader.onload = function(oFREvent) {
                imgPreview.src = oFREvent.target.result;
            }
        }
    </script>

----------------------------------------------------------------

*hapus image pas update dan hapus post

    if($request->oldImage){
        Storage::delete($request->oldImage);
    }

----------------------------------------------------------------

*cara tampilkan post not found letakan ini dipaling bawah

    @if ($posts->count() > 0)
        isi tampilan.....
    @else
        echo "post not found"
    @endif

----------------------------------------------------------------

*cara tampilkan created_at 
    {{ $post->created_at->diffForHumans() }}

----------------------------------------------------------------

*cara submit dengan form (tambahkan @csrf)

    <form> @csrf </form>

=============================================================================================
@model : eloquent(orm, memungkinkan table db memiliki model(class))

    *membuat model bisa dg tinker (php artisan tinker)
        Post::create([
            'title' -> 'judul'
        ])

    *ubah data 
        Post::where('title,'judul)'->update(['title'=>'juduuul'])

    *membuat list berdasarkan slug
        -web.php : Route::get('/posts/{post:slug}' [PostController::class, 'show'])
        -PostContloller.php : 
            function show(Post $post) ->modle binding
            return view('post', [
                'post' => $post
            ])

    *untuk membuat relasi(hubungan) antar table dan model
        maka buatlah migration dan model(class)nya

    *cardinality relationship
        -one to one(belongsTo)->taro foreign_key(migration nama_table)
        -one to many(hasMany)

        contoh : 1 post dimiliki 1 user(belongsTo)->$post_id(foreign_key)
                 1 user memiliki banyak post(hasMany)

    *cara membuat hubungan antar model(post dan category)
        pada tiap model buat method yang namnya sama dg nama model pasangan
        1. models/post.php
            function category(){
                return $this->belongsTo(Category::class);
            }

        2. models/category.php
            function post(){
                return $this->hasMany(Post::class);
            }

        3. panggil di route web.php (panggil model yg memiliki banyak isi)
            contoh: 1 category memiliki banyak post
            Route::get('/categories/{category:slug}', 
                function(Category $category,
                    return view('category',[
                        'title'=>'judul'
                    ])))

=============================================================================================
@collection : pembungkus array (agar bisa pakai function)
    ex. $data = collect(['spatu', baju]);
        return $data = first(index, key)

    *memanggil collection pda <html> ada 2 notasi
    1. {{ $user['id'] }} ->sbg array
    2. {{ $user->id }}   ->sbg object(gunakan ini)

    *memanggil collection pda method
        User::all() , find(), pluck()

    *jika ada <p> pada body <html>
        {!! $post->body !!}


    self:: -> memanggil property static
    static:: -> memanggil method static


=============================================================================================
@controller : semua subController harus extends dari class utama (Controller.php)

    route -> Route::get('/, [HomeController::class, 'index']');

    class -> class HomeController extends Controller{
                public function index(){
                    return view('home',[
                        'title' => 'home'
                    ])
                }
    }

=============================================================================================
@migration : melacak perubahan pda database

    *kita bisa buat schema/table buat manual di dbms ckup buat nama db saja
    yang terhubung dg Model/user.php, UserFactory.php, user_table.php

    *3 ini berpasangan :
        app/model/users.php -> $fillable ,$guarded (massAssigment)
        database/migration/table_users.php ->row terhubung ke table database
        database/factories/Userfactory.php ->membuat data db faker()

    *data pda table ini sudah berbentuk collection()

=============================================================================================
@database seeder : DatabaseSeeder.php
@factory faker : database/factories/userfactory.php

    *membuat manual
        function run()
            User::create([
                'name' => 'dharma'
            ])

    *membuat faker
    User::factory(3)->create();

        setting di UserFactory.php
            public function definition(){
            return [
                'name' => $this->faker->name(),
                'username' => $this->faker->unique()->userName(),
                'email' => $this->faker->unique()->safeEmail(),
                'password' => '12345',
                'remember_token' => Str::random(10),
            ];
        }

    *cara jalankan
     -saat migration : php artisan migrate:fresh --seed
     -saat biasa : php artisan db:seed


=============================================================================================
@n+1 problem : masalh muncul saat relationship melakukan looping berulang(lazy loading)

        *cara atasinya tambah with(eager loading) pda controller/clousure(function pda route)
            "posts"=>Post::with(['user','category'])->latest();
        
        *gunakan load pda model binding(lazy eager loading)
            "posts"=>$user->posts->load('user','category');

        *buat eager loding sebaiknya pada kontroller/models
            protected $with = ['user','category'];


=============================================================================================
@searching

    *untuk tangkap | ?search= | pada get/url
    tepatnya pda method yg me return view dari post(PostController)

    $posts = Post::latest();

        if(request('search')){
            $posts->where('title', 'like', '%' . request('search') . '%')
            ->orWhere('body', 'like', '%' . request('search') . '%');
        }

        return view('posts', [
            "title" => "All Posts",
            "posts" => $posts->paginate(7)
        ]);
    

=============================================================================================
@pagination

    *return pda view utama
        return view('posts', [
            "title" => "All Posts",
            "posts" => $posts->paginate(10)->withQueryString();
                ]);

    *buat tombol fisik buat justify center
        <div class="d=flex justify-content-center">
            {{ $post->links() }}
        </div>


    *default tailwind ubah ke bootstrap
        App\Providers\AppServiceProviders

        public function boot(){
            paginator::useBootstrap(); ->tambahkan
        }

=============================================================================================
@registration
    *pada RegisterController
    public function store(Request $request){

    $validatedData = $request->validate([
             'name' => 'required|min:3|max:255',
             'username' => 'required|min:3|max:255|unique:users',
             'email' => 'required|email:dns|unique:users',
             'password' => 'required|min:3'
         ]);

        $validatedData['password'] = bcrypt($validatedData['password']);

        User::create($validatedData);

        return redirect('/login')->with('success', 'Success !');
    }

=============================================================================================
@form validation
    
    *tambahkan tiap form <input> dan <label>
        <input class="@error('email') is-invalid @enderror"
            value="{{ old('email') }}" >
    
        <label for="email">Email address</label>
        @error('email')
        <div class="invalid-feedback">
            {{ $message }}
        </div>
        @enderror

=============================================================================================
@login

    *pada LoginController.php

    public function index(){
        return view('login.index', [
            'title' => 'Login'
        ]);
    }

    public function authenticate(Request $request){
        $credentials = $request->validate([
            'email' => 'required|email:dns',
            'password' => 'required'
        ]);

        if(Auth::attempt($credentials)){
            $request->session()->regenerate();
            return redirect()->intended('/dashboard');
        }

        return back()->with('errLogin', 'Email or Password is Wrong');

    }
 
    *tambahkan namespace ini
        Illuminate\Support\Facades\Auth;

    *ganti di app/providers/RouteServiceProvider.php
        public const HOME = '/'

    *tambah juga middleware apda route web.php
        Route::get('login'.....)->middleware('guest');
                                ->middleware('auth');

    *lalu agar login di navbar hilang saat sudah login
        @auth
            ->jika sudah login
        @else
            ->jika belum login
        @endauth

    *pda navbar kita bisa pnggil username
        {{ auth()->user()->name }}

=============================================================================================
@logout

    *tombol logout tidak bagus pake href, gunakan form

        <form action="/logout" method="post">
            @csrf
            <button type="submit" .....>
        </form>

    *pada LoginController.php

        public function logout(Request $request){
            $request->session()->invalidate();
            $request->session()->regenerateToken();
            
            return redirect('/');
        }


=============================================================================================
@CRUD (route resource)

    Controller ada Basic, Resorce, API
    type resource memungkinkan kita CRUD lbh mudah
        - php artisan route:list
    Untuk liat route resoure yg tersedia

    Tulis pada route yg tersedia agar dialihkan ke method pda controllernya
    method utama tetap get dan post, caranya timpa didalam <form> @method(get,post,put,delete)

    form metod  -index : view utama
                -create : view halaman create-><form> @method('get/head')
                -store : validasi data dan create function
                -show : view detail item
                -edit : view halaman edit-><form> @method('put') @csrf
                -update : validasi data dan function update
                -destroy : hapus data-><form> @method('delete') @csrf
                -lainnya : bisa juga tambah method lain

    *Penulisan route web.php
        Route::resource('/dashboard/post' .........)
            link sesuaikan dg method (lihat route:list)

=============================================================================================
@upload image

    *atur agar form bisa upload file
    <form enctype="multipart/form-data">
    
    *ubah peyimpanan file yang diupload
        config/filesystem.php
        'default' => env('FILESYSTEM_DRIVER', 'public')

        rubah ke public agar dibsa dilihat user
        bisa juga ke .env tambahkan baris
            FILESYSTEM_DRIVER = public

    *hubungkan storage/public dg public yg untuk user
        php artisan storage:link
    
=============================================================================================
@autorization

