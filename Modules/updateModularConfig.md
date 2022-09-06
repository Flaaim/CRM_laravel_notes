## func undateModularConfig()
1. функция для автоматического добавления модуля в файл config modular.php. Пример Admin\User
```php
  private function updateModularConfig(){
    $group = explode('\\', str_replace('/', '\\', $this->argument('name)))[0]; //получаем Родительский модуль Admin
    $module = Str::studly(class_basename($this->argument('name'))); //получаем модуль User.
    
    $modular = $this->files->get(base_path('/config/modular.php')); //получаем текст файла modular.php
    $matches = [];
    preg_match("#'modules' => \[.*?'{$group}' => \[(.*?)\]#s", $modular, $matches); //
    
    if($count($matches) == 2){
        if(!preg_match("#{$module}#", $matches[1])){
            $parts = preg_split("#('modules' => \[.*?'{$group}' => \[)#s", $modular, 2, PREG_SPLIT_DELIM_CAPTURE);
            if(count($parts) == 3){
                    $configStr = $parts[0].$parts[1]."\r\n            '$module',".$parts[2];
                    $this->files->put(base_path('/config/modular.php'), $configStr);
                }
        }
    }
    
  }
  ```
