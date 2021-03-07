---
layout: post
authors: [dimuthu_daundasekara]
title: 'How To Run Jenkins In Docker Using Docker Compose With Volumes - Step by Step Explanation'
image: /assets/img/post-imgs/Jenins-Install-ubuntu/Jenkins-Docker-Thumb.jpg
tags: [Jenkins, CICD,Continuous Integration, Continuous Delivery, Docker, Docker-Compose]
category: devops
comments: true
---

# How to Run Jenkins on Docker with Persistent Volumes

Do you want to setup Jenkins on Docker? But You may just curious about persitency of your jenkins data. Ok, The I will explain them here step by step.

**You may arise few questions like...**

* How can I access jenkins data from out side of the container? 

* Are these data persitent even after the jenkins container restarted?

* What happens if container crashed?

* how do I backup data after jenkins is containerized? 

In this tuorial I will walk you thruough how to setup Jenkins server on Docker with a persistent volumes. First, I'll show you each step manually and then I'll use docker-compose to make things faster and reusable.

#### In this tutorial, I will walk you through following sections.

##### Section 01. Run Jenkins on a Docker Container Manually

##### Section 02. Run Jenkins on a Docker Container Using Docker Compose ( make things faster and reusable)

## What is jenkins ?

Jenkins is an open-source continues integration and continues deployment tool. Which can use to automate application building, testing and deploying. Jenkins is the most popular CI/CD automation tool. 

We can extend the capability of Jenkins by integrating with many other tools such as SonarQube and which can use to mesure code quolity and standered before the deployment performed. And also, Jenkins supports thousands of plugins which can use to make things easier.

### Prerequisites

Hardware Requirement

| Minimum Requirement | Recommended Requirement |
|---------------------|------------------------|
| 256MB+ of RAM       | 1GB+ of RAM            |
| 1GB+ HDD Capacity   | 50GB+ HDD Capacity     |



## Sections 01: Run Jenkins On Docker Container Manually

### STEP 01: Install Docker and Docker-Compose On Ubuntu

You can directly jump into the "STEP 02", If you have already installed Docker Engine on your host. In the 1st step I'm setting up my Ubuntu host to be able to run Docker container.

> Note: You can skip this section and jump to "STEP 02" if you already installed Docker on your HOST. Or elese you are going to do this on a Cloud Platform.

### Install Docker Enginer

1. Setup The Repository

Update Ubuntu once.
```bash
sudo apt-get update -y
```
2. Install Additionally Required Packages.
```bash
sudo apt-get install \
     apt-transport-https \
     ca-certificates \
     curl \
     gnupg-agent \
     software-properties-common
```
3. Install Official Trusted GPG Key
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
4. Add Docker Stable Repository
```bash
sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
```
5. Update OS 
```bash
sudo apt-get update -y
```

6. Install Docker Docker Engine
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io -y
```
7. Start and Enable Docker Daemon Service 
```bash
sudo systemctl enable docker.service
sudo systemctl start docker.service
```

For the Production usage, always use official, Long-Term Support(LTS) releases. But, the offcial Docker image doesn't comes with docker CLI inside the Jenkins image.

### Install Docker Compose

1. Docwnload Docker Compose Binary 

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.28.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

2. Provide an executable permissions

```bash
sudo chmod +x /usr/local/bin/docker-compose
```

3. Set binary executable path

```bash
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

4. Verify Installation

```bash
docker-compose --version
```

### STEP 02: Create Docker Bridge Network

First, I'm going  to create a bridge network named as "jeknins-net". And I will attach all docker containers into this network.

```bash
sudo docker network create jenkins-net
```

### STEP 03: Create Docker Volumes

Here we are going to create volumes and map them into Jenkins container. We may need to these volumes to store the jenkins data persistantly.  This volume use to make sure you don't loose your Jenkins data even after a reboot or crash container situations.

I will create create Docker volume to  store "JENKINS_HOME" directory which use to mount into docker host machine.

| Docker Host          | Jenkins Container |
|----------------------|-------------------|
| jenkins-data         | /var/jenkins_home |

```bash
sudo docker volume create jenkins-docker-data
```

### STEP 04: Start Jenkins Server Container

In this step, We are going to use same bridged network created as "jenkins-net" at the STEP 02. Basically, Docker-Agent(Dind) and Jenkins Servier will be on the same Docker network. 

```bash
sudo docker container run \
  --name jenkins-server \
  --detach \
  --restart unless-stopped \
  --network jenkins-net \
  --hostname jenkins \
  --publish 8080:8080 \
  --volume jenkins-data:/var/jenkins_home \
  jenkins/jenkins:lts
```

If you were wondering what the arguments stand for, here is what each means:

-d: detached mode

-v: attach volume

-p: assign port target

—name: name of the container


TCP port 8080 is using to access Jenkins dashboard. Insetead of the default port 8080,  we can use any other un commen TCP port as well.

In the above command we can see "--volume" is the most important argument whivh helps Docker link the volume into the docker container and preserve data persistency. The jenkins docker container path "/var/jenkins_home" where the jeknins container is storing the data. So, You need to  worry about the docker container to restart or even after a container crashed. You can easily mount this volume into new container in such a situation.

Finally, You can see the out like this.

```bash
dimuthu@svr05:~$ sudo docker container ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED         STATUS         PORTS                               NAMES
b45233ebcbba   jenkins/jenkins:lts   "/sbin/tini -- /usr/…"   7 seconds ago   Up 5 seconds   0.0.0.0:8080->8080/tcp, 50000/tcp   jenkins-server
```

### STEP 05: Access Jenkins Console

Access your Jenkins Sever through the web browser

```bash
http://<DOCKER-HOST-IP>:8080/
```
### STEP 06: Get Jenkins Init Password  

In the post configuration wizard, It will asking for an adminitrator password. So,You need to access running jenkins container to get this password.  You can get the initial, default Administrator password using following command


You can use jeknins container ID or name and the following command.
According to my setup, jenkins-server is our Jenkins docker container name. And copy the output and paste it in the text box.

```bash
sudo docker exec jenkins-server cat /var/jenkins_home/secrets/initialAdminPassword
```

```bash
02ac16bd27af4e8c8d1ffc82841f419f
```

Or else you can use the following command. Replace your container name or ID. 

```bash
docker container exec \
    [CONTAINER ID or NAME] \
    sh -c "cat /var/jenkins_home/secrets/initialAdminPassword"
```


<img src="/assets/img/post-imgs/jenkins-docker-per-vol/1.png" width="auto" width="100%">


### STEP 07: Install Jenkins Plugins

As usually, We can install required plugins from here. In my case, I'll choose "install suggested plugin" options. And please wait untill installation completed.

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/2.png" width="auto" width="100%">

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/3.png" width="auto" width="100%">
### STEP 08: Create First Admin User

Create and administrator user providing your details here.

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/4.png" width="auto" width="100%">

### STEP 09: Instance configuration

After creating the admin user, next move on to setup the Instance configuration. Since you neet to access jenkins internally, leave the URL to your localhost URL. either you can provide your FQDN or IP address. If you not sure, leave this url as it is. 

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/5.png" width="auto" width="100%">

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/6.png" width="auto" width="100%">

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/7.png" width="auto" width="100%">

Now, Jenkins server has been setup successfully. Now, I'm going to check the data persistency. 


### STEP 10: Confirm Jenkins Data Persistency 

Once you logged into Jenkins dashboard, I'm going to create a sample job to whether Jenkins is working fine.

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/8.png" width="auto" width="100%">

Now head-over to jenkins dashboard and click on "New Item" 

In the next scree, enter a job name. In this case I'm naming it as HelloWorld. and then choose "Freestyle" project option.

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/9.png" width="auto" width="100%">

Then, In the next screen move on to "Build" tab  and click on the "Add Build Step" button and choose "Execute Shell" option.

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/10.png" width="auto" width="100%">

Then save the job and click on "Build Now" button to start the job.

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/11.png" width="auto" width="100%">

Click on the Build Status (blue ball) under Build History (left sidebar) to view the console output. You should see that our command ran with no problems.

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/12.png" width="auto" width="100%">

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/13.png" width="auto" width="100%">

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/14.png" width="auto" width="100%">

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/15.png" width="auto" width="100%">

<img src="/assets/img/post-imgs/jenkins-docker-per-vol/1.png" width="auto" width="100%">

Other than the step that I've mention above, there so many ways to create build jobs. That's what makes Jenkins such a fantastic continuous deployment tool.

## Section 02. Run Jenkins on a Docker Container Using Docker Compose ( Make things faster and reusable)

### Run Jenkins on Docker Using Docker-Compose

In the previous sections, I've created docker container which runs jenkins instance. Docker containers are ephemeral. These containers can be stopped or destryed and create new once anytime using docker images. If something bad happns it may cause jenkins data losses too.

Now, I'm going to do the same setup using docker-compose with persitence data volume mounts.

##### Where is the Jenkins Data in Docker Container ?

It's always better to understand where the jenkins data stored. To list jenkins data, you can use following command.

```bash
docker exec jenkins-server ls -l /var/jenkins_home
```

In here "jenkins-server" is my jenkins container name. So, You can replace it with your container name. After executing the command, You can see the output looks like below.

```bash
total 108
-rw-r--r--  1 jenkins jenkins  1647 Mar  7 14:23 config.xml
-rw-r--r--  1 jenkins jenkins    53 Mar  7 14:01 copy_reference_file.log
-rw-r--r--  1 jenkins jenkins   156 Mar  7 14:01 hudson.model.UpdateCenter.xml
-rw-r--r--  1 jenkins jenkins   370 Mar  7 14:18 hudson.plugins.git.GitTool.xml
-rw-------  1 jenkins jenkins  1712 Mar  7 14:01 identity.key.enc
-rw-r--r--  1 jenkins jenkins     7 Mar  7 14:23 jenkins.install.InstallUtil.lastExecVersion
-rw-r--r--  1 jenkins jenkins     7 Mar  7 14:23 jenkins.install.UpgradeWizard.state
-rw-r--r--  1 jenkins jenkins   183 Mar  7 14:23 jenkins.model.JenkinsLocationConfiguration.xml
-rw-r--r--  1 jenkins jenkins   171 Mar  7 14:01 jenkins.telemetry.Correlator.xml
drwxr-xr-x  3 jenkins jenkins  4096 Mar  7 16:21 jobs
drwxr-xr-x  3 jenkins jenkins  4096 Mar  7 14:01 logs
-rw-r--r--  1 jenkins jenkins   907 Mar  7 14:01 nodeMonitors.xml
drwxr-xr-x  2 jenkins jenkins  4096 Mar  7 14:01 nodes
drwxr-xr-x 79 jenkins jenkins 12288 Mar  7 14:18 plugins
-rw-r--r--  1 jenkins jenkins   129 Mar  7 16:34 queue.xml
-rw-r--r--  1 jenkins jenkins    64 Mar  7 14:01 secret.key
-rw-r--r--  1 jenkins jenkins     0 Mar  7 14:01 secret.key.not-so-secret
drwx------  4 jenkins jenkins  4096 Mar  7 16:35 secrets
-rw-rw-r--  1 root    root     7152 Feb 10 17:58 tini_pub.gpg
drwxr-xr-x  2 jenkins jenkins  4096 Mar  7 14:18 updates
drwxr-xr-x  2 jenkins jenkins  4096 Mar  7 14:01 userContent
drwxr-xr-x  3 jenkins jenkins  4096 Mar  7 14:23 users
drwxr-xr-x 11 jenkins jenkins  4096 Mar  7 14:01 war
drwxr-xr-x  2 jenkins jenkins  4096 Mar  7 14:18 workflow-libs
drwxr-xr-x  3 jenkins jenkins  4096 Mar  7 16:33 workspace
```

##### Where is the Jenkins Data in Docker Host ?

As we done before, Our Jenkins container's "JENKINS_HOME" derectory is mounted to "jenkins_home" derectory in our Docker host machine.

You can find you docker volume using the command below.

```bash
dimuthu@srv01:~/$ docker volume inspect jenkins-data 
[
    {
        "CreatedAt": "2021-03-07T16:34:42Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/jenkins-data/_data",
        "Name": "jenkins-data",
        "Options": null,
        "Scope": "local"
    }
]
```

According to the output, You can see the "JENKINS_HOME" derectory is mounted on "/var/lib/docker/volumes/jenkins-data/_data" docker volume. Which means, your data is persitence under that derectory and it is availble even container is deleted. So, data will be remain on Docker host.

### STEP 03: Create Docker-Compose.YAML

The another best way to run jenkins is using docker-compose command. By using docker-compose.yaml you can deploy multiple jenkins container more quickly.

Open your favorite test editor and create new file named "docker-compose.yaml" and paste below YAML manifest into the file. And the save and exit.

```bash
sudo vim docker-compose.yml
```


```yaml
version: "3.9"

services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins-server
    privileged: true
    hostname: jenkinsserver
    user: root
    labels:
      com.example.description: "Jenkins-Server by DigitalAvenue.dev"
    ports: 
      - "8080:8080"
      - "50000:50000"
    networks:
      jenkins-net:
        aliases: 
          - jenkins-net
    volumes: 
     - jenkins-data:/var/jenkins_home
     - /var/run/docker.sock:/var/run/docker.sock
     
volumes: 
  jenkins-data:

networks:
  jenkins-net:
```

You can change this docker-compose file as you want. But, Here I'll explain most importent sections only.

* image = The base image name which used to create docker instance. Genaraly this will pulled from dockerhub docker registery.

* Ports = This defines port mappped between docker container and docker host machine
 
* volume = This defines container data storage volume mapped between docker container and docker host machine. This allows to access containerized data from the docker host. 

* Container_name = This defines name of the container that you going to spinup. In here I'm using "jenkins-server" as my container name.

### 04: Run Docker Container Using Docker-Compose In Detached Mode

```bash
docker-compose up -d
```

### 05: Access Jenkins Console

`http://<YOUR-IP-OR-FQDN>:8080/`

### 06: Get Init Administrator Password

```bash
docker exec jenkins-server cat /var/jenkins_home/secrets/initialAdminPassword
```

### Summary

In this article, you leaned to deploy Jenkins using impartivly and declarativly. And also you learned how to get hands dirty with docker volumes and how to mount them. 

You also leanred how to preserve container data on docker volumes by mounting docker volume in to docker container. Making the Jenkins settings persistent and consistent even if the Docker container is deleted.

If you are facing issues with the implementation please comment below. I will regularly reply here.

**Happy learning !** 

**And also don't forget to subscribe my <a href="https://www.youtube.com/channel/UCovlVsoRVItner26ZJPBjmQ" target="_blank">YouTube</a> channel for upcoming tutorials.**