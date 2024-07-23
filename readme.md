
## YouTube Link
For the full 1 hour course watch on youtube:
https://www.youtube.com/watch?v=6YZvp2GwT0A

# Installation
## Build the Jenkins BlueOcean Docker Image (or pull and use the one I built)
```
docker build -t myjenkins-blueocean:2.414.2 . (run `docker network create jenkins` first)

#IF you are having problems building the image yourself, you can pull from my registry (It is version 2.332.3-1 though, the original from the video)

docker pull devopsjourney1/jenkins-blueocean:2.332.3-1 && docker tag devopsjourney1/jenkins-blueocean:2.332.3-1 myjenkins-blueocean:2.332.3-1
```

## Create the network 'jenkins'
```
docker network create jenkins
```

## Run the Container
### MacOS / Linux
```
docker run --name jenkins-blueocean --restart=on-failure --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  myjenkins-blueocean:2.414.2
```

### Windows
```
# modify the 'FROM' command in your dockerfile to, 
`FROM jenkins/jenkins:2.452.3-jdk17` before running this command
docker run --name jenkins-blueocean --restart=on-failure --detach `
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 `
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 `
  --volume jenkins-data:/var/jenkins_home `
  --volume jenkins-docker-certs:/certs/client:ro `
  --publish 8080:8080 --publish 50000:50000 myjenkins-blueocean:2.452.3-1
```


## Get the Password
```
docker exec jenkins-blueocean cat /var/jenkins_home/secrets/initialAdminPassword
```
### If you are unable to run the command above at once for whatever reason, run:
```
docker exec jenkins-blueocean bash
```
### to get into a shell in your container, then cat the file

## Connect to the Jenkins
```
https://localhost:8080/
```

## Installation Reference:
https://www.jenkins.io/doc/book/installing/docker/


## Accessing the file system of our jenkins server
This is what you will do often time while working with jenkins especially when troubleshooting. 
Run the command, ```docker exec -it jenkins-blueocean bash ``` to run a bash shell inside the container interactively.
```
cd /var/jenkins_home
```
This is the directory you provided in the 'volume' when you ran your jenkins container
If you run ``` ls -ltra ``` you will see various folders including the **workspace** folder.
In the workspace folder is a folder with the name of your job


## Create and execute a build job
Visit the dashboard page of your jenkins UI and click on ***New Item***. Enter a name and select ***Freestyle project*** and click ***save***
Select ***Configure*** in the next screen. Provide a description for the particular build. Under **Source Code Management, select ***Git***.
Copy the is repository url and enter under ***Repository URL***. If it is a private repo ensure to provide your git credential.
Under **Branches to build**, enter ```*/main``` or ```*/master``` depending on your naming. 
Scroll down to ***Build Steps***, select ***Execute shell*** from the dropdown and enter the command, ```python3 helloworld.py```. 
This will execute the helloworld.py script in the repo. To confirm the version of python installed in your jenkins server, you can run a bash shell in your container and enter ```python``` or ```python3``` Click ***save***.
Click ***Build Now*** in the next screen.

### Accessing Build History
Click on the build number under ***Build History*** to access the build console and view result.

## alpine/socat container to forward traffic from Jenkins to Docker Desktop on Host Machine

https://stackoverflow.com/questions/47709208/how-to-find-docker-host-uri-to-be-used-in-jenkins-docker-plugin
```
docker run -d --restart=always -p 127.0.0.1:2376:2375 --network jenkins -v /var/run/docker.sock:/var/run/docker.sock alpine/socat tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock
docker inspect <container_id> | grep IPAddress
```

## Using my Jenkins Python Agent
```
docker pull devopsjourney1/myjenkinsagents:python
```
