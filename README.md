# Dockerfiles

Collection of useful dockerfiles for development & debugging.

## Requirements

- [Docker](https://docs.docker.com/get-docker/)

## Usage

### Build an image

```shell
docker build -t <image-tag> - < ./<path>/Dockerfile
```

### Run a container

```shell
docker run --detach --publish-all --name <name> --hostname <name> <image-tag>
```

### Log into the container

Find out container SSH port

```shell
docker container inspect --format='{{(index (index .NetworkSettings.Ports "22/tcp") 0).HostPort}}' <container-name>
```

Connect via SSH

```shell
ssh dev@172.17.0.1 -p <port>
```

Default credentials are `dev:pass`
