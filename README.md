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
