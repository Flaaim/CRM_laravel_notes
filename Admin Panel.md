## Admin Panel

1. В файле ModuleMake в методе создания Controllera и ApiControllera создаем методо updateModularConfig();
2. В данном методе получаем имя группы и имя модуля $group, $module
3. Создаем переменную $modular в которую сохраняем содержимое файла config/modular.php ->   files->get([путь до конфига])
4. С помощью регулярное выражения получаем доступ к необходимым массивам. $matches = []; php_pregmatch("", $modular, $matches); 