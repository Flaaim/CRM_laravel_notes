## Модуль прав и привелегий пользователй

1. Создаем новый модуль Admin\Role --controller --api --migration --view --model
2. В директории модуля создаем новый контроллер PermissionController. Создаем в директории /Role еще две директории Services, Policies. Дополняем роуты web.php путями с префиксом permissions. 
3. Создаем model Permissions.
4. Создаем policy. //php artisan make:policy. 
5. Создаем миграцию для привелегий пользователей. create=permissions. Создаем миграцию для связей между roles and permissions.
6. Описываем миграцию roles. //increments('id), string('alias'), string('title'). Описываем миграцию permissions //точно такая же как и roles. Описываем миграцию role_permission.

role_premission table
```
$table->increments('id');
            
$table->integer('role_id')->unsigned();
$table->foreign('role_id')->references('id')->on('roles')->onDelete('cascade');

$table->integer('permission_id')->unsigned();
$table->foreign('permission_id')->references('id')->on('permissions')->onDelete('cascade');
```
7. Описываем миграцию role_user
```
$table->increments('id');
            
$table->BigInteger('user_id')->unsigned()->nullable();
$table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');

$table->integer('role_id')->unsigned()->nullable();
$table->foreign('role_id')->references('id')->on('roles')->onDelete('cascade');

$table->timestamps();
```
10. Описываем Модель Role. заполняем массив fillable. Создаем функции связи 
```
public function perms(){
        return $this->belongsToMany(Permission::class)
    }

    public function users(){
        return $this->belongsToMany(User::class)
    }
``` 
11. Создаем вспомогательный метод savePermission($perms).  В методе проверяем получаемый массив на пустота if(!empty($perms)), то $this->perms()->sync(); иначе $this->perms()->detach();
12. В RoleController устаналиваем наследование от Base. В методе index() вызываем метод $this->authorize('view', Role::class);
13. Получаем список всех ролей из базы данных $roles = Role::all();
14. Определяем свойство content, свойство title
```
$this->title = 'Текст';
$this->content = view('Admin::Role.index')->with(['role'=>$roles, 'title' => $this->title])->render();
```
Возвращаем renderOutput();
15. В файле RolePolicy. Определяем метод view, который возвращает true (временно). Перходим в AuthServiceProvider в массив $policies прописываем Role::class => RolePolicy::class 

## Часть 2
1. Заполняем шаблон index. В RoleController описываем метод create(); //по аналогии с методом index();
2. В RolePolicy добавляем методо create()//возвращает true;
3. Определяем шаблон create. Проверяем роуты
```
Route::prefix('roles')->group(function(){
    route::get('/', [RoleController::class, 'index'])->name('roles.index');
    Route::post('/', [RoleController::class, 'store'])->name('roles.store');
    route::get('/create', [RoleController::class, 'create'])->name('roles.create');
    route::get('/{role}', [RoleController::class, 'show'])->name('roles.read');
    route::get('/edit/{role}', [RoleController::class, 'edit'])->name('roles.edit');
    route::put('/{role}', [RoleController::class, 'update'])->name('roles.update');
    route::delete('/{role}', [RoleController::class, 'delete'])->name('roles.delete');
});
```
4. В папке Role создаем папку Requests, в данной папке командой создаем файл RoleRequest. Устанавливаем authorization true. Описываем правила. 
5. Создаем папку Services, в которой создаем файл RoleService, в котором определяем метод save(RoleRequest $request, Model $model){}
6. В классе Base  опеределяем свойство $service.
7. В RoleController определяем метод 
```
 __construct(RoleService $roleService){
            parent::__construct();
            $this->service = $roleSrvice; }

```
8. В store() обращаемся к свойству service и сохраняем в нем метод save() $this->service->save(); Возвращаем \Redirect::route('roles.index')->with(['message'=>'success']);
9. В методе save() обращаемся к модели $model->fill($request->only($model->getFillable())); Сохраняем данные в базе. Возвращаем или true или какие то проверки сохранения делаем.

## Часть 3

1. В PermissionController метод store(Request $request). Проверяем есть ли у пользователя права на выполнении соответсвующего запроса. $this->authorize('create', Role::class);
2. Получаем все роли $roles = Role::all();
3. Получаем реквест без '_token', $data = $request->except('_token');
4. Циклом обходим $roles 

```
foreach($roles as $role){
  if(isset($data[$role->id])){
        $role->savePermissions($data[$role->id]);    
  }else {
        $role->savePermissions([]);
 }
   }
```
5. Переносим данный код начиная от получения всех ролей до конца цикла в метод save($request) //папка Service
6. На месте перенесенного кода оставляем $this->service->save($request);
7. В методе store(Request $request) дописываем return back()->with(['message'=> 'success]);
8. Раскоментируем метод hasPermission($val->alias); в Шаблоне Pesmission/index.blade.php;
9. В модели Role добавляем метод hasPermission($val->alias);

```
public function hasPermission($alias, $require = false){
        if(is_array($alias)){
            foreach($alias as $permissionAlias){
                $hasPermissions = $this->hasPermission($permissionAlias);
                    if($hasPermissions && !$require){
                        return true;
                    }
                    elseif(!$hasPermissions && $require){
                        return false;
                    }
            }
        }else {
            foreach($this->perms as $permission){
                if($permission->alias == $alias){
                    return true;
                }
            }
        }
        return $require;
    }
 ```
 //После применения данного метода чекбоксы должны сохранять свое состояние.
 10. Определяем трейт для работы с политиками безопасности. Данный трейт будет связывать сущность User  с ролями и привелегиями. В модуле Role, папке Module создаем папку Traits. Создаем файл UserRoles.php прописываем namespace. 
 11. Определям в трейте связь между сущностью User и сущностью Role. public function roles(){return $this->belongToMany(Role::class, 'role_user');
 12. Подлючаем trait  в модели User.
 13. В трейте создаем следующие методы 
```
public function canDo($alias, $require = false){

    }

    public function hasRole($alias, $require = false){

    }

    public function getMergedPermissions(){

    }
    
    public function getRoles(){
        
    }
```
14. Описываем функцию canDo($alias, $require = false)
```
    public function canDo($alias, $require = false){
        if(is_array($alias)){
            foreach($alias as $permName){
                $result = $this->canDo($permName);
                if($result && !$require){
                    return true;
                }elseif(!$result && $require){
                    return false;
                }
            }
        }else {
            foreach($this->roles as $role){
                foreach($role->perms as $permission){
                    if(Str::is($alias, $permission->alias)){
                        return true;
                    }
                }
            }
        }
        return $require;
    }
    
```
15. Функция hasRole($alias, $require = false)
```
 public function hasRole($alias, $require = false){
        if(is_array($alias)){
            foreach($alias as $roleName){
                $hasRole = $this->hasRole($roleName);
                if($hasRole && !$require){
                    return true;
                }elseif(!$hasRole  && $require){
                    return false;
                }
            }
        }else {
            foreach($this->roles as $role){
                    if($role->alias == $alias){
                        return true;
                    }
                
            }
        }
        return $require;
    }
```
16. Функции getMergedPermissions(), getRoles()

```
public function getMergedPermissions(){
        $result = [];
        foreach($this->roles as $role){
            $result = array_merge($result, $role->perms->toArray());
        }
        return $result;
    }

    public function getRoles(){
        $roles = [];
        if($this->roles){
            return $this->roles;
        }
        return $roles;
    }
 ```
 ## Часть 4
 1. Переходим в модель Menu scopeFrontMenu(), рскоментируем условие WhereHas. Если все пункты меню равны в которых тип равен front,
```
->WhereHas('perms', function($q) use($user)){

}
```
