## Файл ModularProvider.php
Создаем свой собственный провайдер ModularProvider.php командой php artisan make:provider ModularProvider.
Подключаем ModularProvider в config/app.php раздел providers \App\Providers\ModularProvider::class //По аналогии с другими подключениями.
В файле ModularProvider получаем все модули и путь к модулям из файла modular.php.
```
$modules = config('modular.modules');
$path = config('modular.path');
```
Проверяем есть ли что-то в переменной $modules
```
if($modules){
 
      Route::group([
        'prefix'=>''
      ], function()use($modules, $path){
         foreach($modules as $mod => $submodule){
    foreach($submodule as $sub){
        $relativePath = "/$mod/$sub";
           Route::middleware('web')
                ->group(function()use($mod, $sub, $relativePath, $path){
                      $this->getWebRoutes($mod, $sub, $relativePath, $path);
                  });
           Route::middleware('api')
            ->group(function()use($mod, $sub, $relativePath, $path){
                      $this->getApiRoutes($mod, $sub, $relativePath, $path);
                  });
    }
  }
}
```
Описываем методы getApiRoutes, getWebRoutes
```

 private function getApiRoutes($mod, $sub, $relativePath, $path){
   $routesPath = $path.$relativePath.'\Routes\api.php';
   if(file_exists($routesPath){
      Route::group([
        'prefix'=> strtolower($mod),
        'middleware'=> $this->getMiddleware($mod, 'api),
        ], function() use($mod, $sub, $routesPath){
           Route::namespace("App\Modules\\$mod\\$sub\Controllers")->group($routePath);
         }
   }
   
  private function getWebRoutes($mod, $sub, $relativePath, $path){
   $routesPath = $path.$relativePath.'\Routes\web.php';
   if(file_exists($routesPath){
      if($mod != config('modular.groupWithoutPrefix')){
             Route::group([
        'prefix'=> strtolower($mod),
        'middleware'=> $this->getMiddleware($mod),
        ], function() use($mod, $sub, $routesPath){
           Route::namespace("App\Modules\\$mod\\$sub\Controllers")->group($routePath);
         }
      } else {
        Route::namespace("App\Modules\\$mod\\$sub\Controller")->middleware($this->getMiddleware($mod))->group($routePath);
      }

   }
   ```
Определям функцию getMeddleware($mod, $key = 'web');
```
 private function getMiddleware($mod, $key = 'api){
     $middleware = [];
     $config = config(modular.groupWithoutPrefix
     if(isset($config[$mod])){
        if(array_key_exists($key, $config[$mod])){
         $middleware = array_merge($middleware, config[$mod][$key]);
        }
     }
     return $middleware;
  }
```
        
           
      



