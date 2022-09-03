## function createView()
```
private function createView(){
    $viewPaths = $this->getViewsPath($this->argument('name'));
    
    foreach($viewPaths as $path){
        if($this->alreadyExists($path)){
            $this->error("View already exists");
          } else {
            $this->makeDirectory($path);
            $stub = $this->files->get(base_path('resources/stubs/view.stub'));
            $stub = str_replace([], [], $stub);
            $this->files->put($path, $stub);
            $this->info("View created succesfully");
          }
    }
    
}

```

### function getViewsPath($name)
```
private function getViewsPath($name){
    $views = collect([
    'index',
    'create',
    'edit',
    'show',
    ]);
    $viewsPath = $views->map(function($item) use($name)){
      return base_path("resources/views".str_replace('\\', '/', $name)."/$item."blade.php");
    }
    return $viewsPath;
  }
