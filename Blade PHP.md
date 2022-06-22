#tampilkan page active
{{ Request::is ('dashboard/posts*') ? 'active' : '' }}


#pesan error pada form/bootstrap
-return redirect('/dashboard/posts')->with('success', 'New Post Added');

-@if(session()->has('success'))
	<div class="alert alert-success alert-dismissible fade show" role="alert">
  	{{ session('success') }}
  	<button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
	</div>
 @endif


#js delete button
onclick="return confirm('Are Yout Sure?')"


#cara mudah $++ iteration
{{ $loop->iteration }}


#js img-preview

-  @if ($post->image)
   <img src="{{ asset('storage/' . $post->image) }}" class="img-preview img-fluid mb-3 col-sm-8 d-block">      
   @else
   <img class="img-preview img-fluid mb-3 col-sm-8">
   @endif

-  <input class="..... onchange="previewImage()">

- <script>
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


#hapus image pas update dan hapus post

if($request->oldImage){
     Storage::delete($request->oldImage);
}

#cara lihat route resource
	php artisan route:list

#


