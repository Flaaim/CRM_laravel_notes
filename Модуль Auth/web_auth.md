# Аuth Web.
1. Устанавливаем laravel\ui (если до этого не был установлен)
2. Создаем модуль Pub\Auth --controller --apicontroller --view // --apicontroller для аутентификации по API.
3. Создаем LoginController вместо AuthController (AutController удаляем). 
4. Подключаем trait AuthenticatesUsers.
5. Описываем закрытое св-во $redirectTo = 'путь куда будет перенапраляться залогиненный пользователь'; 
6. Создаем конструктор //В конструкторе определен запрет для входа к данному контроллеру аутентифицированных пользователей 
```php
  public function __construct(){
    $this->middleware('quest')->except('logout');
  }
  
```
7. В LoginController переопределяем метод showLoginForm();
```php
  public function showLoginForm(){
    return view('Pub::Auth.login');
  }

```
8. Выводим форму страницы логин. Создаем layout.blade.php подключаем bootstrap. Создаем форму login.
9. Добавляем маршрут post запроса LoginController в routes.
10. Формируем модель User. //App\Modules\Admin\User\Models\User.php
10.1. Создаем миграцию. (Дефолтную миграцию users удаляем).
```php
    $table->bigInteger('id);
    $table->string('firstname');
    $table->string('lastname')->nullable();
    $table->string('email', 191)->unique();
    $table->string('password');
    $table->enum('status', [0,1])->default(0);
    $table->rememberToken();
```
10.2. Выполняем миграцию с параметром --path=путь до миграции
11. Создаем с помощью Seed пользователя для проверки работы логина.
12. В модели User меняем родителя на AuthUser, который явл. псевдонимом от Illuminate\Foundation\Auth\User
13. В модели User формируем свойства $fillable; $hidden;
```php
  use Illuminate\Foundation\Auth\User as AuthUser;
  
  class User extends AuthUser {
        use HasFactory;

    $fillable = [
        'firstname',
        'lastname',
        'email',
        'status',
    ];

    $hidden = [
        'password',
        'remember_token',
    ]
  }
```
