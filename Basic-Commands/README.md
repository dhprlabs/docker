# Docker Basic Commands

## Images

### Pull a Docker image
```bash
docker image pull <image_name>
````

Pulls an image from Docker Hub or another registry.

### List Docker images

```bash
docker image ls
```

Displays a list of all images stored locally.

### Remove a Docker image

```bash
docker image rm <image_name>
```

Deletes a specific image from local storage.

---

## Containers

### Start a container

```bash
docker container start <container_id_or_name>
```

Starts an existing container.

### Stop a container

```bash
docker container stop <container_id_or_name>
```

Stops a running container.

### View running containers

```bash
docker container ls
```

Lists all currently running containers.

### View container logs

```bash
docker container logs <container_id_or_name>
```

Displays the logs of a specific container.

---

## Extras

### Run a new container

```bash
docker container run <options> <image_name>
```

Runs a new container from an image.

### Remove a container

```bash
docker container rm <container_id_or_name>
```

Removes a stopped container.

### Execute a command inside a running container

```bash
docker container exec -it <container_id_or_name> <command>
```

Runs a command inside an active container.

### Force remove an image

```bash
docker image rmi <image_name>
```

Forces removal of an image.

### Clean up stopped containers

```bash
docker container prune
```

Removes all stopped containers.