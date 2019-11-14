# Networking Commands


## Networking Basics

`ifconfig`

List all Docker network commands:

```docker network -h```
* `connect` Connect a container to a network 
* `create` Create a network 
* `disconnect` Disconnect a container from a network 
* `inspect` Display detailed information on one or more networks 
* `ls` List networks 
* `prune` Remove all unused networks 
* `rm` Remove one or more networks
List all Docker networks on the host:
```
docker network ls
docker network ls --no-trunc
```
Getting detailed info on a network:
```docker network inspect [NAME]```
Creating a network:
```docker network create br00```
Deleting a network:
```docker network rm [NAME]```
Remove all unused networks:
```docker network prune```
## Adding and Removing containers to a network
Create a container with no network:
```docker container run -d --name network-test03 -p 8081:80 nginx```
Create a new network:
```docker network create br01```
Add the container to the bridge network:
```docker network connect br01 network-test03```
Inspect network-test03 to see the networks:
```docker container inspect network-test03```
Remove network-test03 from br01:
```docker network disconnect br01 network-test03```
# Networking Containers:
## Creating a network and defining a Subnet and Gateway
Create a bridge network with a subnet and gateway:
```docker network create --subnet 10.1.0.0/24 --gateway 10.1.0.1 br02```
Run ifconfig to view the bridge interface for br02:
```ifconfig```
Inspect the br02 network:
```docker network inspect br02```
Prune all unused networks:
```docker network prune```
Create a network with an IP range:
```
docker network create --subnet 10.1.0.0/16 --gateway 10.1.0.1
--ip-range=10.1.4.0/24 --driver=bridge --label=host4network br04
```
Inspect the br04 network:
```docker network inspect br04```
Create a container using the br04 network:
```docker container run --name network-test01 -it --network br04 centos /bin/bash```
Install Net Tools:
```
yum update -y
yum install -y net-tools
```
Get the IP info for the container:
`ifconfig`
Get the gateway info the container:
```netstat -rn```
Get the DNS info for the container:
```cat /etc/resolv.conf```
## Assigning IPs to a container:
Create a new container and assign an IP to it:
```docker container run -d --name network-test02 --ip 10.1.4.102 --network br04 nginx```
Get the IP info for the container:
```docker container inspect network-test02 | grep IPAddr```
Inspect network-test03 to see that br01 was removed:
```docker container inspect network-test04```
## Networking two containers:
Create an internal network:
```docker network create -d bridge --internal localhost```
Create a MySQL container that is connected to localhost:
```
docker container run -d --name test_mysql 
    -e MYSQL_ROOT_PASSWORD=P4sSw0rd0 
    --network localhost mysql:5.7
```
Create a container that can ping the MySQL container:
```
docker container run -it --name ping-mysql
    --network bridge 
    centos
```
Connect ping-mysql to the localhost network:
```docker network connect localhost ping-mysql```
Restart and attach to container:
```docker container start -ia ping-mysql```
Create a container that can't ping the MySQL container:
```
docker container run -it --name cant-ping-mysql
centos
```
Create a Nginx container that is not publicly accessible:
```docker container run -d --name private-nginx -p 8081:80 --network localhost nginx```
Inspect `private-nginx`:
```docker container inspect private-nginx```
