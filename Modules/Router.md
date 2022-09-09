## Маршрутизатор
1. Создаем файл в директории /config modular.php. Данный файл будет возвращать пул настроек.
```
 return = [
  'path' => base_path() . "/App/Modules", //Полный путь к модулям.
  'base_namespace' => 'App/Modules',
  'groupWithoutPrefix' => 'Pub',
  'groupMiddleware' => [
      'Admin' => [
          'web' => ['auth'],
          'api' => ['auth:api]'
      ]
    ]
    
    'modules' => [
      'Admin' => [
        'User'
      ]
    ]
 ];
```
2. Создаем провайдер ModularProvider. php artisan make:provider ModularProvider. Регистрируем провайдер в config/app.php 
3. В ModularProvider описываем метод boot()
```
  $modules = config('modular.modules');
  $path = config('modular.path');
  
    if($modules) 
    {
      Route::group(['prefix' => ''], function () use($path, $modules){
          foreach($modules as $mod => $submodules){
            foreach($submodules as $sub){
              $relativePath = '/$mod/$sub';
              
                //копируем из файла RouteServiceProvider Route, заменяем данные
                 Route::middleware('web')
                ->group(function()use($sub, $mod, $path, $relativePath){
                  $this->getWebRoutes($sub, $mod, $path, $relativePath);
                });
                
                Route::middleware('api')
                ->prefix('api')
                ->group(function()use($sub, $mod, $path, $relativePath){
                  $this->getApiRoutes($sub, $mod, $path, $relativePath);
                });
                
            }
          }
      });
        $this->app['view']->addNamespace('Pub', base_path().'/resources/views/Pub');
        $this->app['view']->addNamespace('Admin', base_path().'/resources/views/Admin');
    }
    }
  
  
  ```
  ### getApiRoutes();
4. Описываем функцию getApiRoutes()
  ```
   private function getApiRoutes($sub, $mod, $path, $relativePath) 
   {
     $routesPath = $path.$relativePath."\\Routes\\api.php";
     if(file_exists($routesPath)){
        Route::group([
         'prefix' => strtolower($mod),
         'middleware' => $this->getMiddleware($mod, 'api'),
        ], function () use($mod, $sub, $routesPath) {
         Route::namespace("App\\Modules\\"$mod\\$sub\\"Controllers")->group($routesPath);
        });
     }
   }
```
5. Описываем функцию getWebRoutes()
```
 private function getWebRoutes($sub, $mod, $path, $relativePath)
 {
   $routesPath = $path.$relativePath."/Routes/web.php";
    if(file_exists($routesPath)){
      if($mod != config('modular.groupWithoutPrefix'))
      {
        Route::group([
        'prefix' => strtolower($mod),
        'middleware' => $this->getMiddleware($mod),
        ], function() use($mod, $sub, $routesPath) {
         Route::namespace("App\\Modules\\$mod\\$sub\\Controllers")->group($routesPath);
        });
      } else {
        Route::namespace("App\\Modules\\$mod\\$sub\\Controllers")->middleware($this->getMiddleware($mod))->group($routesPath);
      }
    }
    
 }
```
6. описываем функцию getMiddleware();
```
 private function getMiddleware($mod, $type = 'web') 
 {
   $middleware = [];
   $config = config('modular.groupMiddleware');
    if(isset($config[$mod])){
       if(array_key_exists($type, $config[$mod])){
         $middleware = array_merge($middleware, $config[$mod][$type]);
       }
    }
   
   return $middleware;
   
 }

```
   
 7. 
   
