## Simple DevOps Project -3 


1. Launch an EC2 instance for Docker host

2. Install docker on EC2 instance and start services 
  ```sh 
  yum install docker
  service docker start
  ```

3. create a new user for Docker management and add him to Docker (default) group
useradd dockeradmin
passwd dockeradmin
usermod -aG docker dockeradmin

4. Enable passwd based authentication :
    vi /etc/ssh/sshd_config
5. Restart SSHd
    service sshd restart

4. Write a Docker file under /opt/docker

```sh
mkdir /opt/docker

### vi Dockerfile
# Pull base image 
From tomcat:8-jre8 

# Maintainer
MAINTAINER "valaxytech" 

# copy war file on to container 
COPY ./webapp.war /usr/local/tomcat/webapps
```

5. Login to Jenkins console and add Docker server to execute commands from Jenkins  
Manage Jenkins --> Configure system -->  Publish over SSH --> add Docker server and credentials

6. Create Jenkins job 

A) Source Code Management  
 Repository : https://github.com/ValaxyTech/hello-world.git  
 Branches to build : */master  

B) Build
 Root POM: pom.xml  
 Goals and options : clean install package  
 
C) send files or execute commands over SSH
 Name: docker_host  
 Source files	: `webapp/target/*.war`
 Remove prefix	: `webapp/target`
 Remote directory	: `//opt//docker`  
 Exec command[s]	: 
  ```sh
  docker stop valaxy_demo;
  docker rm -f valaxy_demo;
  docker image rm -f valaxy_demo;
  cd /opt/docker;
  docker build -t valaxy_demo .
  ```

D) send files or execute commands over SSH  
  Name: `docker_host`  
  Exec command	: `docker run -d --name valaxy_demo -p 8090:8080 valaxy_demo`  

7. Login to Docker host and check images and containers. (no images and containers)

8. Execute Jenkins job

9. check images and containers again on Docker host. This time an image and container get creates through Jenkins job

10. Access web application from browser which is running on container
```
<docker_host_Public_IP>:8090
```
chown -R dockeradmin:dockeradmin /opt/docker

                                                                       DOCKER VOLUMES
                                                                       
                                                                       
# Manage data in Docker
By default all files created inside a container are stored on a writable container layer. This means that:  
The data doesn’t persist when that container no longer exists, and it can be difficult to get the data out of the container if another process needs it.

To list out existing volumes 

Command :docker volume ls

To create a container for stateless applications 
  
  docker run -it --name vtuatjenkins01
  
Docker has 3 options for containers to store files in the host machine, so that the files are persisted even after the container stops:

### docker volume types: 
1. anonymous volumes
1. named voluems
1. host volume or bind volumes
 
#### Anonymous Volumes
Create a container with an anonymous volume which is mounted as `/data01` on container. in this case we mention container directory name. On host system it maps to a random-hash directory under /var/lib/docker directory. 
  ```sh 
  docker run -it --name vtwebuat01 -v /data01 nginx /bin/bash
  ```
On Host to verify volume 
  ```sh
  docker volume ls 
  docker inspect <volume_name>
  ```

#### Named Volumes 
Create a container with a named volume name which is mounted as `/data01` on container. You can see volume name as `vtwebuat02_data01_val`
  ```sh 
  docker run -it --name vtwebuat02 -v vtwebuat02_data01_val:/data01 nginx /bin/bash
  ```
Create a named volume then attach volume to a container 
  ```sh 
  docker volume create vtuatweb03_data01_vol
  docker run -it --name vtuatweb03 -v vtuatweb02_data01_vol:/data01 nginx /bin/bash
  ```

Create a named volume with size 
  ```sh
  docker volume create --opt o=size=100m --opt device=/data3 --opt type=btrfs vtuatdb02_data3 // to create volume with size 
  ```

#### Host Volumes 

Create a host volume 
  ```sh 
  mkdir /opt/data02
  docker run -it --name vtwebuat03 -v /opt/data02:/data02 nginx /bin/bash
  ```

### 💡 Help/Suggestions or 🐛 Bugs

Thank you for your interest in contributing to our project. Whether it's a bug report, new feature, correction, or additional documentation or solutions, we greatly value feedback and contributions from our community.


### 🏷️ Metadata

**Level**: 100

