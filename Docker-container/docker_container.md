# Running Containers:

docker container run -it --name <NAME> <IMAGE>:<TAG>


# Docker Management Commands: 

`Usage:  docker image COMMAND`

`Manage images`

## Commands:

 | Command     | Description
 | ----------- | ------------------------------------------------------------------------------|		
 | `build`     |  Build an image from a Dockerfile                                             |
 |  `history`  |  Show the history of an image						       |	
 | `import`    |  Import the contents from a tarball to create a filesystem image	       |	
 | `inspect`   |  Display detailed information on one or more images			       | 		
 | `load`      |  Load an image from a tar archive or STDIN				       |			
 | `ls`        |  List images								       |	
 | `prune`     |  Remove unused images							       |	
 | `pull`      |  Pull an image or a repository from a registry				       |			
 | `push`      |  Push an image or a repository to a registry				       |		
 | `rm`        |  Remove one or more images						       |	
 | `save`      |  Save one or more images to a tar archive (streamed to STDOUT by default)     |	
 | `tag`       |  Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE			       |	



# Creating Containers:

## Create a container and attach to it:
```docker container run –it busybox```
## Create a container and run it in the background:
```docker container run –d nginx```
## Create a container that you name and run it in the background:
```docker container run –d –name myContainer busybox```
# Exposing and Publishing Container Ports:
## Exposing:
## Expose a port or a range of ports
Use --expose [PORT]
```docker container run --expose 1234 [IMAGE]```
## Publishing:
## Maps a container's port to a host`s port
```-p or --publish publishes a container's port(s) to the host
-P, or --publish-all publishes all exposed ports to random ports
docker container run -p [HOST_PORT]:[CONTAINER_PORT] [IMAGE]
docker container run -p [HOST_PORT]:[CONTAINER_PORT]/tcp -p [HOST_PORT]:[CONTAINER_PORT]/udp [IMAGE]
docker container run -P
```

## Lists all port mappings or a specific mapping for a container:
```docker container port [Container_NAME]```
# Executing Container Commands
Start a container with a command:
```docker container run [IMAGE] [CMD]```
Execute a command on a container:
```docker container exec -it [NAME]or[CONTAINER_ID] [CMD]```
## Example:
```
docker container run -d -p 8080:80 nginx
docker container ps
docker container exec -it [NAME]or[CONTAINER_ID] /bin/bash
docker container exec -it [NAME]or[CONTAINER_ID] ls /usr/share/nginx/html/
```
## Container Logging:
Create a container using the weather-app image.
```docker container run --name weather-app -d -p 80:3000 linuxacademycontent/weather-app```
Show information logged by a running container:
```docker container logs [NAME]```
Show information logged by all containers participating in a service:
```docker service logs [SERVICE]```
Logs need to be output to STDOUT and STDERR.
Nginx Example:

```
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log
```
Debug a failed container deploy:
```
docker container run -d --name ghost_blog \
    -e database__client=mysql \
    -e database__connection__host=mysql \
    -e database__connection__user=root \
    -e database__connection__password=P4sSw0rd0! \
    -e database__connection__database=ghost \
    -p 8080:2368 \
    ghost:1-alpine
