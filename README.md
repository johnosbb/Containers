# Containers
A repo for container related projects


## Administering Containers

__List containers__

```bash
docker ps
```

__Observing Logs from a Container__

```bash
docker logs <container-id>
```

__Stopping and Removing a Container__

```bash
docker stop <the-container-id> # Stop
docker rm <the-container-id> # Remove
docker rm -f <the-container-id> # Stop and Remove
```

__Uploading a Container to Docker__

```bash
docker login -u drakesoftdocker
docker push drakesoftdocker/getting-started
```

__Running a Container uploaded to Docker__

```bash
docker run -dp 127.0.0.1:3000:3000 drakesoftdocker/getting-started
```

__connect to Running Container and execure a command__

```bash
docker exec <container-id> <your bash command>
```

__connect to Container shell__

```bash
docker exec -it <container-id> /bin/bash
```

__Create a Volume__

```bash
docker volume create todo-db # create
```
__Mount a Volume on a Container__

```bash
docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
```
__Inspect a Volume__


```bash
docker volume inspect todo-db #

[
    {
        "CreatedAt": "2023-08-23T13:38:19+01:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/todo-db/_data",
        "Name": "todo-db",
        "Options": null,
        "Scope": "local"
    }
]

# We can view the container contents at: /var/lib/docker/volumes/todo-db/_data
```

__Bind_Mounts__

With a bind mount we can specify a directory on the host that the container can access. The 'target' option spedifies where the host directory will be mounted in the container.

```bash
docker run -it --mount type=bind,src="$(pwd)",target=/src ubuntu bash
```
## Containers and Networking

We can create a network for a container with the command 'docker network'

```bash
docker network create container_network
```
We can launch a container to attach to a particular network, in this instance a mySQL server instance:

```bash
docker run -d --network container_network --network-alias mysql -v todo-mysql-data:/var/lib/mysql  -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=todos    mysql:8.0
```

Docker recognizes you want to use a named volume (/var/lib/mysql) and creates one automatically for you.

We can inspect the network with

```bash
docket network inspect container_network
```

```json
[
    {
        "Name": "container_network",
        "Id": "850bb7e9016a20647a8e79c14b5a953411d4cb671c3385410be76bacc7472668",
        "Created": "2023-08-23T16:09:24.677293645+01:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "22701baec940ac43776d6bc1df0d7e039d3232a19615d447b1538e03d4ced129": {
                "Name": "vigorous_chatelet",
                "EndpointID": "7d9930d59bd5f9db65451651cd2dfb53e32a8e20e192fb8311cbbcc7c6216f95",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

```

We can connect to this instance with 

```bash
docker exec -it <mysql-container-id> mysql -u root -p
```

```bash
docker exec -it 22701baec940 mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.34 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

```

Remember that each container has its own IP address.

### Trouble Shooting Container Networks

The container nicolaka/netshoot ships with a lot of tools that are useful for troubleshooting or debugging networking issues.

```bash
docker run -it --network container_network nicolaka/netshoot
```

```bash
                    dP            dP                           dP   
                    88            88                           88   
88d888b. .d8888b. d8888P .d8888b. 88d888b. .d8888b. .d8888b. d8888P 
88'  `88 88ooood8   88   Y8ooooo. 88'  `88 88'  `88 88'  `88   88   
88    88 88.  ...   88         88 88    88 88.  .88 88.  .88   88   
dP    dP `88888P'   dP   `88888P' dP    dP `88888P' `88888P'   dP   
                                                                    
Welcome to Netshoot! (github.com/nicolaka/netshoot)
Version: 0.11

```

We can then use commands like dif to find the ip address of other containers.

```bash
dig mysql

; <<>> DiG 9.18.13 <<>> mysql
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 33492
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;mysql.				IN	A

;; ANSWER SECTION:
mysql.			600	IN	A	172.18.0.2

;; Query time: 3 msec
;; SERVER: 127.0.0.11#53(127.0.0.11) (UDP)
;; WHEN: Wed Aug 23 15:26:52 UTC 2023
;; MSG SIZE  rcvd: 44


```
The network alias we specified when creating the mysql container allows other containers to connect to it. (--network-alias mysql) [For exampe see here](https://docs.docker.com/get-started/07_multi_container/)


## Using Compose



We can script a number of containers by using compose.

We first create a yaml file that defines the stack

```yml
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 127.0.0.1:3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos

  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```
We can then launch this with:

```bash
docker compose up -d
[+] Running 5/5
 ✔ app 4 layers [⣿⣿⣿⣿]      0B/0B      Pulled                              2.4s 
   ✔ 7264a8db6415 Already exists                                           0.0s 
   ✔ 751194035c36 Already exists                                           0.0s 
   ✔ eff5dce73b38 Already exists                                           0.0s 
   ✔ c8ce5be43019 Already exists                                           0.0s 
[+] Running 4/4
 ✔ Network getting-started-app_default           Created                   0.1s 
 ✔ Volume "getting-started-app_todo-mysql-data"  Created                   0.0s 
 ✔ Container getting-started-app-mysql-1         Started                   0.6s 
 ✔ Container getting-started-app-app-1           Started
```
We can check the startup with:

```bash
docker compose logs -f
```

We can tear down this stack with:

```bash
docker compose down
```

[Docker Compose CLI Reference](https://docs.docker.com/compose/reference/)
