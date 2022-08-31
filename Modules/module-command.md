## Создание команды ModuleMake.
1. командой php artisan make:command ModuleMake, создаем файл в App/Console/Commands/ для формирования модульной структуры проекта. 
2. В файле ModuleMake определяем сигнатуру protected $signature = 'make:module {name} {--flag}';
```
Flags:
{--all}
{--migration}
{--model}
{--controller}
{--api}
{--view}
```
3. Описываем метод handle();
```
//Определяем какие парметры приходят
  if($this->option('--all')){
    $this->input->setOption('migration', true);
    $this->input->setOption('model', true);
    $this->input->setOption('controller', true);
    $this->input->setOption('api', true);
    $this->input->setOption('view', true);
   }
   //Используя условные операторы проверяем есть ли в флаг в команде.
  
  if($this->option('migration')){
    $this->createMigration();
   }
  if($this->option('model')){
    $this->createMigration();
   }
  if($this->option('controller')){
    $this->createMigration();
   }
  if($this->option('api')){
    $this->createMigration();
   }
  if($this->option('view')){
    $this->createMigration();
   }
```

