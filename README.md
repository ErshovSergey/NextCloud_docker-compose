# NextCloud_docker-compose  
nextcloud запущенный через docker-compose

по мотивам https://hub.docker.com/_/nextcloud/  
Создать  
```docker-compose down --remove-orphans```  
Пересоздать  
```docker-compose up --build -d --remove-orphans --force-recreate```  

## cron - регулярные задания  
Простой и не совсем правильный способ - на хостовой машине создать задание в cron  
    */5 * * * * docker exec -u www-data kcc.elavt.spb.ru_nextcloud php cron.php

### Команды упраления  
**Пересканировать каталог**  
```
su -s "/bin/bash" www-data
    /var/www/html/occ files:scan <username>
    /var/www/html/occ files:scan --all
    /var/www/html/occ upgrade
```
**Очистить версии файлов**
```
su -s "/bin/bash" www-data
php occ versions:cleanup <username>
```
**Очистить корзину**
```
su -s "/bin/bash" www-data
www-data@nextcloud:~/html$ php occ trashbin:cleanup <username>
```
* * *
Обновление 
узнать текущую версию
```
docker exec -u www-data <container_name> php occ status
```
обновить
```
docker exec -u www-data <container_name> php occ upgrade
```
**Выключить режим обслуживания**
```
docker exec -u www-data <container_name> php occ maintenance:mode --off
```
* * *
После обновление nextcloud обновить базу
```
su -s "/bin/bash" www-data
    php /var/www/html/occ upgrade
    php /var/www/html/occ maintenance:mode --off
```
или просто выключить режим обслуживания и далее обновить через веб-интерфейс
```
su -s "/bin/bash" www-data
     php /var/www/html/occ maintenance:mode --off
```
**Очистить корзину**
```
docker exec -i -t <container_name> bash
su -s "/bin/bash" www-data
php occ trashbin:cleanup bob
```
**Обновить образ**
```
  docker pull nextcloud
  docker stop <container_name>
  docker rm <container_name>
  docker run <OPTIONS> -d nextcloud
```

### Обновление Nextcloud в контейнере на несколько версий - требует проверки  
- удалить (если есть) файл */var/www/html/config/update.config.php* 
- создать файл */var/www/html/config/update.config.php* с указанием версий, которым доверяем, пример файла при обновлении с 20 на 25
```
<?php
$OC_Version = array(20,0,1,1);
$OC_VersionString = '20.0.1';
$OC_Edition = '';
$OC_Channel = 'stable';
$OC_VersionCanBeUpgradedFrom = array (
  'nextcloud' =>
  array (
    '20.0' => true,
    '21.0' => true,
    '22.0' => true,
    '23.0' => true,
    '24.0' => true,
    '25.0' => true,
  ),
  'owncloud' =>
  array (
    '10.5' => true,
  ),
);
$vendor = 'nextcloud';
```
- пересоздать последовательно для всех версий контейнер из образа "текущая версия Nextcloud + один" каждый раз запуская обновление  
