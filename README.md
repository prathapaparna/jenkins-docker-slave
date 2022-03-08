# jenkins-docker-slave
## install jenkins on linux machine
1.	Create 2 ec2 instances
2.	 Install Jenkins in one server
3.	 ```
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
  ### write dockerfile and create docker image for slaves
      docker build -t <reponame>/<imagename>:<tagname> .
  docker push <reponame>/<imagename>:<tagname>
  
## configure cloud in jenkins
install docker,docker pipeline, aws, aws pipeline steps plugins in jenkins
  Head over to Jenkins Dashboard --> manage jenkins --> manage plugins ---> avilable
  
  Head over to Jenkins Dashboard –> Manage Jenkins –> manage nodes and clouds  configure cloud 
  
  ![image](https://user-images.githubusercontent.com/99127429/157305867-2ae952e6-3432-4311-aeb1-1ba9a68b9822.png)

  ![image](https://user-images.githubusercontent.com/99127429/157305899-d268be82-0dd0-4a4b-9d75-54f7fda9dcc5.png)
  
![image](https://user-images.githubusercontent.com/99127429/157305932-7494df66-5a0c-4fdc-8bfd-54be6bd2198e.png)
  
  

  

  
  
  
