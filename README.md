# Django site

Докеризированный сайт на Django для экспериментов с Kubernetes.

Внутри конейнера Django запускается с помощью Nginx Unit, не путать с Nginx. Сервер Nginx Unit выполняет сразу две функции: как веб-сервер он раздаёт файлы статики и медиа, а в роли сервера-приложений он запускает Python и Django. Таким образом Nginx Unit заменяет собой связку из двух сервисов Nginx и Gunicorn/uWSGI. [Подробнее про Nginx Unit](https://unit.nginx.org/).

## Как запустить dev-версию
В папке проекта cоздать файл .env и заполнить его:
```
POSTGRES_DB=
POSTGRES_USER=
POSTGRES_PASSWORD=
```
Разверните БД Postgres в Doker:
```shell-session
 docker-compose up -d --build
```

Запустите Minikube:
```shell-session
minikube start
```
Включает аддон ingress в вашем кластере Minikube
```shell-session
minikube addons enable ingress
```
В файле [`django-app.yaml`](./django-app.yaml) заполните переменные окружения:
```
data:
  DATABASE_URL: "postgresql://username:password@db-hostname:5433/dbname"
  SECRET_KEY: "your_secret_key_here"
```
Запустите манифест [`django-app.yaml`](./django-app.yaml)
```shell-session
kubectl apply -f django-app.yaml
```
#### При первом запуске проекта сделайте миграции и создайте суперпользователя:
Посмотрите имя подов приложения:
```shell-session
kubectl get pods
```
Подключитесь к любому из подов:
```shell-session
kubectl exec -it <имя-пода> -- /bin/bash
```
Запустите миграции:
```shell-session
python manage.py migrate
```
Создайте суперпользователя:
```shell-session
python manage.py createsuperuser
```
Узнайте  IP-адреса узла:
```
minikube ip
```

Внесите IP-адреса узла в файл hosts на вашей машине:
```
<ip-адреса узла> star-burger.test
```
