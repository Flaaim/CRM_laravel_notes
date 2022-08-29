## Module Source
1. Создаем модуль Admin\Source --api --model --migration. Создаем папку Seeds, Policies, Requests. 
2. Создаем SourceRequest.
3. Создаем dir Services, внутри которой SourcesService.
4. В Api\UserController определяем переменную $services, конструктор. 
```
    public function __construct(SourcesService $sourceservice){
        $this->services = $sourceservice;
    }
 ```
 5. Формируем необходимые CRUD методы. index Возвращает ResponseService(true, 200, [], ['item'=>$this->services->getSources()]);
 6. В Services в классе SourceService определяем метод getSources();
 ```
 public function getSources(){
  return Source::all();
  }
  ```
  7. Описываем model Source. Определяем переменную $fillable = ['title']; 
  8. Описываем миграцию. Добавляю $table->string('title');. Запускаем миграцию. 
  9. В методе index() Контроллера добавляем $this->authorize('view', new Source()); 
  10. Создаем политику безопасности. php artisan make:policy путь до папки Policies. Копируем туда методы view and delete. Внутри данных методов прописываем функцию canDo() c пермиссионс 'Source_Access'. Добавляем данную привелегию в таблицу permissions.
  11. Добавляем связь Source and SourcePolicy в провайдере.
