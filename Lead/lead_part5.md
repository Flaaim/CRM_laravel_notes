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
