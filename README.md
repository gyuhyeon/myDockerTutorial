# Docker Tutorial

## Part 1 : Intro(installing Docker)
- Ubuntu 16.04 (amd64)
```
sudo apt-get update
# add dependencies(or rather, allow apt repo over HTTPS)
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
# add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update
sudo apt-get install docker-ce
```

## Part 2 : Hello World!
Using the "app.py", "Dockerfile", and "requirements.txt", build an image. This results in the image going into the local docker registry.  
After that, running it with "docker run" will result in the image being used by a container.  
when running in detached mode, use "docker container stop" to stop the container process.  
"docker push/pull" will push/pull to/from the repository(Docker cloud).  
```
docker build -t dockerimagename .          # Create image using this directory's Dockerfile
docker run -p 4000:80 dockerimagename      # Run "dockerimagename" mapping port 4000 to 80
docker run -d -p 4000:80 dockerimagename   # Same thing, but in detached mode
docker container ls                        # List all running containers
docker container ls -a                     # List all containers, even those not running
docker container stop <hash>               # Gracefully stop the specified container
docker container kill <hash>               # Force shutdown of the specified container
docker container rm <hash>                 # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                         # List all images on this machine
docker image rm <image id>                 # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login                               # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag # Tag <image> for upload to registry
docker push username/repository:tag        # Upload tagged image to registry
docker run username/repository:tag         # Run image from a registry
```

## Part 3 : Services
A service runs only one image, but it can configure how that image runs - ports, how many replicas, etc.  
Defining the service is done by creating a "docker-compose.yml" file.  

```
# IMPORTANT NOTE : Simple namespace will be surrounded in curly brackets.
# For example, {web} can be web, webservice, etc.

version: "3"
services:
  {web}:
    # replace username/repo:tag with own configuration
    image: username/repo:tag
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.1"
          memory: 50M
      restart_policy:
        condition: on-failure
    ports:
      - "80:80"
    networks:
      - {webnet}
networks:
  {webnet}:
```

- Initialize swarm.  
```
docker swarm init
```
- Run the stack  
```
docker stack deploy -c docker-compose.yml whateverephemeralappname
docker service ls  # show running service
```
- Examine service
```
docker service ps whateverephemeralappname_web
docker container ls
```
- Take down service and swarm
```
docker stack rm whateverephemeralappname
docker swarm leave --force
```

- Whole cheatsheet
```
docker stack ls                                            # List stacks or apps
docker stack deploy -c <composefile> <appname>  # Run the specified Compose file
docker service ls                 # List running services associated with an app
docker service ps <service>                  # List tasks associated with an app
docker inspect <task or container>                   # Inspect task or container
docker container ls -q                                      # List container IDs
docker stack rm <appname>                             # Tear down an application
docker swarm leave --force      # Take down a single node swarm from the manager
```

## Part 4 : Swarm
