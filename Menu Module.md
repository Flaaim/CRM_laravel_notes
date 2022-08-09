## Модуль Меню - 12

1. Создаем новый модуль. php artisan make:module Admin\Menu //котнроллер, модель, апиконтроллер, миграция
2. В миграции формируем структуру таблички для хранения элекментов меню. id, title, path, (int)parent, type, (int)sortorder->defaul(100). Выполняем миграцию с путем для содержиться миграция. 
3. В моделе Menu создаем две константы MENU_TYPE_FRONT = 'front',  MENU_TYPE_ADMIN = 'Admin'. Создаем два публичных метода типа scopeFrontMenu($query, User $user){ }, scopeMenuByType($query, $type){ }
```
scopeMenuByType($query, $type){ 
    return $query->where('type', $type)->orderBy('parent')->orderBy('sort_order');
}


scopeFrontMenu($query, User $user){ 
    return $query->where('type', self::MENU_TYPE_FRONT);
}

```

4. В ApiController'e в методе index() необходимо вернуть ResponseService::sendJsonResponse(true, 200, [], ['menu' => (Menu::frontMenu(Auth::user()->get()))->toArray()]);
5. В модуле Dashboard в директории classes в классе Base.php Создаем переменную menu = $this->getMenu(); Переменная $menu передается в sidebar в меню. 
6. Описываем метод getMenu() return Menu::make('menuRender, function($m){
    foreach(MenuModel::menuByType(MenuModel::MENU_TYPE_ADMIN)->get() as $item){
        $path = $item->path;
        if($path && $this->checkRoute($path) == true){
            $path = route($path);
        }
    }
});
Подключаем Lavary menu, модель Menu (Используем псевдоним MenuModel) Провайдер и фасад (см. github)
7. Создаем seed для тестирования данных. seed AddMenu. Заполняем таблицу тестовыми данными
