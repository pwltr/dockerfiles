# Dockerfiles (Sysbox)

Collection of useful dockerfiles for system containers.

## Requirements

- [Docker](https://docs.docker.com/get-docker/)
- [Sysbox](https://github.com/nestybox/sysbox/blob/master/docs/user-guide/install-package.md)

## Usage

### Build an image

```shell
docker build -t <image-tag> - < ./<path>/Dockerfile
```

### Run a container

```shell
docker run --detach --publish-all --runtime sysbox-runc --name <name> --hostname <name> <image-tag>
```

### Log into a container

Find out container SSH port

```shell
docker container inspect --format='{{(index (index .NetworkSettings.Ports "22/tcp") 0).HostPort}}' <container-name>
```

Connect via SSH

```shell
ssh dev@172.17.0.1 -p <port>
```

Default credentials are `dev:pass`

## Troubleshoot

Run `docker info` and make sure you have "overlay2" configured as your storage driver and that docker knows about Sysbox as a runtime.

- Unknown runtime specified sysbox-runc

https://github.com/nestybox/sysbox/blob/master/docs/user-guide/troubleshoot.md#docker-reports-unknown-runtime-error

- Inner containers fail to start / stop

Make sure you have "overlay2" configured as your storage driver, see https://docs.docker.com/storage/storagedriver/overlayfs-driver/

For Sysbox related issues see https://github.com/nestybox/sysbox/blob/master/docs/user-guide/troubleshoot.md
