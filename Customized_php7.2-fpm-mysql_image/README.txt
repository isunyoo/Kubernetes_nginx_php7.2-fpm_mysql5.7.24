

*** Build docker image with mysql modules ***

$ cd /home/syoo/Documents/GCloud/K8S(Kubernetes)/blackhole-k8s/Customrized_php7.2-fpm_image
$ docker build -t php:7.2-fpm-mysql .

$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
php                 7.2-fpm-mysql       69544c7ac8a2        5 seconds ago       697MB

$ docker-compose up -d

$ docker exec -it php7.2 ls /usr/local/etc/php/conf.d/
docker-php-ext-exif.ini    docker-php-ext-pcntl.ini	 docker-php-ext-zip.ini
docker-php-ext-gd.ini	   docker-php-ext-pdo_mysql.ini
docker-php-ext-mysqli.ini  docker-php-ext-sodium.ini

$ docker stop php7.2


*** Docker push to Docker Hub ***

$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: isunyoo
Password: i~

$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
php                    7.2-fpm-mysql       69544c7ac8a2        4 minutes ago       697MB

$ docker tag 6df0f687dbbe isunyoo/php:7.2-fpm-mysql
$ docker push isunyoo/php (Upload image to DockerHub)

$ docker pull isunyoo/php:7.2-fpm-mysql

https://hub.docker.com/repository/docker/isunyoo/php

DockerHub 2FA Recovery Code: 1T63SZX0BX5RV

