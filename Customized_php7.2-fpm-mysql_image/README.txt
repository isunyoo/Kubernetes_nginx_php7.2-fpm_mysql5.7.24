
*** Build docker image with mysql modules ***

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

$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
php                    7.2-fpm-mysql       69544c7ac8a2        4 minutes ago       697MB

$ docker tag 6df0f687dbbe isunyoo/php:7.2-fpm-mysql
$ docker push isunyoo/php (Upload image to DockerHub)

$ docker pull isunyoo/php:7.2-fpm-mysql
