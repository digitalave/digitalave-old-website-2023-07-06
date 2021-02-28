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

