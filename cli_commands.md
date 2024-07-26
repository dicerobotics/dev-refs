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
Run-tag
``` shell
docker run redis:4.0 # run redis version 4.0
```
##

