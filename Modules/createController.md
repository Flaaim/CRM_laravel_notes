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
    
 ```
 Прописываем use
 ```
use Illuminate\Filesystem\Filesystem;
```
