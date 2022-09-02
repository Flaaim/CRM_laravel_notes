## Метод createController()
1. Описываем метод createController()
```
private function createController() {
  $controller = Str::studly(class_basename($this->argument('name)));
  $modelName = Str::singular(Str::studly(class_basename($this->argument('name'))));
  
  $path = $this->getControllerPath($this->argument('name));
  
  if($this->alreadyExists($path)){
      $this->error("Controller already exists");
    } else {
      $this->makeDirectory($path);
      
      $stubs = $this->files->get(base_path('/resources/stubs/controller.model.api.stub');
      $stubs = str_replace(                [
                    'DummyNamespace',
                    'DummyRootNamespace',
                    'DummyClass',
                    'DummyArgument',
                    'DummyModel',
                    'model',
                ],
                [
                    "App\\Modules\\".trim(str_replace("/", "\\", $this->argument('name')))."\\Controllers",
                    $this->laravel->getNamespace(),
                    $controller."Controller",
                    trim(str_replace("/", "\\", $this->argument('name'))),
                    $modelName,
                    Str::lower($modelName),
                ],
                $stub);
      $this->files->put($path, $stubs);
    }
  
  
  }
  
  
  
  
  private function getControllerPath($argument){
    $controller = Str::studly(class_basename($argument));
    return $this->laravel['path']"/Modules/".str_replace('\\', '/', $argument)."/Controllers/".$controller."Controller.php";
  }
  
  private function alreadyExists($path){
      $this->files->exists($path);
  }
  
  private function makeDirectory($path) {
    if(!$this->files->isDirectory(dirname($path))){
      $this->files->makeDirectory(dirname($path), '0777', true, true; 
      }
      return $path
  }
  ```
  Добавляем в class ModuleMake 
  ```
  private $files;
  
  public function __construct(Filesystem $filesystem){
    parent::__construct()
    $this->files =$filesystem;
    }
    
 ```
Подключаем Filesystem
 ```
use Illuminate\Filesystem\Filesystem;
```

## StubController
1. controller.model.api.stub

```
<?php

namespace DummyNamespace;

use Illuminate\Http\Request;
use DummyRootNamespaceHttp\Controllers\Controller;
use App\Modules\DummyArgument\Models\DummyModel;

class DummyClass extends Controller {

    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        //
    }

    /**
     * Show the form for creating a new resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function create()
    {
        //
    }

    /**
     * Store a newly created resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function store(Request $request, DummyModel $model)
    {
        //
    }

    /**
     * Display the specified resource.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function show(DummyModel $model)
    {
        //
    }

    /**
     * Show the form for editing the specified resource.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function edit(DummyModel $model)
    {
        //
    }

    /**
     * Update the specified resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function update(Request $request, DummyModel $model)
    {
        //
    }

    /**
     * Remove the specified resource from storage.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function destroy(DummyModel $model)
    {
        //
    }
}
```
