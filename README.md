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
We can launch a container to attach to a particular network:

```bash
docker run -d --network container_network --network-alias mysql -v todo-mysql-data:/var/lib/mysql  -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=todos    mysql:8.0
```

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





