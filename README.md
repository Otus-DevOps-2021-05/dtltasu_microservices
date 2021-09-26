# dtltasu_microservices
dtltasu microservices repository
[![Run tests for OTUS homework](https://github.com/Otus-DevOps-2021-05/dtltasu_microservices/actions/workflows/run-tests.yml/badge.svg)](https://github.com/Otus-DevOps-2021-05/dtltasu_microservices/actions/workflows/run-tests.yml)

### HW Kubernetes-1 ###
1. Создал манифесты
2. Установил k8s и Kubeadm
https://www.liquidweb.com/kb/how-to-install-kubernetes-using-kubeadm-on-ubuntu-18/

3. Поднял кластер с мастер и воркер нодой
root@fhmg7m6dlkofs4gu2uaq:/home/ubuntu# kubectl get no
NAME                   STATUS   ROLES                  AGE    VERSION
fhm9fcn8o5ornm9bdgeh   Ready    <none>                 118m   v1.22.2
fhmg7m6dlkofs4gu2uaq   Ready    control-plane,master   133m   v1.22.2

После первого запуска ноды были в статусе NotReady
для их запуска установил сетевой плагин и раскоментил строчки с сетевыми настройками
curl https://docs.projectcalico.org/manifests/calico.yaml -O

раскомментируем переменную CALICO_IPV4POOL_CIDR в манифесте и установить для нее то же значение 10.244.0.0/16

kubectl apply -f calico.yaml

4. Применил ранее созданный манифест
kubectl apply -f test.yaml

пода поднялась
root@fhmg7m6dlkofs4gu2uaq:/home/ubuntu# kubectl get po
NAME                                  READY   STATUS    RESTARTS   AGE
comment-deployment-85bc889b59-n8nf9   1/1     Running   0          39m

Для получения данных и запуска манифестов с локальной машины
пользуюсь командами
kubectl --kubeconfig ~/.kube/conf-1 apply -f comment-deployment.yml
kubectl --kubeconfig ~/.kube/conf-1 get po

пока не разобрался как перенести эти данные в основной конфиг куба, при копировании их в файл
~/.kube/config получаю ошибку

Попробовал экспортировать в переменную KUBECONFIG=~/.kube/config:~/.kube/сonf-1  - тоже не сработало

### HW logging-1 ###
1. Скопировали и сбилдили образы с тегом logging
2. Создали инстанс для работы с логированием
3. Создали docker-compose-logging со стеком EFK
4. В директории logging/fluentd создали докер файл и конф файл для флюента
5. Для сервисов в docker-compose определили драйвер для отправки логов
6. В кибане создали индекс флюента и поигрались с поиском логов
7. Добавили фильтра для стуктурированных логов
8. Добавили неструктурированные логи и добавили грок шаблоны для парсинга
9. Добавили сервис Zipkin и настроили отправку логов.

### HW monitoring-1 ###
1. Запустили prometheus
2. В конфиг прометеуса добавили таргеты для отображение в UI и сбора метрик
3. Через скрипты сбилдили новые образы
4. Добавили node-exporter (без объявления сети и портов в docker-compose прометеус его не видел)
5. Мониторинг базы сделал на основе noenv/mongo-exporter без докер файла
6. Так же добавил black-box экспортер для сервисов, проверил все работает (
    для недо сделал Dockerfile и yml файл с настройками)
7. Сделал Makefile с возможностью запускать/останавливать/билдить/пушить контейнеры,
как все скопом так по отдельности
для условий build up down stop logs  можно через  $(c) передавать имя контейнера
например '''make up c=ui'''
так жя для примера добавил функция билда не через компос а через Dockerfile
'''
build_dockerfile:
	docker build -f folder_with_dockerfile/Dockerfile -t $(USER):prometheus .
'''

### HW gitlab-ci-1 ###
Для создания машины использовал packer образ с докером и terraform
Создал директории и докер файл после запуска страница отдавала то 422, то 502
проблему решил так:
gitlab-rails console -e production

проверил что узер root есть
user = User.where(id: 1).first

создал пароль дря рута
user.password = 'secret_pass'
user.password_confirmation = 'secret_pass'

и сохранил
 user.save!
 после этих махинация страница открывалась отлично

Задание №1 *
Создал простой плайбк для развертывания Gitlab
ansible-playbook-gitlab.yml

Отключил возможность регистрации
Создал группу и проект
Добавил ремоте в настройки репозитория
git remote add gitlab http://<your-vm-ip>/homework/example.git
git push gitlab gitlab-ci-1

Создал pipeline
Добавил ранера
задание №2 *
Создал docker-compose для запука раннера
docker-compose-gitlab_runner.yml
Зарегистрировал раннера
Проверена работа пайплайнов
Добавлены разные окружения
Провелена работа с тегами и динамическими окружениями


### HW Docker-4 ###
Задание №1
Изменен docker-compose для работы с несколькими сетями
пример с пост
post:
    container_name: post
    networks:
      - front_net
      - back_net

Задаине №2-3-4
Вынес переменные в отдельный фал .env
post_db:
    container_name: db
    env_file:
Для удобства добавлены имена контейнеров при запуске docker-compose

Базовое имя проекта задается через ключь docker-compose -p name up -d
Создан docker-compose.override.yml для изменения параметров запуска основного docker-compose, запускается через ключ -f с указанием основного файла
и файла с нужными параметрами

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
