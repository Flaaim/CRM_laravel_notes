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
    }
  }
}
        
        
           
      



