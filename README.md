# dtltasu_microservices
dtltasu microservices repository

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
