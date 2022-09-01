## Метод createModel()
1. описываем метод createModel();
```
private function createModel(){
  $model = Str::singular(Str::studly(class_basename($this->argument('name'))));
  $this->call('make:model', [
    'name' => "App\\Modules\\".trim($this->argument('name'))."\\Models\\".$model,
  ]);
  }
  ```
  2. описываем метод createMigration()
  ```
  private function createMigration() {
    $table = Str::plural(Str::sanke(class_basename($this->argument('name'))));
    $this->call(''make:migration', [
      'name' => "create_{$table}_table",
      '--create' => $table,
      '--path' => "App\\Modules\\".trim($this->argument('name'))."\\Migrations",
    ]);
    ```
