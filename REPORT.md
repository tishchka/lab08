## Laboratory work VIII

Данная лабораторная работа посвещена изучению систем автоматизации развёртывания и управления приложениями на примере **Docker**

```sh
$ open https://docs.docker.com/get-started/
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab08** на сервисе **GitHub**
- [x] 2. Ознакомиться со ссылками учебного материала
- [x] 3. Выполнить инструкцию учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
# Устанавливаем значение переменной окружения GITHUB_USERNAME
$ export GITHUB_USERNAME=tishchka
$ alias gsed=sed
$ alias edit=subl
```

```sh
# Вход в директорию workspace
$ cd ${GITHUB_USERNAME}/workspace
# Запоминаем текущий каталог в виртуальном стеке каталогов
$ pushd .
# Исполняем команду из файла scripts/activate
$ source scripts/activate
```

```sh
# Копируем файлы lab07 из удаленного репозитория github в папку projects/lab08
$ git clone https://github.com/${GITHUB_USERNAME}/lab07 projects/lab08
# Переход в директорию lab08 
$ cd projects/lab08
# Настраиваем git-репозитория для работы
$ git submodule update --init
# Удаление ссылки на старый удаленный репозиторий
$ git remote remove origin
# Добавляем ссылку на новый репозиторий
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab08
```

```sh
# Создаем Dockerfile. В самом начале указываем базовый образ, который определяет рабочую среду, пакеты и утилиты.
$ cat > Dockerfile <<EOF
FROM ubuntu:18.04
EOF
```

```sh
# Указываем пакеты, которые должны установиться 
$ cat >> Dockerfile <<EOF

RUN apt update
RUN apt install -yy gcc g++ cmake
EOF
```

```sh
# Копируем файлы из текущего каталога и задаем рабочую директорию для docker
$ cat >> Dockerfile <<EOF

COPY . print/
WORKDIR print
EOF
```

```sh
# Собираем приложение с помощью CMake
$ cat >> Dockerfile <<EOF

RUN cmake -H. -B_build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=_install
RUN cmake --build _build
RUN cmake --build _build --target install
EOF
```

```sh
# Устанавливаем значение переменной LOG_PATH 
$ cat >> Dockerfile <<EOF

ENV LOG_PATH /home/logs/log.txt
EOF
```

```sh
# Указываем в какой директории будут храниться файлы, которые останутся после работы с контейнером
$ cat >> Dockerfile <<EOF

VOLUME /home/logs
EOF
```

```sh
# Переход в созданную в процессе сборки директорию _install/bin
$ cat >> Dockerfile <<EOF

WORKDIR _install/bin
EOF
```

```sh
# Инструкция ENTRYPOINT позволяет задавать команду с аргументами, которая должна выполняться при запуске контейнера. 
$ cat >> Dockerfile <<EOF

ENTRYPOINT ./demo
EOF
```

```sh
# Сборка образа с тегом `logger` и путем к Dockerfile
$ sudo docker build -t logger .
```

```sh
# Выводим информацию о существующих образах
$ sudo docker images
```

```sh
# Создаем директорию logs
$ mkdir logs
# Создаем интерактивный процесс logger, 
# в который передаем следующий текст
$ sudo docker run -it -v "$(pwd)/logs/:/home/logs/" logger
text1
text2
text3
<C-D>
```

```sh
# Вывод подробной информации о контейнере
$ sudo docker inspect logger
```

```sh
# Вывод содержимого файла, полученного в результате работы процесса
$ cat logs/log.txt
```

```sh
# Изменяем иконку тревиса
$ gsed -i 's/lab07/lab08/g' README.md
```

```sh
# Изменение .travis.yml для сборки в контейнере.
$ vim .travis.yml
/lang<CR>o
services:
- docker<ESC>
jVGdo
script:
- docker build -t logger .<ESC>
:wq
```

```sh
# Добавляем файлы в коммит
$ git add Dockerfile
$ git add .travis.yml
$ git commit -m"adding Dockerfile"
# Отправляем на сервер
$ git push origin main
```

```sh
$ travis login --auto
$ travis enable
```

## Report

```sh
$ popd
$ export LAB_NUMBER=08
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Links

- [Book](https://www.dockerbook.com)
- [Instructions](https://docs.docker.com/engine/reference/builder/)

```
Copyright (c) 2015-2021 The ISC Authors
```
