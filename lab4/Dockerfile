# Base image to run from
FROM ubuntu:latest

# Add author information
LABEL maintainer="Szymon Kowalik s101601@pollub.edu.pl"

# Configure required apache variables
ENV APACHE_RUN_DIR=/var/run/apache2
ENV APACHE_RUN_USER=www-data
ENV APACHE_RUN_GROUP=www-data
ENV APACHE_PID_FILE=/var/run/apache2/apache2.pid
ENV APACHE_LOG_DIR=/var/log/apache2

# Create a stage for building/compiling the application.
RUN apt update -y && \
    apt install -y apache2 && \
    rm -rf /var/lib/apt/lists/* && \
    mkdir -p $APACHE_RUN_DIR

# Inform about port container listes on
EXPOSE 80/tcp

# Copy webpage
COPY index.html /var/www/html/

# Set PID 1 as apache server
ENTRYPOINT [ "apache2", "-D", "FOREGROUND" ]
