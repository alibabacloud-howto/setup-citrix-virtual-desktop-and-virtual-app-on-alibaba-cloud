# Setup Citrix Virtual Desktop and Virtual App on Alibaba Cloud

## Summary
0. [Introduction](#introduction)
1. [Prerequisite](#prerequisite)
2. [System Architecture](#system-architecture)
3. [Cloud Resources](#cloud-resources)
4. [Step by Step Approach](#step-by-step-approach)
5. [Conclusion](#conclusion)
6. [Support](#support)

## Introduction
Citrix Virtual Apps and Desktops are virtualization solutions which deliver virtual apps and virtual Windows or Linux desktops. This document is a step-by-step guide for deploying a demo environment of Citrix Virtual Desktop and Virtual App on Alibaba Cloud.

## Prerequisite
* Citrix Account
    You need an Citrix account. If you don't have any account, please follow
    [this page](https://www.citrix.com/welcome/create-account/) to create one.
* Alibaba Cloud Account
    You need an Alibaba Cloud account. If you don't have any account, please follow
    [this document](https://www.alibabacloud.com/help/doc-detail/50482.htm) to create one.
    
## System Architecture
The following figure depicts the architecture we will set up in this tutorial. It provides a conceptual view of architecture of Citrix Virtual Apps and Desktops Service components, including Delivery Controller, StoreFront Server, SQL Server DB, VDA and Citrix Gateway.
<img src="images/image001.png" width = 80% height = 80% />

## Cloud Resources
The following table lists Alibaba Cloud ECS servers which are used in this tutorial for setting up Citrix components.

| Server | Instance Type | Private IP | Subnet IP | VIP | Public IP|
| :------| :------: | :------: | :------: |:------: |:------: |
| ADDC |ecs.sn2ne.xlarge (4 vCPU 16 GiB) | 172.21.16.22 |  |  |  |			
| Citrix StoreFront |ecs.sn2ne.xlarge (4 vCPU 16 GiB) | 172.21.16.23 |  |  |  |			
| VPA |ecs.sn2ne.xlarge (4 vCPU 16 GiB) | 172.21.16.25 |  |  |  |			
| Citrix Desktop DC |ecs.sn2ne.xlarge (4 vCPU 16 GiB) | 172.21.16.24 |  |  |  |		
| NetScaler |ecs.sn2ne.xlarge (4 vCPU 16 GiB) | 172.21.16.26 | 172.21.16.27| 172.21.16.28| 161.117.11.147|

The NetScaler IP (NSIP) address is the IP address at which you access the NetScaler appliance for management purposes, here the NSIP is 161.117.11.147.

A subnet IP address (SNIP) is a NetScaler owned IP address that is used by the NetScaler appliance to communicate with the servers, here the SNPI is 172.21.16.27.

A VIP address is usually associated with a virtual server, here the VIP is 172.21.16.28.
## Step by Step Approach
### Download Citrix Installation Media
The demo environment in this document is created using the trial version.
#### Download Citrix Virtual Apps and Desktops
You can download Citrix Virtual Apps and Desktops from [this page](https://www.citrix.com/downloads/citrix-virtual-apps-and-desktops/).

<img src="images/image002.png" width = 50% height = 50% />
 
#### Download Citrix ADC (NetScaler ADC)
You can download Citrix ADC from [this page](https://www.citrix.com/downloads/citrix-adc/) and put the file in Alibaba Cloud OSS bucket.

<img src="images/image003.png" width = 50% height = 50% />
 
### Setup Active Directory in Windows Server 2016
Open the server manager，click on “Add roles and features”.

Click on “Next”.

<img src="images/image005.png" width = 80% height = 80% />

<img src="images/image006.png" width = 80% height = 80% />

<img src="images/image007.png" width = 80% height = 80% />

<img src="images/image008.png" width = 80% height = 80% />

<img src="images/image009.png" width = 80% height = 80% />

<img src="images/image010.png" width = 80% height = 80% />

<img src="images/image011.png" width = 80% height = 80% />

<img src="images/image012.png" width = 80% height = 80% />

<img src="images/image013.png" width = 80% height = 80% />

<img src="images/image014.png" width = 80% height = 80% />

<img src="images/image015.png" width = 80% height = 80% />

<img src="images/image016.png" width = 80% height = 80% />

<img src="images/image017.png" width = 80% height = 80% />

<img src="images/image018.png" width = 80% height = 80% />

<img src="images/image019.png" width = 80% height = 80% />

<img src="images/image020.png" width = 80% height = 80% />

<img src="images/image021.png" width = 80% height = 80% />

<img src="images/image022.png" width = 80% height = 80% />

<img src="images/image023.png" width = 80% height = 80% />

After installation completed the system will restart automatically.

Open windows powershell, use below commands to confirm domain and forest functional levels.
```
Get-ADDomain | fl Name,DomainMode
Get-ADForest | fl Name,ForestMode
```
<img src="images/image024.png" width = 80% height = 80% />

Create new AD user “citrixadmin” and add to the Domain Admin group.
<img src="images/image025.png" width = 80% height = 80% />

<img src="images/image026.png" width = 50% height = 50% />

<img src="images/image027.png" width = 50% height = 50% />
 
### Join the DDC, StoreFront and VDA servers to AD Domain
#### Change Windows SID 
As the ECS instances are created with the same Windows image they will have the same windows SID. Before joining to AD domain, needs to manually change their windows SIDs.
(Refer to [Alibaba Cloud Document](https://www.alibabacloud.com/help/faq-detail/40846.htm))

Open windows PowerShell and run below command:

`wget http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/40846/cn_zh/1542198598487/AutoSysprep.ps1 -outfile AutoSysprep.ps1
.\AutoSysprep.ps1 -SkipRearm -Password “password” -PostAction "reboot"`

<img src="images/image028.png" width = 80% height = 80% />
 
#### Join Server to Windows Domain 
Go to Control Panel -> Network and Internet -> Network and Sharing Center.

Open Internet Protocol Version 4 (TCP/IPv4) Properties of Ethernet Connection.
<img src="images/image029.png" width = 80% height = 80% />

Update the DNS server address to the Windows AD server IP address.

<img src="images/image030.png" width = 40% height = 40% />

Open Server Manager -> Local Server, click Computer Name.

<img src="images/image031.png" width = 50% height = 50% />

In the opened System Properties window, click “Change” button.

<img src="images/image032.png" width = 50% height = 50% />

Enter the new computer name and domain name, click “OK”.

<img src="images/image033.png" width = 40% height = 40% />

<img src="images/image034.png" width = 30% height = 30% />

<img src="images/image035.png" width = 30% height = 30% />

<img src="images/image036.png" width = 30% height = 30% />

Restart the server.
Follow the same steps for StoreFront and VDA servers to join domain.
### Install Virtual App and Desktop Controller

<img src="images/image037.png" width = 80% height = 80% />

Click “Delivery Controller”.

<img src="images/image038.png" width = 80% height = 80% />

Accept the license agreement and click “Next”.

<img src="images/image039.png" width = 80% height = 80% />

Select all the components except “StoreFront” and click “Next”.

<img src="images/image040.png" width = 80% height = 80% />

<img src="images/image041.png" width = 80% height = 80% />

<img src="images/image042.png" width = 80% height = 80% />

<img src="images/image043.png" width = 80% height = 80% />

<img src="images/image044.png" width = 80% height = 80% />

<img src="images/image045.png" width = 80% height = 80% />

<img src="images/image046.png" width = 80% height = 80% />

<img src="images/image047.png" width = 80% height = 80% />

### Configure Virtual App and Desktop Controller
Login to DDC server using citrixdemo.com\citrixadmin, launch Citrix Studio.
#### Setup Site

<img src="images/image048.png" width = 80% height = 80% />

<img src="images/image049.png" width = 80% height = 80% />

<img src="images/image050.png" width = 80% height = 80% />

<img src="images/image051.png" width = 80% height = 80% />

<img src="images/image052.png" width = 80% height = 80% />

<img src="images/image53.png" width = 80% height = 80% />

<img src="images/image054.png" width = 80% height = 80% />

<img src="images/image055.png" width = 80% height = 80% />

<img src="images/image056.png" width = 80% height = 80% />

#### Setup Machine Catalog
<img src="images/image057.png" width = 80% height = 80% />

<img src="images/image058.png" width = 80% height = 80% />

<img src="images/image059.png" width = 80% height = 80% />

<img src="images/image060.png" width = 80% height = 80% />

<img src="images/image061.png" width = 80% height = 80% />

<img src="images/image062.png" width = 80% height = 80% />

<img src="images/image063.png" width = 80% height = 80% />
 
#### Setup Delivery Group
<img src="images/image064.png" width = 80% height = 80% />

<img src="images/image065.png" width = 80% height = 80% />

<img src="images/image066.png" width = 80% height = 80% />

<img src="images/image067.png" width = 80% height = 80% />

<img src="images/image068.png" width = 80% height = 80% />

<img src="images/image069.png" width = 80% height = 80% />

<img src="images/image070.png" width = 80% height = 80% />

<img src="images/image071.png" width = 80% height = 80% />

<img src="images/image072.png" width = 80% height = 80% />

<img src="images/image073.png" width = 80% height = 80% />

<img src="images/image074.png" width = 80% height = 80% />

#### Add StoreFront Server
<img src="images/image075.png" width = 80% height = 80% />

<img src="images/image076.png" width = 30% height = 30% />

### Install StoreFront

<img src="images/image037.png" width = 80% height = 80% />

Click “Delivery Controller”.

<img src="images/image038.png" width = 80% height = 80% />

Accept the license agreement and click “Next”
 
<img src="images/image039.png" width = 80% height = 80% />

Select “StoreFront” and click “Next”.

<img src="images/image077.png" width = 80% height = 80% />

<img src="images/image078.png" width = 80% height = 80% />

<img src="images/image079.png" width = 80% height = 80% />

<img src="images/image080.png" width = 80% height = 80% />

<img src="images/image081.png" width = 80% height = 80% />
 
### Install VDA

<img src="images/image037.png" width = 80% height = 80% />

<img src="images/image082.png" width = 80% height = 80% />

<img src="images/image083.png" width = 80% height = 80% />

<img src="images/image084.png" width = 80% height = 80% />

<img src="images/image085.png" width = 80% height = 80% />

<img src="images/image086.png" width = 80% height = 80% />

<img src="images/image087.png" width = 80% height = 80% />

<img src="images/image088.png" width = 80% height = 80% />

<img src="images/image089.png" width = 80% height = 80% />

<img src="images/image090.png" width = 80% height = 80% />

<img src="images/image091.png" width = 80% height = 80% />

<img src="images/image092.png" width = 80% height = 80% />

<img src="images/image093.png" width = 80% height = 80% />

<img src="images/image094.png" width = 80% height = 80% />

### Configure StoreFront

<img src="images/image095.png" width = 80% height = 80% />

<img src="images/image096.png" width = 80% height = 80% />

<img src="images/image097.png" width = 80% height = 80% />

<img src="images/image098.png" width = 80% height = 80% />

<img src="images/image099.png" width = 80% height = 80% />

<img src="images/image100.png" width = 80% height = 80% />

<img src="images/image101.png" width = 80% height = 80% />

<img src="images/image102.png" width = 80% height = 80% />

<img src="images/image103.png" width = 80% height = 80% />

<img src="images/image104.png" width = 80% height = 80% />

<img src="images/image105.png" width = 80% height = 80% />

<img src="images/image106.png" width = 80% height = 80% />

<img src="images/image107.png" width = 80% height = 80% />

<img src="images/image108.png" width = 80% height = 80% />

<img src="images/image109.png" width = 80% height = 80% />

<img src="images/image110.png" width = 80% height = 80% />

### Install Windows CS

<img src="images/image111.png" width = 80% height = 80% />

<img src="images/image112.png" width = 80% height = 80% />

<img src="images/image113.png" width = 80% height = 80% />

<img src="images/image114.png" width = 80% height = 80% />

<img src="images/image115.png" width = 80% height = 80% />

<img src="images/image116.png" width = 80% height = 80% />

<img src="images/image117.png" width = 80% height = 80% />

<img src="images/image118.png" width = 80% height = 80% />

<img src="images/image119.png" width = 80% height = 80% />

<img src="images/image120.png" width = 80% height = 80% />

<img src="images/image121.png" width = 80% height = 80% />

<img src="images/image122.png" width = 80% height = 80% />

<img src="images/image123.png" width = 80% height = 80% />

<img src="images/image124.png" width = 80% height = 80% />

<img src="images/image125.png" width = 80% height = 80% />

<img src="images/image126.png" width = 80% height = 80% />

<img src="images/image127.png" width = 80% height = 80% />

<img src="images/image128.png" width = 80% height = 80% />

<img src="images/image129.png" width = 80% height = 80% />

<img src="images/image130.png" width = 80% height = 80% />

<img src="images/image131.png" width = 80% height = 80% />

<img src="images/image132.png" width = 80% height = 80% />

<img src="images/image133.png" width = 80% height = 80% />

### Connection to StoreFront
Now you can connect to StoreFront from this URL: 
https://citrixdemosf.citrixdemo.com/Citrix/citrixdemoWeb

(Please note that this address is only available from servers which joined citrixdemo.com.)

<img src="images/image134.png" width = 80% height = 80% />

<img src="images/image135.png" width = 80% height = 80% />

Click “Download” to download Citrix Receiver.

<img src="images/image136.png" width = 50% height = 50% />

<img src="images/image137.png" width = 50% height = 50% />

<img src="images/image138.png" width = 50% height = 50% />

<img src="images/image139.png" width = 50% height = 50% />

<img src="images/image140.png" width = 50% height = 50% />

<img src="images/image141.png" width = 80% height = 80% />

<img src="images/image142.png" width = 80% height = 80% />

<img src="images/image143.png" width = 80% height = 80% />

<img src="images/image144.png" width = 80% height = 80% />

<img src="images/image145.png" width = 80% height = 80% />

<img src="images/image146.png" width = 80% height = 80% />

### Install and Configure NetScaler ADC VPX 12
#### Import Image
<img src="images/image147.png" width = 80% height = 80% />

#### Create ECS Instance using VPX Custom Image
<img src="images/image148.png" width = 60% height = 60% />

#### Configure VPX Server
Create 2 ENIs and bind to the VPX Server.

Login to VPX Server run below commands:
```
set ns config -IPAddress 172.21.16.26 -netmask 255.255.240.0
add route 0 0 172.21.16.22
save ns config
```
<img src="images/image149.png" width = 80% height = 80% />

After reboot, login using the public address http://161.117.11.147 .
<img src="images/image150.png" width = 80% height = 80% />

<img src="images/image151.png" width = 80% height = 80% />

<img src="images/image152.png" width = 80% height = 80% />

<img src="images/image153.png" width = 80% height = 80% />

<img src="images/image154.png" width = 80% height = 80% />

Configuration -> System -> System Information -> Hardware Information
<img src="images/image156.png" width = 80% height = 80% />

<img src="images/image157.png" width = 80% height = 80% />

<img src="images/image158.png" width = 80% height = 80% />

Create SSL key and CSR files:

<img src="images/image159.png" width = 60% height = 60% />

<img src="images/image160.png" width = 60% height = 60% />

<img src="images/image161.png" width = 60% height = 60% />

<img src="images/image162.png" width = 60% height = 60% />

<img src="images/image167.png" width = 80% height = 80% />

<img src="images/image168.png" width = 80% height = 80% />

<img src="images/image169.png" width = 80% height = 80% />

<img src="images/image170.png" width = 80% height = 80% />

<img src="images/image171.png" width = 60% height = 60% />

<img src="images/image172.png" width = 60% height = 60% />

<img src="images/image173.png" width = 60% height = 60% />

<img src="images/image174.png" width = 60% height = 60% />

<img src="images/image183.png" width = 50% height = 50% />

<img src="images/image184.png" width = 50% height = 50% />

<img src="images/image185.png" width = 50% height = 50% />

<img src="images/image186.png" width = 30% height = 30% />

<img src="images/image175.png" width = 80% height = 80% />

<img src="images/image176.png" width = 80% height = 80% />

#### Configure StoreFront for NetScaler Gateway
Login to StoreFront server, launch StoreFront and click on "Manage NetScaler Gateways".
<img src="images/image189.png" width = 80% height = 80% />

Click on "Add".

<img src="images/image177.png" width = 60% height = 60% />

<img src="images/image178.png" width = 80% height = 80% />

<img src="images/image179.png" width = 80% height = 80% />

<img src="images/image180.png" width = 80% height = 80% />

<img src="images/image181.png" width = 80% height = 80% />

<img src="images/image182.png" width = 80% height = 80% />


After completing the configuration of NetScaler, we can now access to the Citrix Virtual Desktop and Virtual App from https://nsdemo.alibabacloudanz.com .
<img src="images/image187.png" width = 80% height = 80% />

## Conclusion
Now, we have finished the deployment for Citrix Virtual Desktop and Virtual App on Alibaba Cloud.
## Support
Don't hesitate to [contact us](mailto:projectdelivery@alibabacloud.com) if you have questions or remarks.