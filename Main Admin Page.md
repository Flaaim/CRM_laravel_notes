## Главная страница админки
1. В resources/views/Adnin создаем папку layout, где будут храниться все лайауты
2. Вносим изменения в layout.blade, добавляем хедер, навигацию. 
3. В директории Admin/Dashboard создаем директорию dashboard. Данный шаблон будет наследоваться от Admin::layouts.layout. Указываем секции header. Секция header подключается с помощью include([путь до header'a]) //parts/header.
4. Аналогично определяем navigation. @section {!!$sidebar!!} @endsection
5. Определяем секцию content @section {!!$content!!} @endsection
6. Определяем секцию footer точно также через как и хеадер через include.
7. В классе Base добавляем новую переменную $siderbar. В $this->vars с помощью хелпера Arr::add() добавляем переменную sidebar
8. В директории parts создаем header.blade.php
