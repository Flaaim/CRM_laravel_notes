## Lead Part 4
//Создаем метод для добавления коментариев к лиду в базу данных.
1. Создаем request LeadCommentRequest. Создаем LeadCommentPolicy. 
2. Прописываем rules в LeadCommentRequest
```php
  reules = [
    'lead_id' => 'required',
    'status_id' => 'required',
    'text' => 'nullable',
  ];
```
3. Регистрируем LeadCommentPolicy.
4. В LeadCommentController создаем service.
```php
  protected $service;
  
  piblic function __construct(LeadCommentService $service){
    return $this->service = $service;
  }
```
5. В LeadCommentController описываем метод store();
```php
  public function store(LeadCommentRequest $request){
    $this->authorize('create', LeadComment::class);
    $lead = $this->service->store($request, Auth::user());
    
    return ResponseService::sendJsonResponse(true, 200, [], [
      'lead' => $lead
    ]);
  }

```
6. Описываем метод store() в LeadCommentService.
```
  public function store($request, $user){
    $lead = Lead::findOrFail($request->lead_id);
    $status = Status::findOrFail($request->status_id);
    
    if($status->id != $lead->status_id){
      $is_event = true;
      $lead->status()->associate($status)->save();
      $tmpText = "Пользователь ".$user->firstname." изменил статус лида на ".$status->title;
      self::saveComment($tmpText, $lead, $user, $status, null, $is_event);
      $lead->statuses()->attach($status->id);
    }
    
    if($request->user_id && $request->user != $lead->user_id){
      $is_event = true;
      $newUser = User::findOrFail($request->user_id); 
      $lead->user()->associate($newUser)->save();
      $tmpText = "Пользователь ".$user->firstname." изменил автора лида на ".$newUser->firstname;
      self::saveComment($tmpText, $lead, $newUser, $status, null, $is_event);
    }
     if($request->text){
     $is_event = false;
     $tmpText = "Пользователь ".$user->firstname." оставил комментарий ".$request->text;
     self::saveComment($tmpText, $lead, $user, $status, $request->text);
    }
    return $lead;
  }

```
7. Создаем метод update() в LeadController. Данный метод необходим для обновления лидов, при обновлении статус лида будет менять на new.
```
  public function update(leadCreateRequest $request, Lead $lead){
      $this->authorize('update', Lead::class);
      $lead = $this->service->update($request, Auth::user(), $lead);
      return ResponseService::sendJsonResponse(true, 200, [], [
        'item'=> $lead,
      ]);
  }

```
8. Описываем метод update() в Service.
```
  public function update($request, $user, $lead){
    $tmp = clone $lead //клонируем текущий лид
    $lead->countable++; //увелиичваем счетчик лида на 1
    $status = Status::where('title', 'New')->first(); //получаем статус лида new
    $lead->status()->associate($status);
    $lead->save();
    $this->addUpdateComments($request, $lead, $tmp, $status, $user);
    return $lead;
  }
  
```
9. Описываем метод addUpdateComments()
```
  public function addUpdateComments($request, $lead, $tmp, $status, $user){
      if($request->text){
            $tmpText = "Пользователь ".$user->firstname." оставил комментарий к лиду ".$request->text;
            LeadCommentService::saveComment($tmpText, $lead, $user, $status, $request->text, $is_event);
        }
        if($tmp->unit_id != $lead->unit_id){
            $is_event = true;
            $tmpText = "Пользователь ".$user->firstname." изменил подразделение лида на ".$lead->unit->title;
            LeadCommentService::saveComment($tmpText, $lead, $user, $status, null, $is_event);
        }
        if($tmp->source_id != $lead->source_id){
            $is_event = true;
            $tmpText = "Пользователь ".$user->firstname." изменил источник лида на ".$lead->source->title;
            LeadCommentService::saveComment($tmpText, $lead, $user, $status, null, $is_event);
        }
        $is_event = true;
        $tmpText = "Пользователь ".$user->firstname." создал лид со статусом ".$status->title;
        LeadCommentService::saveComment($tmpText, $lead, $user, $status, null, $is_event);
        $lead->statuses()->attach($status->id);
  }


```
