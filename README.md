# NextCloud_docker-compose
nextcloud запущенный через docker-compose

по мотивам https://hub.docker.com/_/nextcloud/

docker-compose down --remove-orphans

docker-compose up --build -d --remove-orphans --force-recreate


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
