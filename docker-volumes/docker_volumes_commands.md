# Volume Commands

Volumes are the preferred method of maintaining persistent data in Docker. In this lesson, we will begin learning how to use the volume subcommand to list, create, and remove volumes.

## Volume Basics

List all Docker volume commands:
```docker volume -h```
* `create`: Create a volume.
* `inspect`: Display detailed information on one or more volumes.
* `ls`: List volumes.
* `prune`: Remove all unused local volumes.
* `rm`: Remove one or more volumes.
List all volumes on a host:
```docker volume ls```
Create two new volumes:
```
docker volume create test-volume1
docker volume create test-volume2
```
Get the flags available when creating a volume:
```
docker volume create -h
```
Inspecting a volume:
```
docker volume inspect test-volume1
```
Deleting a volume:
```
docker volume rm test-volume
```
Removing all unused volumes:
```
docker volume prune
```
# Using Bind Mounts
Bind mounts have been around since the early days of Docker. They have limited functionality compared to volumes. With bind mount, a file or directory on the host machine is mounted into a container.
Volumes use a new directory that is created within Docker’s storage directory on the host machine, and Docker manages that directory’s contents.
Using the mount flag:
```
mkdir target

docker container run -d \
  --name nginx-bind-mount1 \
  --mount type=bind,source="$(pwd)"/target,target=/app \
  nginx
```
```
docker container ls
```
Bind mounts won't show up when listing volumes:
```
docker volume ls
```
Inspect the container to find the bind mount:
```
docker container inspect nginx-bind-mount1
```
Create a new file in /app on the container:
```
docker container exec -it nginx-bind-mount1 /bin/bash
cd target
touch file1.txt
ls
exit
```
Using the volume flag:
```
docker container run -d \
 --name nginx-bind-mount2 \
 -v "$(pwd)"/target2:/app \
 nginx
```
Create /app/file3.txt in the container:
```
docker container exec -it nginx-bind-mount2 touch /app/file3.txt
ls target2
```
Create an `nginx.conf` file:
```
mkdir nginx
cat << EOF >  nginx/nginx.conf
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
EOF
```
Create an Nginx container that creates a bind mount to `nginx.conf`:
```
docker container run -d \
 --name nginx-bind-mount3 \
 -v "$(pwd)"/nginx/nginx.conf:/etc/nginx/nginx.conf \
 nginx
```
Look at the bind mount by inspecting the container:
```
docker container inspect nginx-bind-mount3
```
# Using Volumes for Persistent Storage
Volumes are the preferred method for maintaining persistent data.
Volumes are easier to back up or migrate than bind mounts. You can manage volumes using Docker CLI commands or the Docker API. They work on both Linux and Windows containers. Volumes can be more safely shared among multiple containers. Volume drivers allow for:
* Storing volumes on remote hosts or cloud providers
* Encrypting the contents of volumes
* Add other functionality
New volumes can have their content pre-populated by a container.
Create a new volume for an Nginx container:
```
docker volume create html-volume
```
Creating a volume using that volume mount:
```
docker container run -d \
 --name nginx-volume1 \
 --mount type=volume,source=html-volume,target=/usr/share/nginx/html/ \
 nginx
 ```
Inspect the volume:
```
docker volume inspect html-volume
```
List the contents of `html-volume`:
```
sudo ls /var/lib/docker/volumes/html-volume/_data
```
Creating a volume using that volume flag:
```
docker container run -d \
 --name nginx-volume2 \
 -v html-volume:/usr/share/nginx/html/ \
 nginx
```
Edit `index.html`:
```
sudo vi /var/lib/docker/volumes/html-volume/_data/index.html
```
Inspect `nginx-volume2` to get the private IP:
```
docker container inspect nginx-volume2
```
Login into `nginx-volume1` and go to the `html` directory:
```
docker container exec -it nginx-volume1 /bin/bash
cd /usr/share/nginx/html
cat index.hml
```
Install Vim:
```
apt-get update -y
apt-get install vim -y
```
Using a readonly volume:
```
docker run -d \
  --name=nginx-volume3 \
  --mount source=html-volume,target=/usr/share/nginx/html,readonly \
  nginx
```
Login into `nginx-volume3` and go to the `html` directory:
```
docker container exec -it nginx-volume3 /bin/bash
cd /usr/share/nginx/html
cat index.hml
```
Install Vim:
```
apt-get update -y
apt-get install vim -y
```
Now edit and change the `index.html` contents and check if the contents are being saved. 
Contents will not be saved since the volume was mounted as read-only