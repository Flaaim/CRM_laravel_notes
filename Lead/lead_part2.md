## Вывод всех Leads с помощью api
1. Описываем метод index() App/Modules/Admin/Lead/Controllers/Api
```
 public function index()
 {
  $this->authorize('view', Lead::class);
  $result = $this->service->getLeads() //описываем данный в файл в Service
    return ResponseService::sendJsonResponse(true, 200, [], ['item'=> $result]);
  }
  ```
  2. Описываем функцию getLeads() в Service

```
public function getLeads()
{
  $leads = (new Leads())->getLeads() //описываем данную функцию в model
  $statuses = Status::all();
  $resultLeads = [];
  
  $statuses->each(function($item, $key) use (&$resultLeads, $leads){
      $collection = $leads->where('status_id', $item->id);
      $resultLeads[$item->title] = $collection->map(function($elem){
          return $elem;
      });
  });
  
  }
```
3. Описываем функцию getLeads() в модели
```
  public function getLeads(){
   return $this->with(['source', 'unit', 'status'])->whereBetween('status_id', [1,2])->orWhere(funtction($query){
    $query->where('status_id', 3)->where('updated_at', '>', \DB::raw('DATE_SUB(NOW(), INTERVAL 24 HOUR'))
   })->orderBy('created_at')->get();
  }
```
