---
layout: post
authors: [dimuthu_daundasekara]
title: 'Install Wazuh on CentOS and RHEL An Intrusion Detection System'
image: /assets/img/post-imgs/wazuh/wazuh.png
tags: [Wazuh,Automatic Log Data Analysis, Intrusion Detection, Security Analytics, File Integrity Monitoring,Vulnerability Detection, Configuration Assessment, Incident Response, Regulatory Compliance, Cloud Security Monitoring, Containers Security]
category: devops
comments: true
last_modified_at: 2020-01-31
---

<style>
.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }
</style>
<div class='embed-container'>
    <iframe src='https://www.youtube.com/embed/TfyCYt6S5XQ?&autoplay=1' frameborder='0' allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen>
    </iframe>
</div>

### What is Wazuh

Wazuh is a free, open source and enterprise-ready security detection and monitoring solution.

Wazuh is born as a fork of OSSEC (HIDS) host based intrusion detection system. Later is was integrated with Elastic stack and OpenSCAP.

Which can perform threat detection, integrity monitoring, incident response and compliance.

##### Wazuh System consist with several components 

* OSSEC HIDS - Host Based Intrusion Detection System


* OpenSCAP - Open Vulnerability Assessment Language


* Elastic Stack - Filebeat, Elasticsearch, Kibana


* Wazuh is loaded with number of valued capabilities.

### Main Features:

#### 1. Security Analytics:

Wazuh is used to collect, aggregate, index and analyze security data which helping to detect intrusions,  threats and anomalies.

Endpoint Detection and Response (EDR)

Wazuh Agent actively perform security analysts discover, investigate and perform block a network attack, stop a malicious process or quarantine a malware infected file.

#### 2. Intrusion Detection

Wazuh-Agent scan the monitored system looking for malware, rootkits and suspicious anomalies. Also It can detect hidden files, clocked processes or  unregistered network listeners.

#### 3. Log Data Analysis

Wazuh-Agent read operating system and application logs, and forward them to a central Wazuh-Manager for rule-based analysis.

Which helps you to  aware of application or system errors,miss-configuration, attempted successful malicious activities, policy violations and many more.

#### 4. File Integrity Monitoring

Wazuh monitors the file system, identifying changes in content, permissions, ownership and attributes of files that you need to keep an eye on.

Also It can identify users and applications used to create or modify files.

#### 5. Vulnerability Detection

Wazuh agent pull software inventory data and send them to the Wazuh Manager server. Then, they matches with CVE (Common Vulnerabilities and Exposure) databases, in order to identify well-know vulnerable software.

Automated vulnerability assessment helps you find the weak spots in your critical assets and take corrective action before attackers exploit them to sabotage your business or steal confidential data.

#### 6. Configuration Assessment

Wazuh monitors system and application configuration settings to  ensure they are compliant with you security policies and standards.

Agent automatically performs periodic scan to detect applications that are know to be vulnerable, unpatched, or insecurely configured.

And also It alerts recommendations for  better configuration and security hardening. 

#### 7. Incident Response

Wazuh take action against active threats such  as blocking access from the threat source when certain criteria are met. 

#### 8. Regulatory Compliance

Wazuh provides some of necessary security  controls to become complaint with industry standards and regulations.

#### 9. Cloud Security

Wazuh helps monitoring cloud infrastructure as an API level.
It can pull security data from instances on well known cloud providers such as AWS, Azure, Google Cloud Platform.

#### 10. Containers Security

Wazhuh provides security  visibility  into your docker hosts and containers.

### STEP 01: Perform Prerequisites

##### A. Install latest yum repositories

```bash
yum install https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-12.noarch.rpm

yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm

yum install https://www.elrepo.org/elrepo-release-7.0-4.el7.elrepo.noarch.rpm

yum install http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm

yum install https://download1.rpmfusion.org/free/el/rpmfusion-free-release-7.noarch.rpm

yum install http://mirror.ghettoforge.org/distributions/gf/el/7/gf/x86_64/gf-release-7-10.gf.el7.noarch.rpm
```

##### B. Set Hostname with Fully Qualified Domain Name (FQDN)

```bash
[root@server1 ~]# vim /etc/hostname 

wazuh-elk
```

```bash
[root@server1 ~]# vim /etc/hosts

172.25.10.50    wazuh-elk.da.com        wazuh-elk
```

##### C. SELinux Configuration

```bash
[root@server1 ~]# vim /etc/selinux/config

SELINUX=permissive
```

##### D. Install Python3

```bash
root@server1 ~]# yum install python3
```

### SETP 02: Install Wazuh Manager

##### A. Install Development Tools, Compilers, and Other Required Packages.

```bash
[root@server1 ~]# yum install make gcc policycoreutils-python automake autoconf libtool
```

OpenSSL will be required to create a system certificate while authentication with Wazuh Agents.

```bash
[root@server1 ~]# yum install openssl
```

Install Latest EPEL-Release 

```bash
[root@server1 ~]# yum install epel-release yum-utils -y
```

Install Budild Dependencies of CPython

```bash
[root@server1 ~]# yum-builddep python34 -y
```

##### B. Download and Extract Wazuh Installation Files

```bash
[root@server1 ~]# curl -Ls https://github.com/wazuh/wazuh/archive/v3.11.1.tar.gz | tar zx

[root@server1 ~]# cd wazuh-3.11.1/
```

##### C. Run the "Install.sh" Script

This will guide you through the installation process.
When Script ask for what kind of installation need to perform, type "manager" to install the Wazuh Manager.

```bash
[root@server1 wazuh-3.11.1]# ./install.sh
```

```bash
** Para instalação em português, escolha [br].
 (en/br/cn/de/el/es/fr/hu/it/jp/nl/pl/ru/sr/tr) [en]: en

Wazuh v3.11.1 (Rev. 31116) Installation Script - http://www.wazuh.com

You are about to start the installation process of Wazuh.
You must have a C compiler pre-installed in your system.

 - System: Linux server1 3.10.0-862.el7.x86_64 (centos 7.5)
 - User: root
 - Host: server1
```

```bash
1- What kind of installation do you want (manager, agent, local, hybrid or help)? manager

2- Setting up the installation environment.

 - Choose where to install Wazuh [/var/ossec]: 

3- Configuring Wazuh.

  3.1- Do you want e-mail notification? (y/n) [n]: y 
   - What's your e-mail address? user@gmail.com

   - We found your SMTP server as: alt1.gmail-smtp-in.l.google.com.
   - Do you want to use it? (y/n) [y]: y

   --- Using SMTP server:  alt1.gmail-smtp-in.l.google.com.

  3.2- Do you want to run the integrity check daemon? (y/n) [y]: y

   - Running syscheck (integrity check daemon).

  3.3- Do you want to run the rootkit detection engine? (y/n) [y]: y

   - Running rootcheck (rootkit detection).

  3.4- Do you want to run policy monitoring checks? (OpenSCAP) (y/n) [y]: y

   - Running OpenSCAP (policy monitoring checks).

  3.5- Active response allows you to execute a specific
       command based on the events received.
       By default, no active responses are defined.

   - Default white list for the active response:
      - 172.25.10.1
      - 8.8.8.8
      
   - Do you want to add more IPs to the white list? (y/n)? [n]: n

  3.6- Do you want to enable remote syslog (port 514 udp)? (y/n) [y]: y

   - Remote syslog enabled.

  3.7 - Do you want to run the Auth daemon? (y/n) [y]: y

   - Running Auth daemon.

  3.8- Do you want to start Wazuh after the installation? (y/n) [y]: y

   - Wazuh will start at the end of installation.

  3.9- Setting the configuration to analyze the following logs:

    -- /var/log/audit/audit.log
    -- /var/ossec/logs/active-responses.log
    -- /var/log/messages
    -- /var/log/secure
    -- /var/log/maillog

 - If you want to monitor any other file, just change
   the ossec.conf and add a new localfile entry.
   Any questions about the configuration can be answered
   by visiting us online at https://documentation.wazuh.com/.


   --- Press ENTER to continue ---

4- Installing the system

 - Running the Makefile

Finished processing dependencies for wazuh==3.11.1
Installing SCAP security policies...
Generating self-signed certificate for ossec-authd...
Building CDB lists...


Created symlink from /etc/systemd/system/multi-user.target.wants/wazuh-manager.service to /etc/systemd/system/wazuh-manager.service.
Starting Wazuh...

 - Configuration finished properly.

 - To start Wazuh:
      /var/ossec/bin/ossec-control start

 - To stop Wazuh:
      /var/ossec/bin/ossec-control stop

 - The configuration can be viewed or modified at /var/ossec/etc/ossec.conf


---------------------------------
[root@server1 wazuh-3.11.1]# /var/ossec/bin/ossec-control start
Starting Wazuh v3.11.1...
Started ossec-csyslogd...
Started ossec-dbd...
2020/01/13 05:13:08 ossec-integratord: INFO: Remote integrations not configured. Clean exit.
Started ossec-integratord...
Started ossec-agentlessd...
ossec-authd already running...
wazuh-db already running...
ossec-execd already running...
ossec-maild already running...
ossec-analysisd already running...
ossec-syscheckd already running...
ossec-remoted already running...
ossec-logcollector already running...
ossec-monitord already running...
wazuh-modulesd already running...
Completed.
----------------------------------
```
##### D. Allow Wazuh Manager Service Ports Through The Firewall

```bash
[root@wazuh-elk ~]# firewall-cmd --permanent --add-port={514,1514,1515,1516}/tcp
[root@wazuh-elk ~]# firewall-cmd --permanent --add-port={514,1514}/udp
[root@wazuh-elk ~]# firewall-cmd --reload
```

514 - send collected events from syslogs

1514 - Send collected event from agents

1515 - Agents registration service

1516 - Wazuh  cluster communications

##### E. Enable Service & Restart 

```bash
[root@server1 wazuh-3.11.1]# systemctl daemon-reload
[root@server1 wazuh-3.11.1]# systemctl enable wazuh-manager.service
[root@server1 wazuh-3.11.1]# systemctl restart wazuh-manager.service
[root@server1 wazuh-3.11.1]# systemctl status -l  wazuh-manager.service
```