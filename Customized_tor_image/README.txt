
*** Build docker image with TorV2 modules ***

$ docker build -t tor:v2-nginx .

$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tor                 v2-nginx            50f9131c128c        8 seconds ago       218MB

$ docker-compose up -d

$ docker ps -a
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
tor                        v2-nginx            50f9131c128c        3 minutes ago       218MB

$ docker exec -it tor cat /etc/tor/torrc
$ docker exec -it tor cat /var/www/html/.tor/hidden_app_1/service
behawofjkiooslfs.onion

$ docker logs -f tor
$ docker stop tor

$ docker system prune -a (All Docker resources clean up)


*** Docker push to Docker Hub ***

$ docker login

$ docker images
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
tor                        v2-nginx            50f9131c128c        9 minutes ago       218MB

$ docker tag 50f9131c128c isunyoo/tor:v2-nginx
$ docker push isunyoo/tor (Upload image to DockerHub)

$ docker pull isunyoo/tor:v2-nginx


*** Check Docker Resources ***

$ docker volume ls
$ docker network ls

