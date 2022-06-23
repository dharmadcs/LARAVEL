@php
    

{{ LARAVEL guide }}

=============================================================================================
-routes : web.php

Route::get('/, function(){
    return view('welcome', [
        "title" => "homepage"
    ])
}')


=============================================================================================
-view

=============================================================================================
-blade engine

=============================================================================================
-model : eloquent(orm, memungkinkan table db memiliki model(class))

    *membuat model bisa dg tinker (php artisan tinker)
        Post::create([
            'title' -> 'judul'
        ])

    *ubah data 
        Post::where('title,'judul)'->update(['title'=>'juduuul'])
'
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
-collection : pembungkus array (agar bisa pakai function)
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
-controller : semua subController harus extends dari class utama (Controller.php)

route -> Route::get('/, [HomeController::class, 'index']');

class -> class HomeController extends Controller{
            public function index(){
                return view('home',[
                    'title' => 'home'
                ])
            }
}

=============================================================================================
-database migration : melacak perubahan pda database
    *kita bisa buat schema/table buat manual di dbms ckup buat nama db saja
    yang terhubung dg Model/user.php, UserFactory.php, user_table.php

    *3 ini berpasangan :
        app/model/users.php -> $fillable ,$guarded (massAssigment)
        database/migration/table_users.php ->row terhubung ke table database
        database/factories/Userfactory.php ->membuat data db faker()

    *data pda table ini sudah berbentuk collection()
    *

=============================================================================================
-database seeder : DatabaseSeeder.php
-factory faker : database/factories/userfactory.php

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

    *cara jakankan
     -saat migration : php artisan migrate:fresh --seed
     -saat biasa : php artisan db:seed


=============================================================================================
-n+1 problem : masalh muncul saat relationship melakukan looping berulang(lazy loadind)
        *cara atasinya tambah with(eager loading) pda controller/clousure(function pda route)
            "posts"=>Post::with(['user','category'])->latest();
        
        *gunakan load pda model binding(lazy eager loading)
            "posts"=>$user->posts->load('user','category');

        *buat eager loding sebaiknya pada kontroller/models
            protected $with = ['user','category'];


=============================================================================================
-searching

=============================================================================================
-pagination

=============================================================================================
-registration

=============================================================================================
-login

=============================================================================================
-middleware

=============================================================================================
-form validation

=============================================================================================
-CRUD (route resource)

=============================================================================================
-upload image

=============================================================================================
-autorization
