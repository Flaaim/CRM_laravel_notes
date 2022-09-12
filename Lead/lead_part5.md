## Lead part5
1. Метод arhive(); метод будет выводить лиды со статусом 3 (done), за последние 24 часа.
2. Описываем метод arhive();

```php
//LeadControlelr
public function arhive() {
  $this->authorize('view', Lead::class);
  $leads = $this->service->arhive();
  
  return ResponseService::sendJsonResponse(true, 200, [], [
    'item' => $leads
  ]);
  }
  
 //LeadService
 
 public function arhive(){
  $leads = (new Lead())->getArhiveLeads();
  return $leads;
 }
 
 //Lead Model
 
 public function getArhiveLeads(){
  return $this->with('statuses', 'source', 'unit')->where('status_id', self::DONE_STATUS)->where('updated_at', '<', '\DB:raw(DATE_SUB(NOW(), INTERVAL 24 HOUR))')->oerderBy('updated_at', 'DESC')->paginate(config('settings.paginate'));
 }
2. Описываем метод show(); //метод show() будет показывать один lead.
```php
  public function show(Lead $lead)
  {
    $this->authorize('view', Lead::class);
    
    return ResponseService::sendJsonResponse(true, 200, [], [
      'item' => $lead
    ]);
  }

```
3. Описываем метод checkExists(); // Данный метод будет 
```php

  public function checkExists() {
    $this->authorize('create', Lead::class);
    $lead = $this->service->checkExists();
    
    if($lead) {
      return ResponseService::sendJsonResponse(true, 200, [], [
      'item'=> $lead
    ]);
    } else {
        return ResponseService::success();
    }
    
  }
  public function checkExists() {
    $queryBuilder = Lead::select('*');
      if($request->link) {
          $queryBuilder->where('link', $request->link);
      }
      elseif($request->phone){
          $queryBuilder->where('phone', $request->phone);
      }
      $queryBuilder->where('status_id', '!=', Lead::DONE_STATUS);
      return $queryBuilder->first();
  }
```
4. Описываем метод isQualityLead(); Данный метод будет устанавливать флаг isQualityLead (Был ли лид успешным)
```php

  public function isQualityLead(Request $request, Lead $lead) {
    $this->authorize('edit', Lead::class);
    $lead = $this->service->isQualityLead($request, $lead);
    
    return ResponseService::sendJsonResponse(true, 200, [], [
      'item'=> $lead
    ]);
  }

  public function isQualityLead($request, $lead) {
  
        $lead->isQualityLead = true;
        $lead->save();
    
        return $lead;
  }

```
5. Описываем Routes в api.php
```php

Route::prefix('leads')->group(function(){
    Route::get('/', [LeadController::class, 'index'])->name('api.leads.index');
    Route::post('/', [LeadController::class, 'store'])->name('api.leads.store');
    Route::put('/{lead}', [LeadController::class, 'update'])->name('api.leads.update');
    Route::get('/{lead}', [LeadController::class, 'show'])->name('api.leads.show');

    Route::get('/arhive/index', [LeadController::class, 'arhive'])->name('api.arhive.index');
    Route::post('/create/check', [LeadController::class, 'checkExists'])->name('api.leads.check');
    Route::put('/update/quality/{lead}', [LeadController::class, 'isQualityLead'])->name('api.leads.update.quality');
});
```








