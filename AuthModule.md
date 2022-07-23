## Web and Api
### WEB

1. Создаем новый модуль Pub\Auth. Создаем контроллер, апиконтроллер и view. //Модель будет в модуле User. (Не забываем подключить модуль в config'e)
2. В папке App\Modules\Pub\Auth\Controllers создаем LoginController. Проверяем маршруты. 
3. В контроллере LoginController используем трейт AuthenticatesUsers.
4. Создаем переменную для редиректа после аутентификации (путь куда будет перенаправляться залогиненный пользователь). 
5. Создаем контструктор привязываем middleware к guest $this->middleware('guest) //за исключением 'logout';
6. Переопределяем метод ShowLoginForm(); //возвращаем view(); 'Pub::Autt.login'
7. Добавляем в роут, post который будет обрабытывать отправку данных 

#### Определяем пространство имен для view //Pub //Admin

1. В файле ModularProvider метод boot() $this->app['view']-addNamespace('Pub', [путь куда будет ссылаться пространство имен]); // /resourses/views/Pub
2. В указанном пути создаются файл. В роутах создаем новый маршрут //контроллер, метод и т.д
3. Создаем шаблон с полями входа в приложение


### API

1. Создаем контроллер для api LoginController
2. Создаем метод login, который принимает request //проверяем routes при необходимости обновляем маршруты. //post запрос

### Создание шаблона/представления

1. Используем adminlte.io или свой шаблон. 
2. В /view/Pub создаем директорию layouts - > layout.blade.php //макет проекта. <- index.html (adminlte) 
3. Убираем сайдбар, навигацию  wrapper меняем на pagecontent, content-wrapper меняем на wrapper {{content}}, футер выносим в отдельную директорию (parts), в которой будут располагаться составные части макета. Подключаем части шаблона @include('пространство имен.путь к файлам')
4. Подключаем контент @yield('content'), подключаем стили.
5. theme style, bootstrap -> /добавляем в директорию public прописываем путь {{ asset('путь к файлу в директории public') }}
6. @extends('пространство имен.путь к файлу шаблона')
7. @section('content') @endsection
8. Строки хранить в файлах локализации {{__('public.login_title')} /resources/lang/en/public.php return ['login_title' => 'значение']

### База данных

1. Создаем model User и миграцию users.
2. В миграцию вносим все необходимые столбцы firstname, lastname (nullable), email unique, phone, password, status enum, //rememberToken -> применяем миграцию -path[путь до миграции]
3. Создаем Seed. CreateAdminUser DB::table('name')->insert([]); для пароля используем bcrypt(); 
4. Формируем модель User, 
  extends AuthUser
  use illuminate/foundation/auth/user as AuthUser
  добавить трейт hasapitoken
5. формируем protected $fillable = [] //массив полей firstname, lastname, email, phone, status
6. защищенное свойство $hidden = [] //password
7. В файле config/auth.php изменить провайдерc изменить модель
