## Admin Panel

1. В файле ModuleMake в методе создания Controllera и ApiControllera создаем методо updateModularConfig();
2. В данном методе получаем имя группы и имя модуля $group, $module
3. Создаем переменную $modular в которую сохраняем содержимое файла config/modular.php ->   files->get([путь до конфига])
4. С помощью регулярное выражения получаем доступ к необходимым массивам. $matches = []; php_pregmatch("", $modular, $matches); 
5. Проверяем количество элементов массива matches == 2 { внутри еще раз проверяем функцией preg_match() проверяем $matches[1] если ничего не найдено -> выполняем дальнейшие действия}
6. Разбиваем файл на содержимое до вставки и содержимое после вставки. $parts = preg_split('#pattern#', $modular, 2, PREG_SPLIT_DELIM_CAPTURE);
7. проверяем если количество элекментов в переменной $path === 3 $configStr = //объединяем строки [0][1]"Module"[2]
8. Сохраняем данные в файл $this->files->put(путь, строка);

## Сервис локализации

1. Создаем провайдер make:provider [путь до папки Services/Localization] // создаем LocalizationServiceProvider
2. Добавляем созданный провайдер в config/app
3. В директории Localization создаем класс LocalizationService, класс наследует Facade use Illuminate/Support/Facades/Facade
4. В классе определяем статический метод getFacadeAccessor() метод должне вернуть имя элекмента из сервисконтейнера //'Localization'
5. В LocalizationServiceProvider в методе register формируем имя сервисконтейнера $this->app->singleton('Localization', function($app){
6. return new Localization()
7. })
8. В папке localization создаем класс Localization. В классе формируем публичный метод locale(), в котором создаем переменную locale в которую с помощью функции request()->segment(1);
9. Далее в методе проверяем если что либо в переменной $locale и а содержиться ли значение $locale в массиве конфигурации config(app.locales). В конфиге app создаем еще один параметр locales => ['en', 'ru']
```
function locale(){
$locale = request()->segment(1);
if($locale && in_array($locale, config(app.locales)){
  App::setLocale($locale);
  return $locale;
}
}
```
10. В классе Base создаем еще одно свойство $locale. Определяем данное свойство в конструкторе в middleware $this->locale = App::getLocale();
11. В каталоге resources в директории lang мы можем разместить необходимый набор локализаций текущего проекта. Создаем папки ru , en в каждой из этих папок создаем файлы admin.php Каждый из этих файлов возвращает массив. В массиве формируем необходимык ключи, например 'dashboard_title' => 'Главная'
