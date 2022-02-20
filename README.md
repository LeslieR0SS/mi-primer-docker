# Mi primer Docker :)
Práctica de Sistemas Informáticos con Docker. En la cuál vamos a crear un contenedor en Docker con un sesrvidor Apache+PhpP

---

### 1. Preparar el directorio donde copiaremos los archivos _locales_ al _contenedor_
<img width="670" alt="image" src="https://user-images.githubusercontent.com/91556467/154111342-8809bcee-ec6f-4f50-b289-6f62e2759f2f.png">

###### Dockerfile
```Dockerfile
# we will inherit from  the Debian image on DockerHub
FROM debian

# set timezone so files' timestamps are correct
ENV TZ=Europe/Madrid

# install apache and php 7.3
# we include procps and telnet so you can use these with shell.sh prompt
RUN apt-get update -qq >/dev/null && apt-get install -y -qq procps telnet apache2 php7.4 -qq >/dev/null

# HTML server directory
WORKDIR /var/www/html
COPY . /var/www/html/


# The PHP app is going to save its state in /data so we make a /data inside the container
RUN mkdir /data && chown -R www-data /data && chmod 755 /data & chmod 775 -R /var/www/html/

# we need custom php configuration file to enable userdirs
COPY php.conf /etc/apache2/mods-available/php7.3.conf

# enable userdir and php
RUN a2enmod php7.4

# we run a script to stat the server; the array syntax makes it so ^C will work as we want
CMD  ["./entrypoint.sh"]
```

### 2. Ejecutar `build.sh` para montar el _contenedor de Docker_
<img width="949" alt="image" src="https://user-images.githubusercontent.com/91556467/154109200-22cca8e7-5025-421f-9901-a071235f8d89.png">

###### build.sh
```sh
#!/bin/sh

# build.sh

# we use the "docker build" command to build a container named "chapter2" from . (current directory)
# Dockerfile is found in the current directory, and determines how the conatiner is built.

docker build -t chapter2 .
```

### 3. Ejecutar `debug.sh` para iniciar el _contenedor_
<img width="941" alt="image" src="https://user-images.githubusercontent.com/91556467/154110315-21785c14-7f86-44fe-8821-6c89be874593.png">

###### debug.sh
```sh
#!/usr/bin/env bash

# debug.sh

# run container without making it a daemon - useful to see logging output

docker run \
    --rm \
    -p8086:80 \
    --name="chapter2" \
    -v ${pwd}:/home/app \
    chapter2
```

#### 4. Acceder al servidor PhP _hosteado_ con el contenedor de Docker
<img width="258" alt="image" src="https://user-images.githubusercontent.com/91556467/154111160-8104e7f4-547e-4dc8-896b-4e71c311ba3c.png">
