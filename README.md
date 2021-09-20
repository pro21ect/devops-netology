```
1.
- Высоконагруженное монолитное java веб-приложение -  я бы использовал виртуальную машину, Docker в данном случае замедлял бы работу.
- Go-микросервис для генерации отчетов  -  в данном случае Docker, эта задача как раз для него. 1 задача - 1 Микросервис - 1 контейнер
- Nodejs веб-приложение  - выбираю Docker. Самое оно для тестирования и отладки. 
- Мобильное приложение c версиями для Android и iOS  - виртуалка, т.к необходим интерфейс пользователя.
- База данных postgresql используемая, как кэш - для кэша я бы выбрал физический сервер, как наиболее скоростной.
- Шина данных на базе Apache Kafka - пожалуй в Docker для масштабирования.
- Очередь для Logstash на базе Redis -  тоже Docker для централизации сбора логов.
- Elastic stack для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana - 
выбираю Docker, анализ в интернете показал такую связку вполне рабочей.
- Мониторинг-стек на базе prometheus и grafana - в Docker контейнеры.
- Mongodb, как основное хранилище данных для java-приложения - физическая машина для базы данных будет лучшим решением.
- Jenkins-сервер  - выберу Docker

```
``` 
2.
- Устанавливаю Docker
- Затем тяну образ Apache
sudo docker pull httpd
[sudo] пароль для jack:
Using default tag: latest
latest: Pulling from library/httpd
a330b6cecb98: Pull complete
14e3dd65f04d: Pull complete
fe59ad2e7efe: Pull complete
2cb26220caa8: Pull complete
3138742bd847: Pull complete
Digest: sha256:af1199cd77b018781e2610923f15e8a58ce22941b42ce63a6ae8b6e282af79f5
Status: Downloaded newer image for httpd:latest
docker.io/library/httpd:latest

- Запускаю контейнер
sudo docker run -p 81:80 -dit httpd
58b5c9312e605894830e03fd4d4a36774706ac5b9339f8b6547b9ebd95c8c6df

- Проверяю запущенный контейнер
sudo docker ps
CONTAINER ID   IMAGE     COMMAND              CREATED              STATUS              PORTS                               NAMES
58b5c9312e60   httpd     "httpd-foreground"   About a minute ago   Up About a minute   0.0.0.0:81->80/tcp, :::81->80/tcp   peaceful_sinoussi

- Редактирую index.html в хостовой оси  и копирую его в контейнер
sudo docker cp /home/jack/tmp/index.html 58b5c9312e60:/usr/local/apache2/htdocs

- Открываю  контейнер
sudo docker exec -it 58b5c9312e60 bash
root@58b5c9312e60:/usr/local/apache2#

- Проеряю так:
root@58b5c9312e60:/usr/local/apache2# cd htdocs
root@58b5c9312e60:/usr/local/apache2/htdocs# cat index.html
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I▒m kinda DevOps now</h1>
</body>
</html>
- В добавок проверяю из хостовой оси по адресу http://localhost:81
Всё гуд!

https://hub.docker.com/layers/168092774/pro21ect/devops-netology/latest/images/sha256-96a70c3118cc5c9060a901b9f33498699d748f01f4a34250019780ad939728a9?context=repo
 
```
```
3. 
- выкачиваю образы Centos и Debian
- создаю файл test в /home/jack/tmp хоста
- привинчиваю образ centos и проверяю наличие файла
 docker run -it -v /home/jack/tmp/:/share/info --name centos1 centos
[root@23ca9fc6e83b /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  share  srv  sys  tmp  usr  var
[root@23ca9fc6e83b /]# cd share
[root@23ca9fc6e83b share]# ls
info
[root@23ca9fc6e83b share]# cd info
[root@23ca9fc6e83b info]# ls
index.html  test
[root@23ca9fc6e83b info]# exit
exit

- то же самое с debian
docker run -it -v /home/jack/tmp/:/share/info --name debian debian
root@2039ff5f6347:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  share  srv  sys  tmp  usr  var
root@2039ff5f6347:/# cd share
root@2039ff5f6347:/share# ls
info
root@2039ff5f6347:/share# cd indo
bash: cd: indo: No such file or directory
root@2039ff5f6347:/share# cd info
root@2039ff5f6347:/share/info# ls
index.html  test

- создаю файл test1 из centos
 docker exec -ti centos1 /bin/bash
[root@23ca9fc6e83b /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  share  srv  sys  tmp  usr  var
[root@23ca9fc6e83b /]# cd share
[root@23ca9fc6e83b share]# ls
info
[root@23ca9fc6e83b share]# cd info
[root@23ca9fc6e83b info]# lc
bash: lc: command not found
[root@23ca9fc6e83b info]# ls
index.html  test
[root@23ca9fc6e83b info]# touch test1
[root@23ca9fc6e83b info]# ls
index.html  test  test1
[root@23ca9fc6e83b info]# exit
exit

- проверяю файл из контейнера debian
root@jack-VirtualBox:/home/jack/tmp# docker exec -ti debian /bin/bash
root@2039ff5f6347:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  share  srv  sys  tmp  usr  var
root@2039ff5f6347:/# cd share
root@2039ff5f6347:/share# ls
info
root@2039ff5f6347:/share# cd info
root@2039ff5f6347:/share/info# ls
index.html  test  test1
 
```
