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
