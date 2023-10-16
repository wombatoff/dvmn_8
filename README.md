# Django site

Докеризированный сайт на Django для экспериментов с Kubernetes.

Внутри конейнера Django запускается с помощью Nginx Unit, не путать с Nginx. Сервер Nginx Unit выполняет сразу две функции: как веб-сервер он раздаёт файлы статики и медиа, а в роли сервера-приложений он запускает Python и Django. Таким образом Nginx Unit заменяет собой связку из двух сервисов Nginx и Gunicorn/uWSGI. [Подробнее про Nginx Unit](https://unit.nginx.org/).

## Как запустить dev-версию

Запустите Minikube:
```shell-session
minikube start
```
Установите Helm:

Установите PostgreSQL:
```shell-session
helm install my-postgres bitnami/postgresql
```
Узнайте пароль для подключения к базе данных:
```shell-session
kubectl get secret --namespace default my-postgres-postgresql -o jsonpath="{.data.postgresql-password}" | base64 --decode
```
Впишите пароль в файл [`django-app.yaml`](./django-app.yaml) в переменную `DATABASE_URL`


Включите аддон ingress в вашем кластере Minikube
```shell-session
minikube addons enable ingress
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

Запустите манифест [`django-migrate-job.yaml`](./django-migrate-job.yaml)
```shell-session
kubectl apply -f django-migrate-job.yaml
```

Запустите манифест [`django-clearsessions.yaml`](./django-clearsessions.yaml)
```shell-session
kubectl apply -f django-createsuperuser-job.yaml
```
