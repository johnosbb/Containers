# Containers
A repo for container related projects


## Administering Containers

__List containers__

```bash
docker ps
```
__Stopping and Removing a Container__

```bash
docker stop <the-container-id>
docker rm <the-container-id>
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
