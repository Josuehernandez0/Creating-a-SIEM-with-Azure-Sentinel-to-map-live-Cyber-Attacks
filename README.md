# Creating a SIEM with Azure Sentinel to map live Cyber Attacks
<p align="center">
<img src="https://i.imgur.com/PdvUvwy.png"/>
</p>
<p align="center">
<img src="https://i.imgur.com/bIRuJBQ.jpg"/>
</p>
<h1>Learning Objectives:</h1>
 - Configuration & Deployment of Azure resources such as virtual machines, Log Analytics Workspaces, and Azure Sentinel
 - Hands-on experience and working knowledge of a SIEM Log Management Tool (Microsoft's Azure Sentinel)
 - Understand Windows Security Event logs
 - Utilization of KQL to query logs
 - Display attack data on a dashboard with Workbooks (World Map)<br />




<h2>Tools and Requirements:</h2>

1. Microsoft Azure Subscription
2. Azure Sentinel
3.Kusto Query Language (KQL - Used to build world map)
4.Network Security Groups
5.Remote Desktop Protocol (RDP)
6.3rd Party API: ipgeolocation.io
7.Custom Powershell Script (https://github.com/joshmadakor1/Sentinel-Lab/blob/main/Custom_Security_Log_Exporter.ps1)
<h2>Operating Systems Used </h2>

- Windows 10</b> (21H2)


<h2>Step 1: Create a Honeypot Virtual Machine</h2>
<p>
<img src="https://i.imgur.com/0NnL9CD.png"/>
</p>
<p>
Basics
Go to portal.azure.com
Search for "virtual machines"
Create > Azure virtual machine
Project details
Create new resource group and name it (honeypotlab)
A resource group is a collection of resources that share the same lifecycle, permissions, and policies.

Instance details
Name your VM (honeypot-vm)
Select a recommended region ((US) East US 2)
Availability options: No infrastructure redundancy required
Security type: Standard
Image: Windows 10 Pro, version 21H2 - x62 Gen2
VM Architecture: x64
Size: Default is okay (Standard_D2s_v3 – 2vcpus, 8 GiB memory)
Administrator account
Create a username and password for virtual machine
IMPORTANT NOTE: These credentials will be used to log into the virtual machine (Keep them handy)

Inbound port rules
Public inbound ports: Allow RDP (3389)
Licensing
Confirm licensing
Select Next : Disks >
</p>
<br />
