## Docker Commands

- `docker <command>`

### Run - attach and detach

- `docker run <image_name>:<tag>` : for running a docker container ( in attach mode)
- `docker run -d <image_name> `: -d for detach mode
- `docker attach <container_id> ` : for attaching again
- `logs <container_name>`: for logging

### flags 

- `-p <host>:<container>` : PORT mapping, e.g : `-p 80:5000`
- `-it`: Interactive Terminal
-  `-v <host_path>:<container_path>` : attach volume 
- `-e <var_name>=<value>` : for env var


### Container

- `ps` : list containers, -a for all
- `stop <container_name> ` : to stop a container
- `rm <container_name>` : to remove a container

### Images

- `images` : list images
- `rmi <image_name>` : remove image
- `run <image_name>` : download and run 
- `pull <image_name>` : download only

### Exec - execute a command

- `docker exec <container_name> <exec_command>` : for executing a command in a container. e.g : `docker exec distracted_mcclintock cat /etc/hosts`


# Dockerfile

- `FROM` : base image
- `RUN` : run a command
- `COPY`: to copy files
- `ENTRYPOINT`: run the entry command with just executable, e.g - `ENTRYPOINT ["sleep"]`
- `CMD`: run the entry command, e.g - `CMD command param1` - `CMD sleep 5`, `CMD ["command", "param1"]` `CMD ["sleep", "5"]`
- `ENTRYPOINT and CMD` : e.g - `ENTRYPOINT ["sleep"] CMD ["5"]`

Override entrypoint command : 

`docker run --entrypoint <command> <image_name> <args>`

Build : `docker build -t <name>:<tag> .`


# Docker Compose

Create a file `docker-compose.yml` and run the file using `docker-compose up`

Docker compose file

- `version`: specify the version
- `Service`: 
- `name`:
- `image/build`
- `ports`
- `environment`

# Docker Storage

### Volume Mount : mount inside volumes folder - /var/lib/docker

- `docker run -v <host_path>:<container_path>`

Ex : - `docker run -v data_volume:/var/lib/mysql mysql`
        `docker run -v data_volume:/var/lib/mysql mysql`

### Bind Mount : mount any folder 

- `docker run -v <host_path>:<container_path>`

Ex : - `docker run -v /data/mysql:/var/lib/mysql mysql`
`docker run --mount type=bind,source=/data/mysql,target=/var/lib/mysql mysql`


# Networking

### Network driver : default - bridge, none, host

- `docker run ubuntu`, `docker run ubuntu --network=none`, `docker run ubuntu --network=host`

- `docker inspect <container_name>` : to see the Network settings, ip, type, etc.

- `docker network create --driver bridge --subnet 182.18.0.0/16 custom-isolated-network`

# Registry

- `docker run -d -p 5000:5000 --name registry registry:2` : deploy new registry
- `docker image tag <image_name> localhost:5000/<image_name> ` 
- `docker push localhost:5000/my-image`
- `docker pull localhost:5000/my-image`
- `docker pull 192.168.56.100:5000/my-image`
