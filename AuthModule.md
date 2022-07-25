## Web and Api
### 1.WEB

1. Создаем новый модуль Pub\Auth. Создаем контроллер, апиконтроллер и view. //Модель будет в модуле User. (Не забываем подключить модуль в config'e)
2. В папке App\Modules\Pub\Auth\Controllers создаем LoginController. Проверяем маршруты. 
3. В контроллере LoginController используем трейт AuthenticatesUsers.
4. Создаем переменную для редиректа после аутентификации (путь куда будет перенаправляться залогиненный пользователь). 
5. Создаем контструктор привязываем middleware к guest $this->middleware('guest) //за исключением 'logout';
6. Переопределяем метод ShowLoginForm(); //возвращаем view(); 'Pub::Autt.login'
7. Добавляем в роут, post который будет обрабытывать отправку данных 

#### 2.Определяем пространство имен для view //Pub //Admin

1. В файле ModularProvider метод boot() $this->app['view']-addNamespace('Pub', [путь куда будет ссылаться пространство имен]); // /resourses/views/Pub
2. В указанном пути создаются файл. В роутах создаем новый маршрут //контроллер, метод и т.д
3. Создаем шаблон с полями входа в приложение


### 3.API

1. Создаем контроллер для api LoginController
2. Создаем метод login, который принимает request //проверяем routes при необходимости обновляем маршруты. //post запрос

#### 3.1 Реализация валидации данных

1. Вынесем валидацию в отдельный класс Request в Modules/.. /Request/LoginRequest
2. В rules возвращаем массив правил валидации для полей. Authorize возвращает true если пользователю разрещается использование данного запроса.
3. Для Api при валидации необходимо вернуть ответ. 
4. Создаем еще один класс, который будет использоваться как родитель для всех реквестов, но при этом он будет наследоваться от базового класса FormRequest
5. В App создаем папку Services -> общая логика всех эл-тов проекта. Создаем папку Requests, внутри создаем новый класс ApiRequest (прописываем namespace). ApiRequest наследуется от FormREquest и родитель LoginRequest.
6. Переопределяем в классе метод failedvalidation(). Создаем переменную $errors = (new ValidationException($validator))->errors(); Генерируем исключение HttpResponseException() //передаем в HttpResponseException метод, который описываем в специальном классе. 
7. В папке Services создаем еще одну директорию -> Response, в котороый создаем новый класс ResponseService (don't forget add namespace). В данном классе определяем статический приватный метод Response Params()  формирует параметры запроса, возвращает массив параметров status, errors [], data []. Парметры будут приходить в метод ResponseParams.
8. Определяем статический публичный метод sendJsonResponse($status, $code = 200, $errors = [], $data = []) //отправляет в качестве ответа jsonстроку. Возвращает с помощью функции хелпера response()->json(параметры, $code)//параметры ответа json() формируем использую  sendJsonResponse ($status, $errors, $data)
9. Определяем дополнительные методы success($data = []) который возвращает sendJsonResponse(true, 200, [], $data); notFound() возвращает sendJsonResponse(false, 404, [], [])
10. В методе failedValidation обращаемся к классу ResponseService и вызываем на исполнение метод sendJsonResponse()//аргументы статус false, code 403, $errors

3. Формируем массив $credentials; в нем храняться данные авторизации $request() <- функция хелпер, передаем в нее поля нашей формы в виде массива (email, password)
4. Аутентифицируем пользователя, использую Auth::attempt($credentials) проверяем через if, если ошибка -> возвращаем json ответ с помощью класса ResponseService и функции sendJsonResponse() false, 403, массив ошибок, сообщение auth.login_error
5. С помощью request получаем аутентифицированного пользователя. 
6. Формируем пользователя $tokenResult = $user->createToken('Personal Access Token');
7. Далее возвращаем json ответ (true, 200, [], []). В массиве $data формируем необходимые данные 'api_token' => $tokenResult->accessToken, 'user' => $user, 'token_type' => 'Bearer', 'expires_at' => Carbon::parse($tokenResult->token->expires_at)->toDateTimeString()
8. Проверяем наличие трейта HasApiTokens в модели User
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
