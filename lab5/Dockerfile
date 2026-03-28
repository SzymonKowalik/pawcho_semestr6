# etap 1
# Wykorzystanie obrazu "scratch" do budowy obrazu alpine
FROM scratch AS build
ADD alpine-minirootfs-3.23.3-x86_64.tar /

# Utworzenie wymaganej zmiennej
ARG VERSION=0.1

# Przeniesienie kodu html do kontenera, wraz z uzupełnieniem zmiennej $VERSION
COPY <<EOF /src/index.html.template
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Strona</title>
</head>
<body>
Adres IP: <span id=ip></span><br>
Hostname: \$HOSTNAME<br>
Wersja aplikacji: $VERSION
<script type="text/javascript">
    document.getElementById("ip").innerText = location.host;
</script>
</body>
EOF

# etap 2
# Wybór obrazu
FROM nginx:1.28-alpine

# Skopiowanie aplikacji z poprzedniego etapu
COPY --from=build /src/ /etc/nginx/templates/

# Ustawienie ścieżki dla uzupełnionionego pliku o zmienną środowiskową HOSTNAME
ENV NGINX_ENVSUBST_OUTPUT_DIR=/usr/share/nginx/html

# Informacja o porcie który jest nasłuchiwany
EXPOSE 80

# Sprawdzanie poprawności działania
HEALTHCHECK --interval=10s --timeout=1s \
    CMD curl -f http://localhost:80/ || exit 1

# Uruchomienie serwera na pierwszym planie
CMD ["nginx", "-g", "daemon off;"]
