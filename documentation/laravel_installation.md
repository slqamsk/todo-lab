 Инструкция по установке Laravel 11 на slqa.ru

Инструкция по установке Laravel 11 на slqa.ru
=============================================

Условия
-------

*   У вас есть SSH-доступ к серверу
*   Сайт обслуживается из папки: `/home/a1109685/domains/slqa.ru/public_html`
*   Веб-сервер использует PHP 8.3.x
*   Вы не хотите повредить текущий сайт

Пошаговая инструкция
--------------------

### Шаг 1. Узнайте путь к PHP 8.3

Выполните в терминале:

which php83

Пример результата: `/usr/local/bin/php83`

### Шаг 2. Установите Composer (если ещё не установлен)

php83 -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php83 composer-setup.php --quiet
rm composer-setup.php
mkdir -p ~/bin
mv composer.phar ~/bin/composer
  

### Шаг 3. Создайте команду `composer83`, привязанную к PHP 8.3

echo '#!/bin/bash
exec /usr/local/bin/php83 /home/a1109685/bin/composer "$@"' > ~/bin/composer83
chmod +x ~/bin/composer83
  

### Шаг 4. Перейдите в папку домена

cd ~/domains/slqa.ru

### Шаг 5. Сделайте резервную копию текущего сайта

cp -r public\_html public\_html\_backup\_$(date +%F)

### Шаг 6. Установите Laravel 11 в отдельную папку

composer83 create-project laravel/laravel:^11.0 my-laravel-app

Ожидайте 1–3 минуты. Папка `my-laravel-app` будет создана рядом с `public_html`.

### Шаг 7. Создайте тестовую папку в `public_html`

mkdir public\_html/laravel-test

### Шаг 8. Скопируйте публичные файлы Laravel в тестовую папку

cp -r my-laravel-app/public/\* public\_html/laravel-test/
cp -r my-laravel-app/public/.\* public\_html/laravel-test/ 2>/dev/null || true
  

### Шаг 9. Исправьте пути в файле `public_html/laravel-test/index.php`

Замените всё содержимое файла на следующее:

<?php

use Illuminate\\Http\\Request;

define('LARAVEL\_START', microtime(true));

if (file\_exists($maintenance = \_\_DIR\_\_.'/../../my-laravel-app/storage/framework/maintenance.php')) {
    require $maintenance;
}

require \_\_DIR\_\_.'/../../my-laravel-app/vendor/autoload.php';

(require\_once \_\_DIR\_\_.'/../../my-laravel-app/bootstrap/app.php')
    ->handleRequest(Request::capture());
  

### Шаг 10. Проверьте работу

Откройте в браузере: [https://slqa.ru/laravel-test/](https://slqa.ru/laravel-test/)

Вы должны увидеть стартовую страницу Laravel.

Готово!
-------

Ваш основной сайт остался нетронутым. Laravel работает в подпапке `/laravel-test/`.

Когда будете готовы сделать Laravel основным сайтом — скопируйте содержимое `laravel-test/` в `public_html/`, предварительно сохранив резервную копию.