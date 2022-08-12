## Модуль прав и привелегий пользователй

1. Создаем новый модуль Admin\Role --controller --api --migration --view --model
2. В директории модуля создаем новый контроллер PermissionController. Создаем в директории /Role еще две директории Services, Policies. Дополняем роуты web.php путями с префиксом permissions. 
3. Создаем model Permissions.
4. Создаем policy. //php artisan make:policy. 
5. Создаем миграцию для привелегий пользователей. create=permissions. Создаем миграцию для связей между roles and permissions.
6. Описываем миграцию roles. //increments('id), string('alias'), string('title'). Описываем миграцию permissions //точно такая же как и roles. Описываем миграцию role_permission. 
