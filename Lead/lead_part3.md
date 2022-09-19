## Lead часть 3
1. Создаем миграцию create_lead_comments_table. Прописываем в ней необходимые в таблице столбцы. Применяем миграцию. 
```php
    $table->id();
    
    $table->text('text');

    $table->bigInteger('lead_id')->unsigned()->nullable();
    $table->foreign('lead_id')->references('id')->on('leads')->onDelete('cascade');

    $table->bigInteger('user_id')->unsigned()->nullable();
    $table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');

    $table->bigInteger('status_id')->unsigned()->nullable();
    $table->foreign('status_id')->references('id')->on('statuses')->onDelete('cascade');

    $table->text('commentValue')->nullable();
    $table->boolean('is_event')->default(0);

```
2. В модели LeadComment создаем свойство $fillable, определяем необходимые связи.
```php
  protected = $fillable = 
  [
    'text',
    'commentValue',
  ];
  
  public function lead(){
    return $this->belongsTo(Lead:class);
  }
  
    public function user(){
    return $this->belongsTo(User:class);
  }
  
    public function status(){
    return $this->belongsTo(Status:class);
  }
  
```
3. В модуле LeadComment создаем папку Service, файл LeadCommentService, прописываем namespace и т.д.
4. В LeadCommentService описываем статический метод saveComment(); //функция будет сохранять комментарии в lead_comments.
```php
  
  public static function saveComment(string $text, Lead $lead, User $user, Status $status, string $commentValue, bool $is_event){
      $comment = new LeadComment([
        'text' => $text,
        'commentValue' => $commentValue,
      ]);
      $comment->is_event = $is_event;
      $comment->lead()->associate($lead)->user()->associate($user)->status()->associate($status)->save();
      return $comment;
  }

```
5. Добавляем новый метод в функции store() Sevice, который относиться к LeadController - addStoreComments(); //после save();
```php

    $this->addStoreComments($lead, $request, $user, $status);
 ```
 6. Ниже описываем сам метод addStoreComments();
 ```php
    public function addStoreComments($lead, $request, $user, $status) {
        $is_event = true;
        $tmpText = "Пользователь ".$user->firstname."добавил новый лид со статусом ".$status->title;
        LeadCommentService::saveComment($tmpText, $lead, $user, $status, null, $is_event);
            //Проверяем если добавлен комментарий во фронте, то записываем его
        if($request->text){
            $is_event = false;
            $tmpText = "Пользователь ".$user->firstname."оставил коментарий ".$request->text;
            LeadCommentService::saveComment($tmpText, $lead, $user, $status, $request->text, $is_event);
        }
        
    }
    
 ```
7. Создаем миграцию для создания таблицы по хранению истории статусов лидов. Таблица lead_status. //связь многие ко многим
```php 
    $table->id();
    $table->bigInteger('lead_id')->unsigned->nullable();
    $table->foreign('lead_id')->references('id')->on('leads');
    $table->bigInteger('status_id')->unsigned->nullable();
    $table->foreign('status_id')->references('id')->on('statuses');
```
8. Применяем миграцию. Описываем связь многие со многим в модели Lead.
```php
    public function statuses(){
        return $this->belongsToMany(Status::class);
    }
    
```
9. Добавлем в функции store() Sevice, который относиться к LeadController перед save();
```php
    $lead->statuses()->attach($status->id);
```
