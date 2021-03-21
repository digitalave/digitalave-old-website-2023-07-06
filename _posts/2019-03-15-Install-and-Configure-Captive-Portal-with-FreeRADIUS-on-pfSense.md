---
layout: post
authors: [dimuthu_daundasekara]
title: 'Install and Configure Captive Portal with FreeRADIUS on pfSense'
image: /assets/img/post-imgs/pfsense_captive_portal/captive_portal_N.jpg
tags: [pfSense, Firewall, Captive Portal, WIFI]
category: pfSense
comments: true
last_modified_at: 2020-01-31
categories: 
    - "pfsense"
    - "Captive Portal"
---

<style>
.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }
</style>
<div class='embed-container'>
    <iframe src='https://www.youtube.com/embed/hqjE4KySvWU?&autoplay=1' frameborder='0' allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen>
    </iframe>
</div>

### How to install & Configure Captive Portal with FreeRadius with Pfsense Firewall

# Table of contents
1. [What is Captive Portal?](#What-is-Captive-Portal?)
2. [STEP 1:- Install FreeRADIUS3 Package](#STEP-1:-Install-FreeRADIUS3-Package)
3. [STEP 2:- Create Server certificate](#STEP-2:-Create-Server-certificate)
4. [STEP 3:- Configure FreeRadius Server](#STE-3:--Configure-FreeRadius-Server)
5. [STEP 4:- Configure Captive Portal](#STEP-4:--Configure-Captive-Portal)


#### What is Captive Portal?

Captive portal is use for authenticated users to grant internet access. Firewall automatically captive portal authentication login page which users must use their credentials to enter the portal. User can use Username/Password or a voucher code.

This setup is commonly used throughout the hospitality industries like Airports, Hotels, Restaurants and corporate environments.

The Captive Portal function in Pfsense securing a network by requiring a username and password via portal access web page.

Pfsense built-in user management, LADP, RADIUS can be used as an authentication server.

In this tutorial I’m using FreeRADIUS2 as an authentication server.

#### STEP 1:- Install FreeRADIUS3 Package

Navigate to  **System > Package Manager, Available Packages**  tab


<img src="/assets/img/post-imgs/pfsense_captive_portal/image002.png" width="auto" alt="Digital Avenue DevOps Tutorials">
[![](https://digitalave.github.iopfsense_captive_portal/image002.png)](https://digitalave.github.iopfsense_captive_portal/image002.png)

Click  <img src="/assets/img/post-imgs/pfsense_captive_portal/image002.png" width="auto" alt="Digital Avenue DevOps Tutorials"> at the end of the row for  **FreeRADIUS3**

Confirm the installation

**System > Package Manager > Available Packages**

[Search Item] = freeradius3  
<img src="/assets/img/post-imgs/pfsense_captive_portal/image003.png" width="auto" alt="Digital Avenue DevOps Tutorials">

<img src="/assets/img/post-imgs/pfsense_captive_portal/image004.png" width="auto" alt="Digital Avenue DevOps Tutorials">

#### STEP 2:- Create Server certificate

Navigate to  **System > Cert. Manager**

#### Create a Certificate Authority (CA)

Create a Server Certificate

[How to create CA and Server certificate is available at…](https://digitalave.github.io/2018/08/08/Setup-Remote-VPN-Access-Using-PfSense-and-OpenVPN/)

#### STEP 3:- Configure FreeRadius Server

Navigate to  **System > FreeRadius, EAP**  Tab > “Certificates for TLS” section  
Provide CA and server certificate that we have generated at previous step.

<img src="/assets/img/post-imgs/pfsense_captive_portal/image005.png" width="auto" alt="Digital Avenue DevOps Tutorials">

Save the changes.

Add a new interface on which the RADIUS server should listen on.

**Navigate to System > Services > FreeRADIUS, Interfaces**  tab  
Click <img src="/assets/img/post-imgs/pfsense_captive_portal/image001.png" width="auto" alt="Digital Avenue DevOps Tutorials">button  
In this case I’m using my LAN interface (192.168.100.1) for RADIUS server to listening on.

<img src="/assets/img/post-imgs/pfsense_captive_portal/image006.png" width="auto" alt="Digital Avenue DevOps Tutorials">

<img src="/assets/img/post-imgs/pfsense_captive_portal/image007.png" width="auto" alt="Digital Avenue DevOps Tutorials">

<img src="/assets/img/post-imgs/pfsense_captive_portal/image008.png" width="auto" alt="Digital Avenue DevOps Tutorials">
Save and exit.

<img src="/assets/img/post-imgs/pfsense_captive_portal/image009.png" width="auto" alt="Digital Avenue DevOps Tutorials">

#### Configure the NAS:

Configure the NAS/client(s) from which the RADIUS server should accept packets.  
In this step you need to give the IP address of the device which you want to authenticate from radius server like a firewall, access point, switch and router.

In this step I give my Pfsense box’s IP address because I will use the Pfsense captive portal.

#### Click “+” button to add the NAS/Clients.

> Client IP Address : 192.168.100.1  
> Client Shortname: captiveportal  
> Client Shared Secret: 12345

Reset of the settings can be leave default.

<img src="/assets/img/post-imgs/pfsense_captive_portal/image010.png" width="auto" alt="Digital Avenue DevOps Tutorials">

<img src="/assets/img/post-imgs/pfsense_captive_portal/image011.png" width="auto" alt="Digital Avenue DevOps Tutorials">

#### STEP 4:- Configure Captive Portal

Navigate to  **Services > Captive Portal**

Click “<img src="/assets/img/post-imgs/pfsense_captive_portal/image001.png" width="auto" alt="Digital Avenue DevOps Tutorials">“ button to add new zone.

<img src="/assets/img/post-imgs/pfsense_captive_portal/image012.png" width="auto" alt="Digital Avenue DevOps Tutorials">

<img src="/assets/img/post-imgs/pfsense_captive_portal/image013.png" width="auto" alt="Digital Avenue DevOps Tutorials">

##### NOTE:

<img src="/assets/img/post-imgs/pfsense_captive_portal/image014.png" width="auto" alt="Digital Avenue DevOps Tutorials">

<img src="/assets/img/post-imgs/pfsense_captive_portal/image015.png" width="auto" alt="Digital Avenue DevOps Tutorials">

<img src="/assets/img/post-imgs/pfsense_captive_portal/image016.png" width="auto" alt="Digital Avenue DevOps Tutorials">

<img src="/assets/img/post-imgs/pfsense_captive_portal/image017.png" width="auto" alt="Digital Avenue DevOps Tutorials">

<img src="/assets/img/post-imgs/pfsense_captive_portal/image018.png" width="auto" alt="Digital Avenue DevOps Tutorials"> 

<img src="/assets/img/post-imgs/pfsense_captive_portal/image019.png" width="auto" alt="Digital Avenue DevOps Tutorials"> 

<img src="/assets/img/post-imgs/pfsense_captive_portal/image020.png" width="auto" alt="Digital Avenue DevOps Tutorials">

#### STEP 04:- Create FreeRadius Users

Navigate to  **Services > FreeRadius, Users**  tab.  
<img src="/assets/img/post-imgs/pfsense_captive_portal/image021.png" width="auto" alt="Digital Avenue DevOps Tutorials">

All the other settings can be change upon to your requirements.

#### STEP 05:- Login to Captive Portal User Account  
<img src="/assets/img/post-imgs/pfsense_captive_portal/image022.png" width="auto" alt="Digital Avenue DevOps Tutorials">


**Little Request:**

> I appreciate you guys taking the time in reading my post. Please check out my YouTube channel and please subscribe for more as it'll help me loads.


<a href="https://www.youtube.com/channel/UCovlVsoRVItner26ZJPBjmQ/featured?view_as=subscriber" target="_blank">https://www.youtube.com/channel/UCovlVsoRVItner26ZJPBjmQ/featured?view_as=subscriber</a>


[<img src="Docker-Installation/sub.gif">](https://www.youtube.com/channel/UCovlVsoRVItner26ZJPBjmQ?sub_confirmation=1) 

