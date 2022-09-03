## createWebRoutes();
```
  private function createWebRoutes($controller, $modelName){
      $routesPath = $this->getWebRoutes($this->argument('name'));
      
      if($this->alreadyExists(routesPath)){
          $this->error('Routes already exists');
      } else {
        $this->makeDirectory($routesPath);
        
        $stub = $this->files->get(base_path('resources/stubs/route.web.stub'));
        $stub = str_replace(
                [
                    'DummyArgument',
                    'DummyClass',
                    'DummyRoutePrefix',
                    'DummyModelVariable',
                ],
                [
                    trim(str_replace("/", "\\", $this->argument('name'))),
                    $controller."Controller",
                    Str::plural(Str::snake(lcfirst($modelName))),
                    Str::snake(lcfirst($modelName)),

                ], 
          $stub
        );
        $this->files->put($routePath, $stub);
        $this->info("Routes create succesfully");
      }
    }
    
    
    
 ```
 ### getWebRoutes($name);
 ```
 private function getWebRoutes($name){
    return $this->laravel['path']."/Modules/".str_replace('\\', '/', $name)."/Routes/web.php";

 }
 ```
 ## Routes.stub.web 
 ```
 use Illuminate\Support\Facades\Route;
use App\Modules\DummyArgument\Controllers\DummyClass;

Route::group(['prefix'=> 'DummyRoutePrefix', 'middleware' => []], function(){
    Route::get('/', [DummyClass::class, 'index'])->name('DummyRoutePrefix.index');
    Route::get('/create', [DummyClass::class, 'create'])->name('DummyRoutePrefix.create');
    Route::post('/', [DummyClass::class, 'store'])->name('DummyRoutePrefix.create');
    Route::get('/edit/{DummyModelVariable}', [DummyClass::class, 'edit'])->name('DummyRoutePrefix.edit');
    Route::put('/{DummyModelVariable}', [DummyClass::class, 'update'])->name('DummyRoutePrefix.update');
    Route::delete('/{DummyModelVariable}', [DummyClass::class, 'destroy'])->name('DummyRoutePrefix.destroy');
});
 ```
