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
