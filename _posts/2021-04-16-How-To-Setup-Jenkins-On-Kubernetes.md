---
layout: post
authors: [dimuthu_daundasekara]
title: 'How To Setup Dynamically Scalable Jenkins Master Slave Setup on Kubernetes Clsuter'
subtitle: Stateful Sets, Persistent Volumes, Persistent Volume Claims, Storage Class, Daemon Sets, Services, Ingress Controllers
description: "In this tutorial, I will walk you through how to setup scalable Jenkins server on Kubernetes cluster using set of kubernetes deployment manifest YAMLs."
image: /assets/img/post-imgs/jenkins-k8s-deploy/Jenkins-on-kubernetes.jpg
tags: [Kubernetes, k8s, kubernetes basics, Kubernetes Objects Explained ,kubernetes tutorial, Stateful Sets, Persistent Volumes, Persistent Volume Claims, Storage Class, Daemon Sets, Services, Ingress Controllers,featured]
category: DevOps, Kubernetes
comments: true
---

# How to Create a Dynamically Scalable Jenkins Master Slave Setup on Kubernetes Clsuter 

Jenkins is the most commanly used and populer opensource CI/CD tool which use by many famous companies arrond the world. We don't need to  have a second though of it since the biggest companies like Facebook, Udemy, NetFlix, LinkedIn and many more companies using Jenkins with confidence. 

When your Jenkins running on traditional method, (on a VM, baremetal server, single container) code base growing accrodingly and your code build jobs will increase, You also might running numorous build jobs in pararall. This will lead to redundent resource utilizations and slowness of your delivery pipelines and build/deploy jobs. 

#### So, Is there anyway to overcome this slowness ? 

##### Yes, For sure... Here's the way.

In this tutorial, I will walk you through how to setup scalable Jenkins server on Kubernetes cluster using set of kubernetes deployment manifest YAMLs. Use of kubernetes YAML files will help you to track , edit, modify changes and reuse deployemnts as much as you want.

<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-kubernetes-01.png" width="auto" width="100%">

#### Jenkins Scalability:

Scalability can be define as the ability of the system to exapand it capabilites to handle an additional load as required.

Jenkins scaling is based on the Master-Slave model. Which means, You will have several Jenkins agent instances (Salves) and one Master jenkins instance which is responsible to destribute jobs among Jenkins slaves. Jenkins Slaves are doing the jobs when Jenkins Master triggered the build/deploy pipeline. 

Sounds great! right? 

Have a look at this image and it will feed more idea into your mind.

IMG: scalable-jenkins-master-slave-on-kubernetes-3
<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-kubernetes-02.png" width="auto" width="100%">

#### You Got Benifits !!!
* Multi-Tasking : You can run many more build jobs in parallel
* Self-Healing : Replacing courroupted Jenkins instances automatically.
* Cost Saving : Spinning up and removing slaves dynamically based on need. 
* Even Load Distribution : Spreading the load accross deferent physical macines/ VMs / Nodes when required.

By spining up jenkins on kubernetes you will get the power of inifity stones of the kubernetes. :) 

## STEP 01: Create a Namespace for the Jenkins Deployment

Kubernetes namespace provides and aditional layer of isolation for your jenkins deployment and allow you more control over CI/CD environment.

create a file named with jenkins-ns.yaml and define your namespace name here.

```bash
vim jenkins-ns.yaml
```

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: jenkins
```
Apply changes the kubernetes cluster

```bash
kubectl apply -f jenkins-ns.yaml
```

Use following command to list all exisiting namespaces

<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-on-kubernetes-1.jpg" width="auto" width="100%">

## STEP 02: Create Persistent Volume

Creating a persistence volume is essential, since all of your Jenkins jobs, plguins, configurations should be persist. If one pod dies then another new pod can continue with persitent data from your volume. 

Ok, Then Let's create a Persitent Volume

Create a new file named with "jenkins-pv.yaml" and phast below content into your file.

```bash
vim jenkins-deployment.yaml
```

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: jenkins-home-pv
  namespace: jenkins
  labels:
    usage: jenkins-shared-deployement
spec:
  storageClassName:  default # managed-premium
  capacity:
    storage: 5Gi # Change Me
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: "/var/jenkins_home"
```

Let's apply the change to the Kubernetes cluster. (Make sure to append the "-n jenkins" option)

```bash
kubectl apply -f jenkins-pv.yaml -n jenkins
```
<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-on-kubernetes-2.jpg" width="auto" width="100%">

## STEP 03: Create a PersistentVolumeClaim 

"PersistentVolumeClaim" is request for storage with a specific size and access mode. In this case I'm  going to claim 5GB of storage to my "Jenkins_Home"

Now, Create another file named with "jenkins-pvc.yaml" and phast below content.

Note: Make sure to match the selector lables If you are planing to change the manifest as you like. (Ex: usage: jenkins-shared-deployement)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: jenkins-home-pvc 
  namespace: jenkins
  labels:
    app: jenkins
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: default # managed-premium 
  resources:
    requests:
      storage: 5Gi # Change Me
```

Then, apply changes to the cluster.

```bash
kubectl apply -f jenkins-pvc.yaml -n jenkins
```

<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-on-kubernetes-3.jpg" width="auto" width="100%">

## STEP 04: Create a Deployment Manifest

Here, I'm using Jenkins LTS docker image and expose port 8080 for the web access and port 50000 for the jenkins jnlp service which use to access Jenkins workers respectivly.

Let's create a Kubernetes deployment manifet for the Jenkins server.
Here I'm using Jenkins LTS image and expose port number 8080 and 50000 for the web access and inter master agent commiumications respectivly.

Now, create a file named with "jenkins-deployemt.yaml" and phast below conent.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jenkins-master
  namespace: jenkins
  labels:
    app: jenkins
spec:
  replicas: 1
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      securityContext: # Set runAsUser to 1000 to let Jenkins run as non-root user 'jenkins' which exists in 'jenkins/jenkins' docker image.
        runAsUser: 0
        fsGroup: 1000
      containers:
      - name: jenkins-master
        # https://github.com/jenkinsci/docker
        image: jenkins/jenkins:lts #jenkinsci/jenkins:2.150.1
        imagePullPolicy: IfNotPresent
        env:
        - name: JENKINS_HOME
          value: /var/jenkins_home
        - name: JAVA_OPTS
          value: -Djenkins.install.runSetupWizard=false
        - name: JAVA_OPTS
          value: "-Xmx8192m"
        # - name: COPY_REFERENCE_FILE_LOG
        #   value: $JENKINS_HOME/copy_reference_file.log
        ports:
        - name: jenkins-port # Access Port for UI
          containerPort: 8080
        - name: jnlp-port # Inter agent communication via docker daemon
          containerPort: 50000
        resources: # Resource limitations 
          requests:
            memory: "256Mi" # Change Me
            cpu: "50m"
          limits:
            memory: "4Gi" # Change Me
            cpu: "2000m"
        volumeMounts:
        - name: jenkins-home-volume
          mountPath: "/var/jenkins_home"
      volumes:
      - name: jenkins-home-volume
        persistentVolumeClaim:
          claimName: jenkins-home-pvc 
```

Then apply changes to the cluster by execcuting following command.

```bash
kubectl apply -f jenkins-deployment.yaml -n jenkins
```

## STEP 05: Create a Service Manifest

Now, We have deployed Jenkins on kubernetes. But, It is stil not accessible

Services in Kubernetes use to expose applications running on Kubernetes pods. Now we need to grant access to the jenkins via kubernetes service. So, We need to define a service for jenkins server. In here we exposing port 8080 and 50000.

Ok, Let's create a file named with "jenkins-svc.yaml" and paste content below.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: jenkins-ui-service
  namespace: jenkins
spec:
  type: ClusterIP # NodePort, LoadBalancer 
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
      # nodePort: 30100
      name: ui
  selector:
    app: jenkins
--- 
apiVersion: v1
kind: Service
metadata:
  name: jenkins-jnlp-service
  namespace: jenkins
spec:
    type: ClusterIP # NodePort, LoadBalancer
  ports:
    - port: 50000
      targetPort: 50000
  selector:
    app: jenkins
```

And the apply the chanes to the cluster.

```bash
kubectl  apply -f jenkins-svc.yaml -n jenkins
```
<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-on-kubernetes-4.jpg" width="auto" width="100%">

Now you can access the Jenkins Dashboard. 

## STEP 06: Port-Forwarding (Optinal)

As you can see in the service manifest, I have used "ClusterIP" as my network service. 
So, I need to port-forward jenkins-UI service to be able to access through my (localhost) computer. 

If you planning to deploy on a Cloud service you will get many more options such as ingress controllers so on. We discus them in a next tutorial.

For now, I'm going to port forward Jenkins-UI (8080/TCP) to my localhost as I'm deploying jenkins on Minikube.

Get the Jenkins-UI service details

```bash
dimuthu@srv01:~/jenkins-k8s$ kubectl get svc -n jenkins
NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
jenkins-jnlp-service   ClusterIP   10.106.232.10   <none>        50000/TCP   10m
jenkins-ui-service     ClusterIP   10.98.225.50    <none>        8080/TCP    10m
```

Now, I'm going to forward my port 8080 into my local machine. Simply run the following command.

```bash
dimuthu@srv01:~/jenkins-k8s$ kubectl port-forward service/jenkins-ui-service 8080:8080 -n jenkins
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
```

## STEP 05: Access Jenkins Server
Now, You can access your Jenkins server through the web browser.

`http://<YOUR-HOST-NAME-OR-IP>:8080/`

For the 1st time, Jenkins will prompt to enter an unlock password. You can get the passwrd by looking at the logs for jenkins deployment or pod.

Get the name of the deployment.

```bash
kubectl get deployments -n jenkins
```
Look for initial password in the deployment logs.
In order to get the initial password You need to execute the following command on your kubectl terminal and copy the output password and paste it into the Administrator password text box.

<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-on-kubernetes-5.jpg" width="auto" width="100%">

```bash
kubectl logs deployment/jenkins-master  -n jenkins
```
> 706f68110c5b4b3a8846c1208f0c1c7c

<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-on-kubernetes-6.jpg" width="auto" width="100%">

Log output will look like below.

```bash
dimuthu@srv01:~/jenkins-k8s$ kubectl logs jenkins-master-d654bbdf5-5bsfz -n jenkins
Running from: /usr/share/jenkins/jenkins.war
webroot: EnvVars.masterEnvVars.get("JENKINS_HOME")
2021-04-14 18:48:28.430+0000 [id=1]     INFO    org.eclipse.jetty.util.log.Log#initialized: Logging initialized @894ms to org.eclipse.jetty.util.log.JavaUtilLog

*************************************************************
*************************************************************
*************************************************************

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

706f68110c5b4b3a8846c1208f0c1c7c

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

*************************************************************
*************************************************************
*************************************************************
```

`Troubleshooting: If the "initialAdminPassword" not available, You may have to re-deploy the jenkins and try again.`

**REF:** <a href="https://stackoverflow.com/questions/48611411/initialadminpassword-file-is-not-created-in-jenkins-folder-in-windows-10-os" target="_blank">Admin Password Not Available</a>


## STEP 07: Install Plugins

In this section, You will need to install Jenkins plugins according to how you going to use them. You can choose either option. 

In this case, I'm choosing the "select plugins to install option."

<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-on-kubernetes-7.jpg" width="auto" width="100%">

Once you choose the required plugins, click next.
This plugin installation may take considerable time. Please wait until completed.

<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-on-kubernetes-8.png" width="auto" width="100%">

### STEP 08: Create First Admin User

Next, You have to provide your "name", username", "password", and "email" for the Jenkins admin user.

Next, You need to provide your FQDN or IP address. By default, the IP address will automatically load into the "instance URL" section.

<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-on-kubernetes-10.jpg" width="auto" width="100%">

Provide a domain name as required. (optional)

<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-on-kubernetes-11.jpg" width="auto" width="100%">

<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-on-kubernetes-12.jpg" width="auto" width="100%">

<img src="/assets/img/post-imgs/jenkins-k8s-deploy/jenkins-on-kubernetes-13.jpg" width="auto" width="100%">

Now we have deployed our Jenkins server on a Kubernete cluster. Now we can use this jenkins server as normally. But, We also can setup Jenkins Slave agent to our Jenkins Master. 

We will discuss how to setup Jenkins-Slaves in the next tutorial. You can visit the next session from this link.

In this lession you leared...

* How to setup how to deploy kubernetes cluster
* How to use persitenet volumes, attaching services to the depoyment.

I hope you learn something new from this tutorial. If you facing any difficulties, please comment below. I'll regularly jump into comments.

**And also don't forget to subscribe my <a href="https://www.youtube.com/channel/UCovlVsoRVItner26ZJPBjmQ" target="_blank">YouTube</a> channel for upcoming tutorials.**





