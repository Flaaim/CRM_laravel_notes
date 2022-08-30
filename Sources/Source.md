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
  12. В папке seeds, создаем новый класс SourcesSeed. php artisan make:seed SourcesSeed. Добавляем данные в сидер. Проверяем получение ответа.
  ### CRUD methods
  1. Метод store() - добавление данных. В SourceRequest определяем правила валидации.'title'=> 'required'. 
  2. Описываем метод authorize()
  ```
    function athorize() {
    Auth::user()->canDo(['SUPER_ADMINISTRATOR','SOURCES_ACCESS']);
    }
```
3. Описываем методы store()
```
public function store(SourceRequest $request, Source $source){
    $source = $this->service->save($request, new Source());
    return ResponseService::sendJsonResponse(true, 200, [], [
        'item'=>$source->toArray(),
    ]);
```
4. Описываем метод Save(); Метод находиться в SourceService;
```
    public function save($request, $source){
        $source->fill($request->only($source->getFillable()));
        $source->save();
        return $source;
    }
```
5. Метод update() - обновление данных.
```
public function update(SourceRequest $request, Source $source){
    $source = $this->service->save($request, $source);
        return ResponseService::sendJsonResponse(true, 200, [], [
        'item'=>$source->toArray(),
    ]);
```
6. Метод destroy() - удаление данных.
```
public function destroy(SourceRequest $request, Source $source) {
    $source->delete();
    return ResponseService::sendJsonResponse(true, 200, [], [
        'item'=>$source->toArray(),
    ]);
```
