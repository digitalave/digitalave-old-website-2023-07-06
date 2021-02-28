---
layout: post
authors: [dimuthu_daundasekara]
title: 'How to Install and Configure SonarQube 8 on Ubuntu 18.04'
image: /assets/img/post-imgs/SonarQube-Ubuntu/sonarqube.jpg
tags: [Jenkins, CICD, Automation,Continuous Integration, Continuous Delivery,SonarQube]
category: devops
comments: true
last_modified_at: 2020-01-31
---

<style>
.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }
</style>
<div class='embed-container'>
    <iframe src='https://www.youtube.com/embed/dY_kcdgzzyY?&autoplay=1' frameborder='0' allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen>
    </iframe>
</div>

### Introduction:
SonarQube is an open-source tool which can used for analyze quality of the source code. It can detect your code bugs, vulnerabilities, security black holes and code smells.
SonarQube empowers you to write cleaner and safer codes without breaking standards and code methodologies.

SonarQube is bundled with static code analyzer for more than 27 programming languages.
SonarQube performs continues code inspection using thousands of automated static code analysis rules.

We can perform code analysis manually or integrating with CICD DevOps tools such as Jenkins, Auzre DevOps and Bamboo.

And, also you can integrate SonarQube with your IDE tool such as Visual Studio and Eclips.

SonarQube provides code reliability by preventing bugs and application security by fixing vulnerabilities that compromise your code.

SonarQube is an open-source platform. Which uses for static code analysis and continuous inspection of code quality. SonarQube can detect bugs, code smells and security vulnerabilities.SonarQube empowers developers to write cleaner and safer code.

SonarQube provides code reliability by preventing bugs and application security by fixing vulnerabilities that compromise your code.

SonarQube is able to integrate with CI/CD tools such as Jenkins, Azure DevOps, GitHub, GitLab, Bitbucket and many more.

### Features:

**Build Integration - Jenkins, Azure DevOps, Bamboo, etc...**

**IDE Integration - Visual Studio, Eclips, InteliJ, etc...**

**Other Pipeline Integration** 

### Prerequisites:

`OS - Ubuntu 18.04 / 16.04 LTS / Debian`

`RAM - 4GB Minimum RAM`

`CPU - 1vCPU`

`JAVA - Oracle JRE 11 or OpenJDK 11`

**NOTE:** *Please make sure to install compatible Java version before continue the installation.*

REF: <a href="https://docs.sonarqube.org/latest/requirements/requirements/" target="_blank">https://docs.sonarqube.org/latest/requirements/requirements/</a>

In this tutorial, I will going to install SonarQube Community Edition v8.3 on Ubuntu 18.04. Which required OpenJDK 11 packages to be installed on the system.

SonarQube 8.3
OpenJDK 11
PostgreSQL 12

### STEP 01: Set kernel Parameters and System Limits
First  of all we need to  perform some OS level modifications to "Kernel Parameters" and "System limits"

Append these entries to bottom of the "sysctl.conf" file.

```bash
sudo vim /etc/sysctl.conf
```

```bash
vm.max_map_count=262144
fs.file-max=65536
ulimit -n 65536
ulimit -u 4096
```

And, also append these entries at the end of the "limits.conf" file.

```bash
sudo vim /etc/security/limits.conf
```

```bash
sonarqube   -   nofile   65536
sonarqube   -   nproc    4096
```

Make sure to reboot systems once above changes made. Therefore New changes will reflect after the reboot.

### STEP 02: Install OpenJDK 11

**Download and Install JDK 11 APT Repositories**

Now, It's time to install Java on your system. Don't forget to install compatible Java version with you SonarQube version.


First perform a system update.

```bash
sudo apt-get update -y
```

Then, Install OpenJDK 11

```bash
sudo apt-get install openjdk-11-jdk -y
```

**Set Default JDK Version**

Then, You need to set newly installed  Java version as your default Java version

```bash
sudo update-alternatives --config java
```


**Verify Install Java Version**

```bash
java -version
```

### STEP 02: Install and Configure PostgreSQL Database for SonarQube


In this tutorial I'm using PostgreSQL as my database engine. You also can use other compatible DB such as MySQL or Oracle.

It's always better to check version compatibility matrix, which recommends by SonarQube developers.

REF: <a href="https://docs.sonarqube.org/latest/requirements/requirements/
" target="_blank">https://docs.sonarqube.org/latest/requirements/requirements/
</a>

Let's do a system update again.

```bash
sudo apt update
```


**Import Trusted PGP Key and PostgreSQL APT Repo**

Then, Install trusted GPG key on your system. And create a repository file for PostgreSQL.

```bash
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -

sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
```

