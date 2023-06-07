# lab08
## Laboratory work VIII

Данная лабораторная работа посвещена изучению систем автоматизации развёртывания и управления приложениями на примере **Docker**

```sh
$ open https://docs.docker.com/get-started/
```

## Tasks

- [ ] 1. Создать публичный репозиторий с названием **lab08** на сервисе **GitHub**
- [ ] 2. Ознакомиться со ссылками учебного материала
- [ ] 3. Выполнить инструкцию учебного материала
- [ ] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
$ export GITHUB_USERNAME=<имя_пользователя>
```

```
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab07 lab08
$ cd lab08
$ git submodule update --init
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab08
```

```sh
$ cat > Dockerfile <<EOF
FROM ubuntu:18.04
EOF
```

```sh
$ cat >> Dockerfile <<EOF

RUN apt update
RUN apt install -yy gcc g++ cmake
EOF
```

```sh
$ cat >> Dockerfile <<EOF

COPY . print/
WORKDIR print
EOF
```

```sh
$ cat >> Dockerfile <<EOF

RUN cmake -H. -B_build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=_install
RUN cmake --build _build
RUN cmake --build _build --target install
EOF
```

```sh
$ cat >> Dockerfile <<EOF

ENV LOG_PATH /home/logs/log.txt
EOF
```

```sh
$ cat >> Dockerfile <<EOF

VOLUME /home/logs
EOF
```

```sh
$ cat >> Dockerfile <<EOF

WORKDIR _install/bin
EOF
```

```sh
$ cat >> Dockerfile <<EOF

ENTRYPOINT ./demo
EOF
```

```sh
$ docker build -t logger .
```

```sh
$ docker images
```

```sh
$ mkdir logs
$ docker run -it -v "$(pwd)/logs/:/home/logs/" logger
text1
text2
text3
<C-D>
```

```sh
$ docker inspect logger
```

```sh
$ cat logs/log.txt
```

```sh
$ gsed -i 's/lab07/lab08/g' README.md
```

```sh
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
$ git add Dockerfile
$ git add .travis.yml
$ git commit -m"adding Dockerfile"
$ git push origin master
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
## HOMEWORK
```sh
$ mkdir lab08
$ cd lab08
$ git init 
$ git remote add origin https://github.com/Alinoos/lab08
$ mkdir include
$ cd include
$ cd include
$ cat >> print.hpp << EOF
>EOF
$ nano print.hpp

	Содержимое файла print.hpp:

#include <fstream>
#include <iostream>
#include <string>

void print(const std::string& text, std::ofstream& out);
void print(const std::string& text, std::ostream& out = std::cout);

$cd ..
$ mkdir source
$ cd source
$ cat >> print.cpp << EOF
>EOF
$ nano print.cpp
      Содержимое файла print.hpp:
#include <print.hpp>

void print(const std::string& text, std::ostream& out)
{
  out << text;
}

void print(const std::string& text, std::ofstream& out)
{
  out << text;
}


$cd ..

$ mkdir demo
$ cd demo
$ cat >> main.cpp << EOF
>EOF
$ nano main.cpp

	Содержимое файла main.cpp:

#include <print.hpp>
#include <cstdlib>

int main(int argc, char* argv[])
{
  const char* log_path = std::getenv("LOG_PATH");
  if (log_path == nullptr)
  {
    std::cerr << "undefined environment variable: LOG_PATH" << std::endl;
    return 1;
  }
  std::string text;
  while (std::cin >> text)
  {
    std::ofstream out{log_path, std::ios_base::app};
    print(text, out);
    out << std::endl;
  }
}


$ cd ..
$ mkdir logs
$ cd logs
$ cat >> log.txt << EOF
>EOF
$ nano log.txt

	Содержимое файла log.txt:

simple text

$ cd ..
$ git add .
$ git commit -m "Code files - 1"
[master (корневой коммит) c55e2e2] Code files - 1
 4 files changed, 37 insertions(+)
 create mode 100644 demo/main.cpp
 create mode 100644 include/print.hpp
 create mode 100644 logs/log.txt
 create mode 100644 source/print.cpp
$ git push origin master
Username for 'https://github.com': Alinoos
Password for 'https://Alinoos@github.com': 
Перечисление объектов: 10, готово.
Подсчет объектов: 100% (10/10), готово.
При сжатии изменений используется до 8 потоков
Сжатие объектов: 100% (5/5), готово.
Запись объектов: 100% (10/10), 948 байтов | 948.00 КиБ/с, готово.
Всего 10 (изменений 0), повторно использовано 0 (изменений 0), повторно использовано пакетов 0
remote: 
remote: Create a pull request for 'master' on GitHub by visiting:
remote:      https://github.com/Alinoos/lab08/pull/new/master
remote: 
To https://github.com/Alinoos/lab08
 * [new branch]      master -> master

$ cat >> CMakeLists.txt << EOF
>EOF
$ nano CMakeLists.txt 

	Содержимое файла CMakeLists.txt:

cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)

project(print)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/source/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME}
      RUNTIME DESTINATION bin
    )
  endforeach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
endif()

install(TARGETS print
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)


$ cat >> Dockerfile << EOF
>EOF
$ nano Dockerfile 

	Содержимое файла Dockerfile:

FROM ubuntu:20.04

RUN apt update
RUN apt install -yy gcc g++ cmake

COPY . print/
WORKDIR print

RUN cmake -H. -B_build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=_install
RUN cmake --build _build
RUN cmake --build _build --target install

ENV LOG_PATH /home/lab-07/logs/log.txt
VOLUME /home/lab-07/logs

WORKDIR _install/bin
ENTRYPOINT ./demo

$ git add .
$ git commit -m "CMake / Docker - 1"
[master 4a6203a] CMake / Docker - 1
 2 files changed, 54 insertions(+)
 create mode 100644 CMakeLists.txt
 create mode 100644 Dockerfile
 
$ git push origin master
Username for 'https://github.com': Alinoos
Password for 'https://Alinoos@github.com': 
Перечисление объектов: 5, готово.
Подсчет объектов: 100% (5/5), готово.
При сжатии изменений используется до 8 потоков
Сжатие объектов: 100% (4/4), готово.
Запись объектов: 100% (4/4), 1.10 КиБ | 1.10 МиБ/с, готово.
Всего 4 (изменений 0), повторно использовано 0 (изменений 0), повторно использовано пакетов 0
To https://github.com/Alinoos/lab08
   c55e2e2..4a6203a  master -> master
 
$ nano CMakeLists.txt 
Содержимое файла CMakeLists.txt:

cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)

project(print)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/source/print.cpp)

#добавленные строки

add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/demo/main.cpp)
target_link_libraries(demo print) 
install(TARGETS demo RUNTIME DESTINATION bin)

#конец

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME}
      RUNTIME DESTINATION bin
    )
  endforeach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
endif()

install(TARGETS print
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)


$ git add Dockerfile 
$ git add .
$ git commit -m "docker"
[master b23e8e9] docker
 1 file changed, 6 insertions(+)

$ git push origin master
Username for 'https://github.com': Alinoos
Password for 'https://Alinoos@github.com': 
Перечисление объектов: 5, готово.
Подсчет объектов: 100% (5/5), готово.
При сжатии изменений используется до 8 потоков
Сжатие объектов: 100% (3/3), готово.
Запись объектов: 100% (3/3), 398 байтов | 398.00 КиБ/с, готово.
Всего 3 (изменений 2), повторно использовано 0 (изменений 0), повторно использовано пакетов 0
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/Alinoos/lab08
   4a6203a..b23e8e9  master -> master

$ git commit -m "CMake - 2"
$ git push origin master
$ git push origin master
Username for 'https://github.com': Alinoos
Password for 'https://Alinoos@github.com': 
Everything up-to-date

$ mkdir .github
$ cd ~/lab08/.github
$ mkdir workflows
$ cd ~/lab08/.github/workflows
$ cat >> Action.yml << EOF
>EOF
$ nano Action.yml

	Содержимое файла Action.yml:

name: docker
on:
  push:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: build docker
        run: docker build -t logger .
	
	
$ git add Action.yml
$ git commit -m "Action"
[master 150e5e6] Action
 1 file changed, 11 insertions(+)
 create mode 100644 .github/workflows/Action.yml

$ git push origin master
Username for 'https://github.com': Alinoos
Password for 'https://Alinoos@github.com': 
Перечисление объектов: 6, готово.
Подсчет объектов: 100% (6/6), готово.
При сжатии изменений используется до 8 потоков
Сжатие объектов: 100% (3/3), готово.
Запись объектов: 100% (5/5), 470 байтов | 470.00 КиБ/с, готово.
Всего 5 (изменений 1), повторно использовано 0 (изменений 0), повторно использовано пакетов 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/Alinoos/lab08
   b23e8e9..150e5e6  master -> master

$ cd
$ cd lab08
$ sudo apt install docker.io
[sudo] пароль для alina: 
Чтение списков пакетов… Готово
Построение дерева зависимостей… Готово
Чтение информации о состоянии… Готово         
Будут установлены следующие дополнительные пакеты:
  bridge-utils containerd pigz runc ubuntu-fan
Предлагаемые пакеты:
  ifupdown aufs-tools btrfs-progs cgroupfs-mount | cgroup-lite debootstrap docker-doc rinse
  zfs-fuse | zfsutils
Следующие НОВЫЕ пакеты будут установлены:
  bridge-utils containerd docker.io pigz runc ubuntu-fan
Обновлено 0 пакетов, установлено 6 новых пакетов, для удаления отмечено 0 пакетов, и 228 пакетов не обновлено.
Необходимо скачать 72,1 MB архивов.
После данной операции объём занятого дискового пространства возрастёт на 286 MB.
Хотите продолжить? [Д/н] y
Пол:1 http://ru.archive.ubuntu.com/ubuntu jammy/universe amd64 pigz amd64 2.6-1 [63,6 kB]
Пол:2 http://ru.archive.ubuntu.com/ubuntu jammy/main amd64 bridge-utils amd64 1.7-1ubuntu3 [34,4 kB]
Пол:3 http://ru.archive.ubuntu.com/ubuntu jammy-updates/main amd64 runc amd64 1.1.4-0ubuntu1~22.04.3 [4 244 kB]
Пол:4 http://ru.archive.ubuntu.com/ubuntu jammy-updates/main amd64 containerd amd64 1.6.12-0ubuntu1~22.04.1 [34,4 MB]
Пол:5 http://ru.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 docker.io amd64 20.10.21-0ubuntu1~22.04.3 [33,3 MB]
Пол:6 http://ru.archive.ubuntu.com/ubuntu jammy/universe amd64 ubuntu-fan all 0.12.16 [35,2 kB]
Получено 72,1 MB за 6с (11,6 MB/s)                                                           
Предварительная настройка пакетов …
Выбор ранее не выбранного пакета pigz.
(Чтение базы данных … на данный момент установлено 233144 файла и каталога.)
Подготовка к распаковке …/0-pigz_2.6-1_amd64.deb …
Распаковывается pigz (2.6-1) …
Выбор ранее не выбранного пакета bridge-utils.
Подготовка к распаковке …/1-bridge-utils_1.7-1ubuntu3_amd64.deb …
Распаковывается bridge-utils (1.7-1ubuntu3) …
Выбор ранее не выбранного пакета runc.
Подготовка к распаковке …/2-runc_1.1.4-0ubuntu1~22.04.3_amd64.deb …
Распаковывается runc (1.1.4-0ubuntu1~22.04.3) …
Выбор ранее не выбранного пакета containerd.
Подготовка к распаковке …/3-containerd_1.6.12-0ubuntu1~22.04.1_amd64.deb …
Распаковывается containerd (1.6.12-0ubuntu1~22.04.1) …
Выбор ранее не выбранного пакета docker.io.
Подготовка к распаковке …/4-docker.io_20.10.21-0ubuntu1~22.04.3_amd64.deb …
Распаковывается docker.io (20.10.21-0ubuntu1~22.04.3) …
Выбор ранее не выбранного пакета ubuntu-fan.
Подготовка к распаковке …/5-ubuntu-fan_0.12.16_all.deb …
Распаковывается ubuntu-fan (0.12.16) …
Настраивается пакет runc (1.1.4-0ubuntu1~22.04.3) …
Настраивается пакет bridge-utils (1.7-1ubuntu3) …
Настраивается пакет pigz (2.6-1) …
Настраивается пакет containerd (1.6.12-0ubuntu1~22.04.1) …
Created symlink /etc/systemd/system/multi-user.target.wants/containerd.service → /lib/systemd/
system/containerd.service.
Настраивается пакет ubuntu-fan (0.12.16) …
Created symlink /etc/systemd/system/multi-user.target.wants/ubuntu-fan.service → /lib/systemd/
system/ubuntu-fan.service.
Настраивается пакет docker.io (20.10.21-0ubuntu1~22.04.3) …
Добавляется группа «docker» (GID 137) ...
Готово.
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /lib/systemd/syst
em/docker.service.
Created symlink /etc/systemd/system/sockets.target.wants/docker.socket → /lib/systemd/system/d
ocker.socket.
Обрабатываются триггеры для man-db (2.10.2-1) …


$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
719385e32844: Pull complete 
Digest: sha256:fc6cf906cbfa013e80938cdf0bb199fbdbb86d6e3e013783e5a766f50f5dbce0
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

$ sudo docker build -t logger .
//прикреплю скрины, чтобы вывод не занял много места
![изображение](https://github.com/Alinoos/lab08/assets/126507425/88195b04-67cf-4086-b729-ae9caf87a490)
![изображение](https://github.com/Alinoos/lab08/assets/126507425/0099e12f-c7ea-4684-9d76-6a7ff5e63387)
![изображение](https://github.com/Alinoos/lab08/assets/126507425/ec888f08-e28a-4810-8e0b-e5b92b5152e8)
![изображение](https://github.com/Alinoos/lab08/assets/126507425/a55610b2-346d-4ceb-a6ba-cd1b5f9823af)
![изображение](https://github.com/Alinoos/lab08/assets/126507425/1a28b812-2002-42b7-a171-149d751bdcf4)

$ sudo docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
logger        latest    68a01bd58dce   2 minutes ago   388MB
hello-world   latest    9c7a54a9a43c   4 weeks ago     13.3kB
ubuntu        20.04     88bd68917189   7 weeks ago     72.8MB

$ sudo docker run -it -v "$(pwd)/logs/:/home/lab-08/logs/" logger
hello, world
привет, мир

$ sudo docker inspect logger
//также прикрепляю скрины
![изображение](https://github.com/Alinoos/lab08/assets/126507425/c50906e2-6730-49eb-b796-5da7e2554cf7)
![изображение](https://github.com/Alinoos/lab08/assets/126507425/56e9f976-5f84-47df-b22d-5787ae4581d9)

$ cat logs/log.txt
simple text

```
## Links

- [Book](https://www.dockerbook.com)
- [Instructions](https://docs.docker.com/engine/reference/builder/)

```
Copyright (c) 2015-2021 The ISC Authors
```
