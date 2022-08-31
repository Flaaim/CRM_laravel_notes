## Модуль Меню - 12

1. Создаем новый модуль. php artisan make:module Admin\Menu //котнроллер, модель, апиконтроллер, миграция
2. В миграции формируем структуру таблички для хранения элекментов меню. id, title, path, (int)parent, type, (int)sortorder->defaul(100). Выполняем миграцию с путем для содержиться миграция. 
3. В моделе Menu создаем две константы MENU_TYPE_FRONT = 'front',  MENU_TYPE_ADMIN = 'admin'. Создаем два публичных метода типа scopeFrontMenu($query, User $user){ }, scopeMenuByType($query, $type){ }
```
scopeMenuByType($query, $type){ 
    return $query->where('type', $type)->orderBy('parent')->orderBy('sort_order');
}


scopeFrontMenu($query, User $user){ 
    return $query->where('type', self::MENU_TYPE_FRONT);
}

```

4. В ApiController'e в методе index() необходимо вернуть 
```
return ResponseService::sendJsonResponse(true, 200, [], [
    'menu'=> (Menu::frontMenu(Auth::user())->get())->toArray()
]);
```
6. В модуле Dashboard в директории classes в классе Base.php Создаем переменную menu = $this->getMenu(); Переменная $menu передается в sidebar в меню. 
7. Описываем метод getMenu() return Menu::make('menuRender, function($m){
    foreach(MenuModel::menuByType(MenuModel::MENU_TYPE_ADMIN)->get() as $item){
        $path = $item->path;
        if($path && $this->checkRoutes($path) == true){
            $path = route($path);
        }
    }
});
Подключаем Lavary menu, модель Menu (Используем псевдоним MenuModel) Провайдер и фасад (см. github)
7. Создаем seed для тестирования данных. seed AddMenu. Заполняем таблицу тестовыми данными
## Продолжение
1. Создаем функцию checkRoutes($path); В данную функцию с помощию вспомогательного метода Route::getRoutes()->getRoutes() сохраняем все маршруты в переменную $routes. Циклом foreach обходим все полученные маршруты. Внутри цикла производим проверку if $route->getName() == $path возвращаем true. Иначе за циклом возвращаем false.
2. Продолжаем описывать функцию getMenu(). Проверяем родителя if $item->parent == 0 с помощью вспомогательного метода $m->add($item->title, $path)->id($item->id)->data('permission', []); иначе снова проверяем if($m->find($item->parent)){$m->find($item->parent)->...по аналогии с предыдущего if}
3. Добавляем пункты меню в шаблон. Создаем файл menuItems.blade.php в views/Admin/layouts/parts/.
4. В файле sidebar.blade.php подключаем menuItems @include('Admin::layouts.parts.menuItems', ['items'=>$menu->roots()]) 
5. В файле menuItems.blade.php в цикле обходим items. Внутри цикла проверяем наличие детей $item->hasChildren() если true,  снова подключаем шаблон. @include('Admin::layouts.parts.menuItems', ['items'=>$item->children()]) 
