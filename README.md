# Otus_13 Docker

## Задание:

1.  Установите Docker на хост машину https://docs.docker.com/engine/install/ubuntu/
2.  Установите Docker Compose - как плагин, или как отдельное приложение
3.  Создайте свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx)
4.  Определите разницу между контейнером и образом. Вывод опишите в домашнем задании.
5.  Ответьте на вопрос: Можно ли в контейнере собрать ядро?
6.  Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий.

## Решение:
## 1. Установка Docker на хост машину по иснтрукции https://docs.docker.com/engine/install/ubuntu/
## 2. Установка Docker Compose - как плагин

Выбираю метод установки через apt repository

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
```

Проверка установки:
```
root@2467304-nbservice:~# sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
c1ec31eb5944: Pull complete
Digest: sha256:4bd78111b6914a99dbc560e6a20eab57ff6655aea4a80c50b0c5491968cbc2e6
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## 3. Создание своего кастомного образа nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу.

Создаем папку для проекта

```
mkdir custom_nginx
cd custom_nginx
```

Создаем Dockerfile

```
FROM nginx:alpine
COPY custom-index.html /usr/share/nginx/html/index.html
```

Создаем custom-index.html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Custom Nginx Page</title>
</head>
<body>
    <h1>Welcome to My Custom Nginx Page!</h1>
    <p>This is a custom page served by Nginx.</p>
</body>
</html>
```

Создаем образ

```
docker build -t custom_nginx_image .
```

Запускаем контейнер

```
docker run -p 8080:80 custom_nginx_image
````

Проверяем:
```
root@2467304-nbservice:~# curl http://localhost:8080
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Custom Nginx Page</title>
</head>
<body>
    <h1>Welcome to My Custom Nginx Page!</h1>
    <p>This is a custom page served by Nginx.</p>
</body>
</html>
```

![](https://github.com/Sunabak/Otus_13/blob/main/nginx.JPG)

## 4 Pазницу между контейнером и образом:

В Docker "образ" (image) представляет собой статичный и немодифицируемый шаблон, который содержит все необходимое для запуска приложения, включая код, библиотеки, зависимости и настройки. Образ является основой для создания контейнеров.


"Контейнер" (container)- это экземпляр запущенного образа. Контейнер предоставляет изолированное окружение для приложения, выполняющегося на хост-системе. Он использует образ в качестве основы, но при этом может изменяться и сохранять свое состояние в процессе выполнения.


Таким образом, основное различие заключается в том, что образ является статичным и неизменным, в то время как контейнер предоставляет исполняемую среду на основе образа, которая может быть изменена и сохранена во время выполнения.


## 5 Можно ли в контейнере собрать ядро?

Да, можно собрать ядро в контейнере Docker. 
Примеры и инструкции по сборке ядра в контейнере можно найти в репозиториях на GitHub, таких как [docker-kernel-builder](https://github.com/diemol/docker-kernel-builder) или [docker-linux-kernel](https://github.com/kubermatic/docker-linux-kernel).

## 6 Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий.

Залогиниться в Doker hub
```
root@2467304-nbservice:~# docker login
Log in with your Docker ID or email address to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com/ to create one.
You can log in with your password or a Personal Access Token (PAT). Using a limited-scope PAT grants better security and is required for organizations using SSO. Learn more at https://docs.docker.com/go/access-tokens/

Username: sunabak
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

Переименование локального образа для загрузки в Docker hub
```
root@2467304-nbservice:~# docker tag b1536cbbb1de sunabak/custom_nginx_image:ver1
root@2467304-nbservice:~# docker images
REPOSITORY                   TAG       IMAGE ID       CREATED        SIZE
custom_nginx_image           latest    b1536cbbb1de   11 hours ago   42.6MB
sunabak/custom_nginx_image   ver1      b1536cbbb1de   11 hours ago   42.6MB
```

[ссылка на образ в Docker hub](https://hub.docker.com/r/sunabak/custom_nginx_image)
