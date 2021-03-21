---
layout: post
authors: [dimuthu_daundasekara]
title: 'Configure Local DHCP Server and DNS Resolver on pfSense'
image: /assets/img/post-imgs/pfsense-dns-dhcp/pf-DNS&DHCP.png
tags: [pfsense, DNS, DHCP, Firewall]
category: pfSense
comments: true
last_modified_at: 2020-01-31
---

<style>
.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }
</style>
<div class='embed-container'>
    <iframe src='https://www.youtube.com/embed/J50FFKkkpD0?&autoplay=1' frameborder='0' allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen>
    </iframe>
</div>

Welcome to digital avenue. Hi Guys today I'm goning to demonstrate how to install anbd configure dhcp server and dns reslover on pfsense 2.4.

#### STEP 01: GENARAL CONFIGURATION

Systemc > Genaral Setup

Goto "System" tab and select "Genaral Setup" from the drop down menu.

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_01.png" width="auto" alt="Digital Avenue DevOps Tutorials">

Change following fields as seen below.

Hostname : Define a meaningfull hostname for pfSense.

Domain Name : Define your domain name which pfsense router used.

DNS Server : Define public authoritative DNS servers for user pfSense itself.

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_02.png" width="auto" alt="Digital Avenue DevOps Tutorials">

#### STEP 02: SETUP DHCP SERVER

Goto  Services tab and select DHCP Server from  the drop down menu.

Services > DHCP Server

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_2.png" width="auto" alt="Digital Avenue DevOps Tutorials">

Select the interface which you want to DHCP server runing on.

Fill the relevent fields with "Subnet", "Subnet Mask", "Range", "DNS Servers", "Gateway" and "Domain Name"

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_3.png" width="auto" alt="Digital Avenue DevOps Tutorials">

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_4.png" width="auto" alt="Digital Avenue DevOps Tutorials">

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_5.png" width="auto" alt="Digital Avenue DevOps Tutorials">

Change all other options according to your requirement.

#### STEP 02: SETUP DNS SERVER

Unbound is integrated into pfSense. Unbound is use as the DNS server. 

Go to  "Services" tab and select "DNS Resolver" from the drop down menu.

Services > DNS Resolver

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_6.png" width="auto" alt="Digital Avenue DevOps Tutorials">

Enable DNS Resolver: Enable/Disable DNS Resolver

Network Interfaces: Network interfaces which are listening from  DNS queries from  clients.

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_7.png" width="auto" alt="Digital Avenue DevOps Tutorials">

Outgoing Network Interfaces: Specific interfaces which are passing outbound DNS queries to higer level DNS server such as Google DNS. Most cases WAN interfaces used.

Enable DNSSEC Support: DNSSEC provides added security to DNS queries which validate DNS queries.

Enable Forwarding Mode: Unbound DNS queries forwarding to upstream DNS server which are defined under  System > General 

Register DHCP leases in the DNS Resolver: DHCP static mappings can be registered in Unbound which enables the resolving of hostnames that have been assigned addresses by the DHCP server in pfSense

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_8.png" width="auto" alt="Digital Avenue DevOps Tutorials">

Host Overrides: Allows creation of custom DNS responses/records to create new entries that do not exist in DNS outside the firewall, or to override DNS responses for other hosts.

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_9.png" width="auto" alt="Digital Avenue DevOps Tutorials">

Domain Override: For domains that should be queried by a specific remote server.
At this point I want to redirect all DNS quest for  .youtube.com and .facebook.com redirect to localhost/127.0.0.1 itself.

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_10.png" width="auto" alt="Digital Avenue DevOps Tutorials">

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_11.png" width="auto" alt="Digital Avenue DevOps Tutorials">

#### STEP 02: Verifing DNS Queries.

<img src="/assets/img/post-imgs/pfsense-dns-dhcp/Screenshot_12.png" width="auto" alt="Digital Avenue DevOps Tutorials">


[<img src="Docker-Installation/sub.gif">](https://www.youtube.com/channel/UCovlVsoRVItner26ZJPBjmQ?sub_confirmation=1) 

