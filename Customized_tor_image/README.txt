

*** Build docker image with TorV2 modules ***

$ cd /home/syoo/Documents/GCloud/K8S(Kubernetes)/blackhole-k8s/Customized_tor_image
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
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: isunyoo
Password: i~

$ docker images
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
tor                        v2-nginx            50f9131c128c        9 minutes ago       218MB

$ docker tag 50f9131c128c isunyoo/tor:v2-nginx
$ docker push isunyoo/tor (Upload image to DockerHub)

$ docker pull isunyoo/tor:v2-nginx

https://hub.docker.com/repository/docker/isunyoo/tor

DockerHub 2FA Recovery Code: 1T63SZX0BX5RV


*** Check Docker Resources ***

$ docker volume ls
$ docker network ls



