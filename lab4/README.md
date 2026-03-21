### Lab 4
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

#### Uruchamianie kontenera następuje poleceniem
`docker run --rm -d -p 8888:80 --name=web100 local/web100:v1`

#### Dodanie obrazu na docker hub
```
docker tag local/web100:v1 szymonk44/laby:web100-v1
docker push szymonk44/laby:web100-v1
```
[Link do repozytorium](https://hub.docker.com/repository/docker/szymonk44/laby/general)

### Lab 3

Do pliku `/etc/docker/daemon.json` dodano wpis z adresem niezabezpieczonego rejestru
```
{
  "insecure-registries" : ["localhost:5000"]
}
```

Wygenerowano certyfikat za pomocą poniższych poleceń
```
$ mkdir -p certs

$ openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key \
  -addext "subjectAltName = DNS:localhost" \
  -x509 -days 365 -out certs/domain.crt
```

Następnie utworzono kontener rejestru
```
sk@fedora:~$ docker run -d \
  --restart=always \
  --name registry \
  -v "$(pwd)"/certs:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -p 443:443 \
  registry:3
304b5004fd72e500d1924f028d1c2daf57d30c7809d3c4d11a2791943b2e5382

sk@fedora:~$ docker ps
CONTAINER ID   IMAGE                                       COMMAND                  CREATED          STATUS                 PORTS                                                                             NAMES
304b5004fd72   registry:3                                  "/entrypoint.sh /etc…"   30 seconds ago   Up 29 seconds          0.0.0.0:443->443/tcp, [::]:443->443/tcp, 5000/tcp                                 registry
```

Dodano instrukcję dla daemonów Dockera, aby ufały utworzonemu certyfikatowi<br>
```$ sudo cp ~/certs/domain.crt /etc/docker/certs.d/localhost:5000/ca.crt```

Sprawdzono czy rejestr odpowiada za szyfrowane zapytania<br>
(parametr -k ignoruje ostrzeżenie o samodzielnie podpisanym certyfikacie)
```
sk@fedora:~$ curl -k https://localhost:443/v2/_catalog
{"repositories":[]}
```

Dodano do rejestru obraz utworzony w zadaniu z lab3
```
sk@fedora:~$ docker tag local/web100:v1 localhost/web100
sk@fedora:~$ docker push localhost/web100
Using default tag: latest
The push refers to repository [localhost/web100]
3006ce712506: Pushed 
817807f3c64e: Pushed 
60fc16a99cc6: Pushed 
06945115e7e8: Pushed 
latest: digest: sha256:169962a1a996268508752c5ef7ceed534c24b9c813ce5a1b786837c73c17a61c size: 855
```

Sprawdzono zawartość rejestru
```
sk@fedora:~$ curl -k https://localhost:443/v2/_catalog
{"repositories":["web100"]}
```

Usunięto lokalnie obraz, a następnie pobrano z rejestru
```
sk@fedora:~$ docker image remove localhost/web100:latest 
Untagged: localhost/web100:latest
sk@fedora:~$ docker pull localhost/web100
Using default tag: latest
latest: Pulling from web100
Digest: sha256:169962a1a996268508752c5ef7ceed534c24b9c813ce5a1b786837c73c17a61c
Status: Downloaded newer image for localhost/web100:latest
localhost/web100:latest
```

Usunięcie obrazu z rejestru i wyczyszczenie jego śladów
```
sk@fedora:~$ curl -k -X DELETE https://localhost/v2/web100/manifests/sha256:169962a1a996268508752c5ef7ceed534c24b9c813ce5a1b786837c73c17a61c
sk@fedora:~$ docker exec -it registry bin/registry garbage-collect /etc/distribution/config.yml
...
sk@fedora:~$ docker exec -it registry rm -rf /var/lib/registry/docker/registry/v2/repositories/web100
sk@fedora:~$ curl -k https://localhost:443/v2/_catalog
{"repositories":[]}
```
