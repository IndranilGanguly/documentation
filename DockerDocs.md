# DOCKER DOCS

### Build a docker image from a Dockerfile

```bash
docker build -t image-name .
```

### Save the docker image as an archive file

```bash
docker save -o file-name.tar image-name
```

### Load the image from the archive file

```bash
docker load -i file-name.tar
```

### Run a docker image on a certain port and a user defined container name

```bash
docker run -d -p public-port:docker-port --name container-name image-name
```

1. -d denotes Detached Mode

2. -p defines the port binding

3. --name defines the custom name of the container

4. here public-port is the port in which the docker container will be exposed over the internet

5. here docker-port is the port by which the docker container itself is exposed as it will be mentioned in the Dockerfile

for example considering a Dockerfile

```dockerfile

FROM httpd:alpine


COPY index.html /usr/local/apache2/htdocs/


EXPOSE 80
```

here 80 is the docker-port where the docker container will be exposed

now if a user wants to expose a port 8080 over the internet so the command will be

```bash
docker run -d -p 8081:80 --name container-name image-name
```

### To see the running logs of a container

```bash
docker logs -r container-name
```

### To enter to the interactive mode for a particular docker container from where user may run linux commands inside the container

```bash
docker exec -it container-name sh
```


### To see the list of running containers

```bash
docker ps
```

### To see the list of all containers (running and non-running)

```bash
docker ps -a
```

### To see the list of all images

```bash
docker images
```

### To stop a docker container

```bash
docker stop container-name
```

### To remove a container or image

```bash
docker rm container-name
```

or

```bash
docker rm container-id
```

```bash
docker rmi image-name
```

or

```bash
docker rmi image-id
```

#### If any bash file is created that needs to be included in docker image and if the bash file is created in windows then the image will be created but the execution will not work
#### It most likely has CRLF (\r\n) line endings, which break shell execution on Linux and cause this classic Docker error

It is needed to change to LF endings

In VS Code:
1. Open the bash file
2. Look at the bottom right: it says CRLF
3. Click CRLF → change to LF 
4. Save the file

