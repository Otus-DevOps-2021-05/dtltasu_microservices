# dtltasu_microservices
dtltasu microservices repository

### HW Docker-3 ###
Задание №1 со *
Переменные в команду docker run задаются через -e или --env
 docker run -d --network=reddit -p 9292:9292 --env COMMENT_SERVICE_HOST=comment_new --env POST_SERVICE_HOST=post_new dtltasu/ui:1.0
после запуска всех контейенеров с новыми переменными сервис работает

Задание №2 со *
Собрал образ на основе alpine образ получился почти в три раза меньше
файл Dockerfile.1
dtltasu/comment     1.1                 aae00d7377ba        22 hours ago        262MB
dtltasu/comment     1.0                 a2a8f8342ac6        40 hours ago        769MB
так же для уменьшения размера можно очищать следы установок пакетов
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

Чтоб данные из контейнеров сохранялись необходимо прокинуть volume
сначала создается docker volume и указывается через параметр -v или --volume


123

### HW Docker-2 ###
Задание №1 со *

Сравнил вывод команд:
$ docker inspect <u_container_id>
$ docker inspect <u_image_id>

объяснение в занес в файл dockermonolith/docker-1.log

создал инстанс в яндекс клауде командой
yc compute instance create
--name docker-host
--zone ru-central1-a
--network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4
--create-boot-disk image-folder-id=standard-images,image-family=ubuntu-1804-
lts,size=15
--ssh-key ~/.ssh/id_rsa.pub

создал докер машин командой

$ docker-machine create
--driver generic
--generic-ip-address=<ПУБЛИЧНЫЙ_IP_СОЗДАНОГО_ВЫШЕ_ИНСТАНСА>
--generic-ssh-user yc-user
--generic-ssh-key ~/.ssh/id_rsa
docker-host

переключил докер на удаленный хост
eval $(docker-machine env docker-host)

создали докер файл и сбилдили образ.
Запустили образ на созданой машине

docker build -t reddit:latest .
docker run --name reddit -d --network=host reddit:latest

Отправили наш образ в docker hub для использования образа не только локально.

Задание №2 *
Создал структуру папок infra аналогично репозиторию infra
терраформом инстансы создаются, а с помощью плейбуков устанавливаетсядокер и запускается приложение.
Проверено что все работает
