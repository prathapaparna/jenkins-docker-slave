# jenkins-docker-slave
## install jenkins on linux machine
 Install Jenkins in one server
 ```
     Cd /opt
     yum install java-1.8.0-openjdk-devel -y
     wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
     rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key
     yum install jenkins -y
     systemctl start Jenkins
     systemctl enable Jenkins
```     
   
open port 8080 in security group  and open jenkins ui http://<ip>:8080
  
  copy initial password and unlock jenkins
  
  ![image](https://user-images.githubusercontent.com/99127429/157304755-0e230621-c911-47a2-93de-6cc712a3e616.png)
![image](https://user-images.githubusercontent.com/99127429/157304800-21788208-7b34-4ae2-ab11-7e5b171ea4f8.png)

  
## install docker on ubuntu machine
  
  Follow this link to install docker in ubuntu machine
  
https://linuxize.com/post/how-to-install-and-use-docker-on-ubuntu-18-04/
  
  
## Configure docker host with remote API

Log in to the server and open the docker service file /lib/systemd/system/docker.service. Search for ExecStart and replace that line with the following
  ```
 ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:4243 -H unix:///var/run/docker.sock
  ```
  ![image](https://user-images.githubusercontent.com/99127429/157305274-be8ea89a-259c-447e-ae11-e521a329b33a.png)
Reload and restart docker service.
 ``` 
sudo systemctl daemon-reload 
sudo service docker restart
```

Step 4: Validate API by executing the following curl commands. Replace 54.221.134.7 with your host IP.
 ``` 
curl http://localhost:4243/version
curl http://54.221.134.7:4243/version
```
 ## write dockerfile and create docker image for slaves
 
 ```
 vi Dockerfile
 ```
 
 ```
 FROM ubuntu:18.04
MAINTAINER Bibin Wilson <bibinwilsonn@gmail.com>

# Make sure the package repository is up to date.
RUN apt-get update && \
    apt-get -y install \
        apt-transport-https \
        ca-certificates \
        curl \
        software-properties-common \   
        git  
Run mkdir /.docker && \
    chmod 777 /.docker
# Add user jenkins to the image
RUN useradd -ms /bin/bash jenkins && \
# Set password for the jenkins user (you may want to alter this).
    echo "jenkins:jenkins" | chpasswd && \
    mkdir /home/jenkins/.m2

#ADD settings.xml /home/jenkins/.m2/
# Copy authorized keys
COPY .ssh/authorized_keys /home/jenkins/.ssh/authorized_keys

RUN chown -R jenkins:jenkins /home/jenkins/.m2/ && \
    chown -R jenkins:jenkins /home/jenkins/.ssh/

RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add - \
    && echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu xenial stable" >> /etc/apt/sources.list.d/additional-repositories.list \
    && echo "deb http://ftp-stud.hs-esslingen.de/ubuntu xenial main restricted universe multiverse" >> /etc/apt/sources.list.d/official-package-repositories.list \
    && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 437D05B5 \
    && apt-get update 
# docker
RUN apt-get -y install docker-ce 
#RUN systemctl start docker && systemctl daemon-reload && syastemctl enable docker
# docker-compose
RUN curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose \
    && chmod +x /usr/local/bin/docker-compose

# give jenkins docker rights
RUN usermod -aG docker root

# Install a basic SSH server
RUN apt-get install -qy openssh-server && \
    sed -i 's|session    required     pam_loginuid.so|session    optional     pam_loginuid.so|g' /etc/pam.d/sshd && \
    mkdir -p /var/run/sshd && \
# Install JDK 8 (latest stable edition at 2019-04-01)
    apt-get install -qy openjdk-8-jdk && \
# Install maven and git 
    apt-get install -qy maven && apt install git && \
# Cleanup old packages
    apt-get -qy autoremove 

# Standard SSH port
EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]
```
 ```
 docker build -t <reponame>/<imagename>:<tagname> .
 docker push <reponame>/<imagename>:<tagname>
 ```
  
## configure cloud in jenkins
install docker,docker pipeline, aws, aws pipeline steps plugins in jenkins
 
  Head over to Jenkins Dashboard --> manage jenkins --> manage plugins ---> avilable
  
  Head over to Jenkins Dashboard –> Manage Jenkins –> manage nodes and clouds  configure cloud 
  
  ![image](https://user-images.githubusercontent.com/99127429/157305867-2ae952e6-3432-4311-aeb1-1ba9a68b9822.png)

  ![image](https://user-images.githubusercontent.com/99127429/157305899-d268be82-0dd0-4a4b-9d75-54f7fda9dcc5.png)
  
![image](https://user-images.githubusercontent.com/99127429/157305932-7494df66-5a0c-4fdc-8bfd-54be6bd2198e.png)
  
  

  

  
  
  
