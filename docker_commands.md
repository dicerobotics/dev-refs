# Docker Help
Source: https://www.youtube.com/watch?v=fqMOX6JJhGo&t=1529s

## Basic Commands
''' shell
docker run <container_image>  # run the instance of a container image
docker ps                     # list containers
docker ps -a                  # all containers running/not_running
docker stop <container_name>  # stop a container
docker rm <container_name>    # remove a container (running/not_running)
docker images                 # list images
docker rmi <image_name>       # remove the image, we should delete all dependent containers before we remove a docker image
docker pull <image_name>      # just pull the image but not run the container
'''
Notes: 
1. container is an instance of a docker image
2. a container stops automatically as the processes running in the containers are completed. e.g., "docker run ubuntu" will stop immidiately after start. But, "docker run ubuntu sleep 5" will start after five seconds of running.

## Run a command on a container already running
''' shell
docker exec <container_name>  <command>
docker exec distracted_mcclintock cat /etc/hosts #Lists contents of /etc/hosts file in ubuntu container named distracted_mcclintock
'''

## Run a docker in attach or detack mode

''' shell
docker run <conatiner_name> # running in attached mode
docker run -d <container_name> # running in detached mode
docker attach <id> # attach an intially detached container, first few characters of the ID are sufficient as long as they are distinct
'''

## Docker CLI
