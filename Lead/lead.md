## Создание и обработка лидов
1. Создаем новый модуль make:module Admin\Lead --api --model --migration
2. Описываем файл migration leads
  ```
   Schema::create('leads', function (Blueprint $table) {
            $table->id();
            $table->string('phone')->nullable();
            $table->string('link')->nullable();
            $table->integer('count_create')->default(1);
            $table->boolean('is_processed')->default(false);
            $table->boolean('isQualityLead')->default(false);
            $table->boolean('is_express_delivery')->default(false);
            $table->boolean('is_add_sale')->default(false);

            $table->integer('source_id')->nullable()->unsigned();
            $table->foreign('source_id')->references('id')->on('sources')->onDelete('cascade');

            $table->bigInteger('unit_id')->nullable()->unsigned();
            $table->foreign('unit_id')->references('id')->on('units')->onDelete('cascade');

            $table->bigInteger('user_id')->nullable()->unsigned()->onDelete('cascade');
            $table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');

            $table->bigInteger('status_id')->nullable()->unsigned();
            $table->foreign('status_id')->references('id')->on('statuses')->onDelete('cascade');
            $table->timestamps();
        });
     ```
3. Описываем файл migration units
     ```
             Schema::create('units', function (Blueprint $table) {
            $table->id();
            $table->string('title')->nullable();
            $table->timestamps();
        });
     ```
4. Описываем файл migration statuses
```
        Schema::create('statuses', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->string('title_ru');
            $table->timestamps();
        });
```
5. Примеряем миграции.
6. Описываем связи в модели Lead.
```
public function units() {
  return $this->belongsTo(Unit::class);
}

public function statuses()
{
  return $this->belongsTo(Status::class);
}
 
public function users()
{
  return $this->belongsTo(User::class);
}  
public function source()
{
  return $this->belongsTo(Source::class);
}  
```
7. Создаем модели Unit, Status
8. Создаем модуль LeadComment --api --model --migration
9. В модели LeadComment опичваем связи
```
 public function comments()
 {
  return $this->hasMany(Comment::class);
 }
 public function LastComment()
 {
  return $this->comments()->where('comment_variable', '!=', NULL)->orderBy('id', 'desc')->first();
```
10. Создаем трейт App\Modules\Lead\Model\Traits\UserLeadsTrait.php
11. В трейте прописываем функции связи
```
  public function leads() 
  {
    return $this->hasMany(Lead::class);
  }
  
  public function comments()
  {
  return $this->hasMany(LeadComment::class);
  }
  public function tasks() {}

  public function responsibleTasks() {}

```
12. Создаем Policy App\Modules\Admin\Lead\Policies\LeadPolicy
13. Копируем методы в LeadPolicy из RolePolicy.
14. Добавляем новые permissions в метод canDO() LEADS_ACCESS, DASHBOARD_ACCESS. Добавляем их в таблицу permissions.
15. В модуле Lead создаем папку Services файл LeadService.php
16. В LeadApiController создаем переменную $service и определяем конструктор

```
  protected $service;
  
  public function __construct(LeadService $leadService)
  {
    $this->service = $leadService;
  }
  
```
