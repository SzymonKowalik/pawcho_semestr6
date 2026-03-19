#### Opis zmian w pliku Dockerfile
Całkowicie zmieniono utworzony plik Dockerfile aby zachować przejrzystość.
Utworzono zmienne środowiskowe niezbędne do uruchomienia serwera apache.
Zgodnie z dobrymi praktykami m.in. połączono komendy w jedno poleceniu RUN, 
opisano który port jest nasłuchiwany za pomocą EXPOSE.

#### Budowanie obrazu z pliku Dockerfile
`docker buildx build --tag local/web100:v1 .`

#### Sprawdzanie ilości warstw za pomocą polecenia
```
$ docker history local/web100:v1 | grep -v 0B
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
<missing>      6 minutes ago   COPY index.html /var/www/html/ # buildkit       4.1kB     buildkit.dockerfile.v0
<missing>      6 minutes ago   RUN /bin/sh -c apt update -y &&     apt inst…   116MB     buildkit.dockerfile.v0
<missing>      3 weeks ago     /bin/sh -c #(nop) ADD file:3f78aa860931e0853…   85.7MB    
```
Liczba warstw: 3 (wartość SIZE różna od 0B)

#### Uruchamianie kontenera następuje poleceniem:
`docker run --rm -d -p 8888:80 --name=web100 local/web100:v1`

#### Dodanie obrazu na docker hub
```
docker tag local/web100:v1 szymonk44/laby:web100-v1
docker push szymonk44/laby:web100-v1
```
[Link do repozytorium](https://hub.docker.com/repository/docker/szymonk44/laby/general)
