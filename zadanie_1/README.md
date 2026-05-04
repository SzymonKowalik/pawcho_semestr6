### Zadanie 1 - Obowiązkowe

Obraz budowany jest na podstawie pliku `Dockerfile` oraz kodu źródłowego zawartego w katalogu `code` które znajdują się w obecnym katalogu.

Plik Dockerfile posiada argument ARG `TZ` informujący program w C o strefie czasowej do wyświetlania logów. Domyślnie ustawiony jest na strefę czasową: CET - czas zimowy, CEST - czas letni.

Przy uruchamianiu obrazu można podać zmienną środowiskową `DISPLAY_PORT`, która wykorzystana jest do nadpisania wewnętrznego portu w logach serwera.

W celu uzyskania jak najmniejszego rozmiaru obrazu zastosowano wieloetapowe budowanie na podstawie obrazu `scratch`. W procesie kompilacji ustawiono flagi kompilatora, `-Os`, `-ffunction-sections` oraz `-fdata-sections` w celu optymalizacji pod kątem wielkości binarnej, `-Wl,--gc-sections -s` w celu usuwania nieużywanych symboli podczas linkowania statycznego. Na końcu plik binarny został poddany maksymalnej kompresji za pomocą narzędzia UPX.

#### Budowanie obrazu kontenera
```
$docker build --tag szymonk44/laby:zadanie1-obowiazkowe .
$docker push szymonk44/laby:zadanie1-obowiazkowe
```

#### Uruchomienie kontenera
Kontener uruchomiono w trybie detatched na porcie 8111, z opcją usuwania kontenera po wyłączeniu.
```
$docker run --rm -d -p 8111:8000 -e DISPLAY_PORT=8111 --name zadanie1 szymonk44/laby:zadanie1-obowiazkowe
977f70b57b8af1ff084d62a326096f0fa49869103c874e4c3f5ffcaf9bbd8bb3
```

#### Informacje z logów
```
$ docker logs zadanie1 
631aff 3 mongoose.c:6335:mg_mgr_init    MG_IO_SIZE: 16384, TLS: none
[2026-05-04 13:34:49] Start time: 2026-05-04 13:34:49
[2026-05-04 13:34:49] Server started on outside port 8111
[2026-05-04 13:34:49] Author: Szymon Kowalik
631aff 3 mongoose.c:6252:mg_listen      1 4 http://0.0.0.0:8000
637ae7 3 mongoose.c:12577:accept_conn   2 5 accepted 172.17.0.1:51984 -> 172.17.0.3:8000
637ae7 3 mongoose.c:12427:read_conn     2 5 0:0:0 558 err 0
[2026-05-04 13:35:14] GET /
637ae7 3 mongoose.c:12438:write_conn    2 5 snd 58/16384 rcv 0/16384 n=58 err=0
637b38 3 mongoose.c:12427:read_conn     2 5 0:0:0 490 err 0
[2026-05-04 13:35:14] GET /api/locations
```

#### Sprawdzenie liczby warstw
```
$ docker history szymonk44/laby:zadanie1-obowiazkowe | grep -v 0B
IMAGE          CREATED              CREATED BY                                      SIZE      COMMENT
<missing>      About a minute ago   COPY /app/static /app/static # buildkit         12.3kB    buildkit.dockerfile.v0
<missing>      About a minute ago   COPY /app/build/weatherApp /app/server # bui…   45.1kB    buildkit.dockerfile.v0
<missing>      14 hours ago         COPY /etc/group /etc/group # buildkit           4.1kB     buildkit.dockerfile.v0
<missing>      14 hours ago         COPY /etc/passwd /etc/passwd # buildkit         4.1kB     buildkit.dockerfile.v0
```

Obraz posiada 4 warstwy.


#### Sprawdzenie rozmiaru obrazu
```
$ docker images szymonk44/laby:zadanie1-obowiazkowe
IMAGE                                 ID             DISK USAGE   CONTENT SIZE   EXTRA
szymonk44/laby:zadanie1-obowiazkowe   ade2567e1260        117kB         51.8kB    U   
```

