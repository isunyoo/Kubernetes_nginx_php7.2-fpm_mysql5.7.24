
*** Creating a gcloud kebernates cluster ***

$ gcloud auth list
  Credentialed Accounts
ACTIVE  ACCOUNT
*       csm@bworks.co.kr

$ gcloud beta auth login
$ gcloud config set project ethereal-atlas-261207
$ gcloud config set compute/zone asia-southeast1-a
$ gcloud config set compute/region asia-southeast1
$ gcloud components update

$ gcloud container clusters create blackhole --zone asia-southeast1-a
$ gcloud container clusters delete blackhole --zone asia-southeast1-a
$ gcloud container clusters get-credentials blackhole --zone asia-southeast1-a --project ethereal-atlas-261207



*** Kubectl Commands ***

$ kubectl cluster-info
$ kubectl get nodes --show-labels
$ kubectl get nodes|svc|pod|deployments|pvc -o wide --watch
$ kubectl logs -f [POD_NAME]
$ kubectl describe pod [POD_NAME] | pvc [PVC_NAME]
$ kubectl delete svc [SVC_NAME] | deployment [DEPLOYMENT_NAME]
$ kubectl delete pod [POD_NAME] | $ kubectl delete pods --all
$ kubectl expose deployment nginx --port=80 --type=LoadBalancer


*** Provision GCP Persistent Disk(3nodes g-cluster) ***

https://medium.com/@Sushil_Kumar/readwritemany-persistent-volumes-in-google-kubernetes-engine-a0b93e203180

$ gcloud compute disks create --size=2GB --zone=asia-southeast1-a nfs-disk
$ gcloud container clusters get-credentials blackhole --zone asia-southeast1-a --project ethereal-atlas-261207
$ kubectl create -f kube/gke_nfs_server.yaml

$ kubectl get svc -o wide
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE    SELECTOR
nfs-server   ClusterIP   10.59.247.168   <none>        2049/TCP,20048/TCP,111/TCP   22s    role=nfs-server

$ kubectl get pod -o wide
NAME                          READY   STATUS             RESTARTS   AGE    IP           NODE                                       NOMINATED NODE   READINESS GATES
nfs-server-6b64c5d959-4v2sj   1/1     Running            0          49s    10.56.1.12   gke-blackhole-default-pool-b95ac6cc-7jh0   <none>           <none>

$ kubectl create -f kube/gke_nfs_pv_pvc.yaml

$ kubectl get pvc
NAME        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
www-pvc     Bound    www-pvc                                    2Gi        RWX                           7s


*** Deploying Pods ***

$ cd blackhole-k8s
$ ls -rlt kube/
-rw-r--r-- 1 syoo syoo 1117 Dec 11 18:51 mysql.yaml
-rw-r--r-- 1 syoo syoo  798 Dec 23 18:10 tor.yaml
-rw-r--r-- 1 syoo syoo  284 Dec 23 19:39 local_minikube_volume.yaml
-rw-r--r-- 1 syoo syoo 1219 Dec 23 19:55 php.yaml
-rw-r--r-- 1 syoo syoo 2101 Dec 23 20:22 nginx.yaml
-rw-r--r-- 1 syoo syoo  973 Dec 23 20:34 gke_nfs_server.yaml
-rw-r--r-- 1 syoo syoo  386 Dec 23 20:49 gke_nfs_pv_pvc.yaml

$ kubectl apply -f kube/mysql.yaml
$ kubectl apply -f kube/local_minikube_volume.yaml (Minikube for local single node)
$ kubectl apply -f kube/php.yaml
$ kubectl apply -f kube/nginx.yaml
$ kubectl apply -f kube/tor.yaml
$ kubectl delete -f kube (Delete all pods in kube yaml resources)

$ kubectl cp PHP_Connection_Tests.zip php-5cd5b89b99-xwkkl:/var/www/html/PHP_Connection_Tests.zip
$ kubectl exec -it php-5cd5b89b99-xwkkl -- /bin/bash
 # docker-php-ext-install pdo pdo_mysql mysqli mbstring zip exif pcntl
 # php -i | grep mysql

$ kubectl exec -it php-5cd5b89b99-xwkkl ls /usr/local/etc/php/conf.d
docker-php-ext-exif.ini    docker-php-ext-pcntl.ini	 docker-php-ext-zip.ini
docker-php-ext-gd.ini	   docker-php-ext-pdo_mysql.ini
docker-php-ext-mysqli.ini  docker-php-ext-sodium.ini

$ kubectl exec -it  nginx-7697c74866-bv9k5 cat /etc/nginx/conf.d/site.conf

$ kubectl cp mysql_test_1.php nginx-6fcbc6cdf8-zz848:/var/www/html/mysql_test_1.php
$ kubectl cp mysql_test_2.php nginx-6fcbc6cdf8-zz848:/var/www/html/mysql_test_2.php
$ kubectl cp mysql_test_3.php nginx-6fcbc6cdf8-zz848:/var/www/html/mysql_test_3.php

$ kubectl exec -it  php-6bc57dd7cf-rdx92 ls /var/www/html
$ kubectl exec -it  nginx-7697c74866-bv9k5 ls /var/www/html
index.html  mysql_test_1.php  mysql_test_2.php	mysql_test_3.php  php_info.php

$ kubectl get svc -o wide
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP    PORT(S)                      AGE   SELECTOR
nginx        LoadBalancer   10.59.243.4     34.87.49.222   80:31672/TCP                 10m   app=nginx,tier=backend

http://34.87.49.222/mysql_test_1.php , http://34.87.49.222/mysql_test_2.php , http://34.87.49.222/mysql_test_3.php


*** onion service *** 

$ kubectl apply -f kube/tor.yaml

$ kubectl get svc -o wide
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE    SELECTOR
tor          LoadBalancer   10.104.23.83     <pending>     9050:32617/TCP   12m    app=tor,tier=backend

$ kubectl get pod -o wide
NAME                     READY   STATUS    RESTARTS   AGE    IP            NODE       NOMINATED NODE   READINESS GATES
tor-8fbccb676-6dz88      1/1     Running   0          4m7s   172.17.0.10   minikube   <none>           <none>

$ kubectl exec -it tor-8fbccb676-6dz88 cat /etc/tor/torrc
HiddenServiceDir /home/tor/.tor/hidden_app_1/
HiddenServiceVersion 2
HiddenServicePort 80 nginx:80

$ kubectl exec -it tor-8fbccb676-6dz88 cat /home/tor/.tor/hidden_app_1/hostname
 5eg7frmmnaevchwb.onion , http://5eg7frmmnaevchwb.onion/php_info.php


*** GCloud Cluster Services ***

$ kubectl get svc -o wide
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                      AGE     SELECTOR
kubernetes   ClusterIP      10.59.240.1     <none>          443/TCP                      13m     <none>
mysql        ClusterIP      10.59.245.2     <none>          3306/TCP                     2m59s   app=mysql
nfs-server   ClusterIP      10.59.247.99    <none>          2049/TCP,20048/TCP,111/TCP   5m29s   role=nfs-server
nginx        LoadBalancer   10.59.246.93    34.87.49.222    80:31760/TCP                 2m49s   app=nginx,tier=backend
php          ClusterIP      10.59.250.176   <none>          9000/TCP                     2m53s   app=php,tier=backend
tor          LoadBalancer   10.59.254.22    34.87.108.184   9050:30677/TCP               2m42s   app=tor,tier=backend

http://34.87.49.222/php_info.php

$ kubectl exec -it tor-577f8674dd-chxwr cat /home/tor/.tor/hidden_app_1/hostname
xttlv3a5m4txswu7.onion

http://xttlv3a5m4txswu7.onion/mysql_test_1.php






