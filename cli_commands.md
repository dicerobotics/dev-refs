# Docker
Source: https://www.youtube.com/watch?v=fqMOX6JJhGo&t=1529s

## Basic Commands
``` shell
docker run <container_image>  # Run the instance of a container from a container image
docker container ls           # list running containers
docker ps                     # alias of "docker container ls"
docker container ls -a        # all containers running/not_running, alias "docker ps -a"
docker stop <container_name>  # stop a container. We may also provide the container id
docker stop <container_id>    # We may also provide the container ID to kill/stop it
docker rm <container_name>    # remove a container (running/not_running)
docker rm <container_id>      # We may also provide the container ID to remove it.>
docker images                 # list images
docker rmi <image_name>       # Remove the image, we must delete all dependent containers before we remove a docker image
docker pull <image_name>      # Pull the image but do not run the associated container
docker inspect <container_name/id> # Used to inspect container
docker logs <container_name/id> # Used to check the log of a container
```
Notes: 
1. container is an instance of a docker image
2. a container stops automatically as the processes running in the containers are completed. For example, "docker run ubuntu" will stop immediately after starting. But, "docker run ubuntu sleep 5" will stop after five seconds of running.
``` shell
docker run -it centos bash    # Run an instance of a container from a container image named centos and log us into the bash.
docker run -d --name <container_name> <image_name> # Run an instance of a container with name "container_name" from image "image_name"
```

## Run Command
Run a command on a container already running
``` shell
docker exec <container_name>  <command>
docker exec distracted_mcclintock cat /etc/hosts #Lists contents of /etc/hosts file in ubuntu container named distracted_mcclintock
```
Run a docker in attach or detach mode
``` shell
docker run <conatiner_name> # running in attached mode
docker run -d <container_name> # running in detached mode
docker attach <id> # attach an initially detached container, first few characters of the ID are sufficient as long as they are distinct
```
Run-tag (Run a container with a specific version/tag of the image)
``` shell
docker run <container_name>:<4.0> # run a container with version 4.0
docker run centos:4.0
```
Run - STDIN ()
By default, docker doesn't run in the standard input mode.
We may use an -i option to operate in an interactive mode( similar to standard i/o mode).
We may also use the -t option to use an attached pseudo-terminal.

``` shell
docker run -i <dockerized-app>  # work in an iterative mode
docker run -it <dockerized-app> # work in an iterative mode using an attached pseudo-terminal
```

Run - PORT mapping
Docker host and docker containers have dedicated IPs.
Multiple instances of the same image have dedicated IPs too.
The container IPs and Ports are internal to the host. We may excess them from inside the docker host.
To access a container from outside the docker host, we must map container ports to the available ports in the docker host. Then we may access containers using host ip and port settings.
Note: All host ports must be unique

``` shell
docker run <container_name> # Runs a container without port mapping, can only be accessed from inside the docker host
docker run -p <host_port>:<container_port> <container_name> # Runs a container with port mapping, can be accessed from outside the docker host
```

Run - Volume Mapping
When we remove a container, all the data inside the container gets deleted. To avoid this problem, we may map the container folder/volume to some external volume.
``` shell
docker run mysql # Run without volume mapping
docker stop MySQL
docker rm mysql # data gets deleted

docker run -v <external_dir>:<internal_dir> <container_name>
docker run -v /opt/datadir:/var/lib/mysql mysql # running container mysql with volume mapping
docker stop MySQL
docker rm mysql # container is deleted but data is preserved in /opt/datadir
```





