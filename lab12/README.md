#### Utworzono plik HTML
Dostępny w obecnym katalogu pod nazwą `index.html`

#### Utworzono sieć mostkową
```
$ docker network create lab12net
cb9a64558e20ce8c96e3898be6a1f57d25076ff79e7a4c068846fdda9b73054b
```

#### Utworzono katalogi dla logów
```
$ mkdir -p ~/lab12/web1 ~/lab12/web2 ~/lab12/web3
```

#### Uruchomiono kontenery
```
$ docker run -d --rm --name web1 \
  -p 8081:80 \
  --network lab12net \
  --mount type=bind,source=$PWD/index.html,target=/usr/share/nginx/html/index.html,readonly \
  --mount type=bind,source=/home/sk/lab12/web1,target=/var/log/nginx \
  nginx:latest
cb7c26fcb80bd30fcd73a63323d8f58ef96edb59e15eb37045151508f19abc95

$ docker run -d --rm --name web2 \
  -p 8082:80 \
  --network lab12net \
  --mount type=bind,source=$PWD/index.html,target=/usr/share/nginx/html/index.html,readonly \
  --mount type=bind,source=/home/sk/lab12/web2,target=/var/log/nginx \
  nginx:latest
53ebb49bdd40108e329ae28878497ba29c73457cbb28adb996b40732f35126ce


$ docker run -d --rm --name web3 \
  -p 8083:80 \
  --network lab12net \
  --mount type=bind,source=$PWD/index.html,target=/usr/share/nginx/html/index.html,readonly \
  --mount type=bind,source=/home/sk/lab12/web3,target=/var/log/nginx \
  nginx:latest
22bab0e537490205f4361f6820e8db1d76537020f6e4815bd9e3515f27ab1628
```

#### Sprawdzono działanie kontenerów
```
$ curl localhost:8081
<!DOCTYPE html>
<html lang="pl">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lab12</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
        Laboratorium 12
        Szymon Kowalik
  </body>
</html>

$ curl localhost:8082
<!DOCTYPE html>
<html lang="pl">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lab12</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
        Laboratorium 12
        Szymon Kowalik
  </body>
</html>

$ curl localhost:8083
<!DOCTYPE html>
<html lang="pl">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lab12</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
        Laboratorium 12
        Szymon Kowalik
  </body>
</html>
```
#### Sprawdzono zawartość logów
```
$ tree ~/lab12
/home/sk/lab12
├── web1
│   ├── access.log
│   └── error.log
├── web2
│   ├── access.log
│   └── error.log
└── web3
    ├── access.log
    └── error.log

4 directories, 6 files

$ NAME=web1; cat ~/lab12/$NAME/access.log && cat ~/lab12/$NAME/error.log
192.168.32.1 - - [03/Jun/2026:12:55:16 +0000] "GET / HTTP/1.1" 200 298 "-" "curl/8.15.0" "-"
2026/06/03 12:51:54 [notice] 1#1: using the "epoll" event method
2026/06/03 12:51:54 [notice] 1#1: nginx/1.29.7
2026/06/03 12:51:54 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2026/06/03 12:51:54 [notice] 1#1: OS: Linux 6.19.14-200.fc43.x86_64
2026/06/03 12:51:54 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1024:1048576
2026/06/03 12:51:54 [notice] 1#1: start worker processes
2026/06/03 12:51:54 [notice] 1#1: start worker process 29
2026/06/03 12:51:54 [notice] 1#1: start worker process 30
2026/06/03 12:51:54 [notice] 1#1: start worker process 31
2026/06/03 12:51:54 [notice] 1#1: start worker process 32
2026/06/03 12:51:54 [notice] 1#1: start worker process 33
2026/06/03 12:51:54 [notice] 1#1: start worker process 34
2026/06/03 12:51:54 [notice] 1#1: start worker process 35
2026/06/03 12:51:54 [notice] 1#1: start worker process 36
2026/06/03 12:51:54 [notice] 1#1: start worker process 37
2026/06/03 12:51:54 [notice] 1#1: start worker process 38
2026/06/03 12:51:54 [notice] 1#1: start worker process 39
2026/06/03 12:51:54 [notice] 1#1: start worker process 40

$ NAME=web2; cat ~/lab12/$NAME/access.log && cat ~/lab12/$NAME/error.log
192.168.32.1 - - [03/Jun/2026:12:55:17 +0000] "GET / HTTP/1.1" 200 298 "-" "curl/8.15.0" "-"
2026/06/03 12:52:40 [notice] 1#1: using the "epoll" event method
2026/06/03 12:52:40 [notice] 1#1: nginx/1.29.7
2026/06/03 12:52:40 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2026/06/03 12:52:40 [notice] 1#1: OS: Linux 6.19.14-200.fc43.x86_64
2026/06/03 12:52:40 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1024:1048576
2026/06/03 12:52:40 [notice] 1#1: start worker processes
2026/06/03 12:52:40 [notice] 1#1: start worker process 29
2026/06/03 12:52:40 [notice] 1#1: start worker process 30
2026/06/03 12:52:40 [notice] 1#1: start worker process 31
2026/06/03 12:52:40 [notice] 1#1: start worker process 32
2026/06/03 12:52:40 [notice] 1#1: start worker process 33
2026/06/03 12:52:40 [notice] 1#1: start worker process 34
2026/06/03 12:52:40 [notice] 1#1: start worker process 35
2026/06/03 12:52:40 [notice] 1#1: start worker process 36
2026/06/03 12:52:40 [notice] 1#1: start worker process 37
2026/06/03 12:52:40 [notice] 1#1: start worker process 38
2026/06/03 12:52:40 [notice] 1#1: start worker process 39
2026/06/03 12:52:40 [notice] 1#1: start worker process 40

$ NAME=web3; cat ~/lab12/$NAME/access.log && cat ~/lab12/$NAME/error.log
192.168.32.1 - - [03/Jun/2026:12:55:18 +0000] "GET / HTTP/1.1" 200 298 "-" "curl/8.15.0" "-"
2026/06/03 12:52:47 [notice] 1#1: using the "epoll" event method
2026/06/03 12:52:47 [notice] 1#1: nginx/1.29.7
2026/06/03 12:52:47 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2026/06/03 12:52:47 [notice] 1#1: OS: Linux 6.19.14-200.fc43.x86_64
2026/06/03 12:52:47 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1024:1048576
2026/06/03 12:52:47 [notice] 1#1: start worker processes
2026/06/03 12:52:47 [notice] 1#1: start worker process 29
2026/06/03 12:52:47 [notice] 1#1: start worker process 30
2026/06/03 12:52:47 [notice] 1#1: start worker process 31
2026/06/03 12:52:47 [notice] 1#1: start worker process 32
2026/06/03 12:52:47 [notice] 1#1: start worker process 33
2026/06/03 12:52:47 [notice] 1#1: start worker process 34
2026/06/03 12:52:47 [notice] 1#1: start worker process 35
2026/06/03 12:52:47 [notice] 1#1: start worker process 36
2026/06/03 12:52:47 [notice] 1#1: start worker process 37
2026/06/03 12:52:47 [notice] 1#1: start worker process 38
2026/06/03 12:52:47 [notice] 1#1: start worker process 39
2026/06/03 12:52:47 [notice] 1#1: start worker process 40
```

