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
3. Kusto Query Language (KQL - Used to build world map)
4. Network Security Groups
5. Remote Desktop Protocol (RDP)
6. 3rd Party API: ipgeolocation.io
7. Custom Powershell Script (https://github.com/joshmadakor1/Sentinel-Lab/blob/main/Custom_Security_Log_Exporter.ps1)

<h2>Operating Systems Used </h2>

- Windows 10</b> (21H2)


<h2>Step 1: Create a Honeypot Virtual Machine</h2>
<p>
<img src="https://i.imgur.com/0NnL9CD.png"/>
</p>
<p>
<h2>Basic</h2>
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

<p>
<img src="https://i.imgur.com/6sQJmXk.png"/>
</p>
<p>
Disks
Leave all defaults
Select Next : Networking >
Networking
Network interface
NIC network security group: Advanced > Create new
A network security group contains security rules that allow or deny inbound network traffic to, or outbound network traffic from, the virtual machine. In other words, security rules management.

Remove Inbound rules (1000: default-allow-rdp) by clicking three dots
Add an inbound rule
Destination port ranges: * (wildcard for anything)
Protocol: Any
Action: Allow
Priority: 100 (low)
Name: Anything (ALLOW_ALL_INBOUND)
Select Review + create
</p>
<br />

<p>
<img src="https://i.imgur.com/F2LC8zv.png"/>
</p>
<p>
Step 3: Create a Log Analytics Workspace
Search for "Log analytics workspaces"
Select Create Log Analytics workspace
Put it in the same resource group as VM (honeypotlab)
Give it a desired name (honeypot-log)
Add to same region (East US 2)
Select Review + create
</p>
<br />

<p>
<img src="https://i.imgur.com/eO1qdCw.png"/>
</p>
<p>
Step 4: Configure Microsoft Defender for Cloud
Search for "Microsoft Defender for Cloud"
Scroll down to "Environment settings" > subscription name > log analytics workspace name (log-honeypot)

</p>
<br />

<p>
<img src="https://i.imgur.com/WAhlGrk.png"/>
</p>
<p>
Settings | Defender plans
Cloud Security Posture Management: ON
Servers: ON
SQL servers on machines: OFF
Hit Save
</p>
<br />

<p>
<img src="https://i.imgur.com/tXfBJfv.png"/>
</p>
<p>
Settings | Data collection
Select "All Events"
Hit Save
Step 5: Connect Log Analytics Workspace to Virtual Machine
Search for "Log Analytics workspaces"
Select workspace name (log-honeypot) > "Virtual machines" > virtual machine name (honeypot-vm)
Click Connect
</p>
<br />

<p>
<img src=""/>
</p>
<p>
...
</p>
<br />

<p>
<img src="https://i.imgur.com/ojSxO1h.png"/>
</p>
<p>
Step 6: Configure Microsoft Sentinel
Search for "Microsoft Sentinel"
Click Create Microsoft Sentinel
Select Log Analytics workspace name (honeypot-log)
Click Add
</p>
<br />

<p>
<img src="https://i.imgur.com/R8pLTtk.png"/>
</p>
<p>
Step 7: Disable the Firewall in Virtual Machine
Go to Virtual Machines and find the honeypot VM (honeypot-vm)
By clicking on the VM copy the IP address
Log into the VM via Remote Desktop Protocol (RDP) with credentials from step 2
Accept Certificate warning
Select NO for all Choose privacy settings for your device
Click Start and search for "wf.msc" (Windows Defender Firewall)
Click "Windows Defender Firewall Properties"
Turn Firewall State OFF for Domain Profile Private Profile and Public Profile
Hit Apply and Ok
Ping VM via Host's command line to make sure it is reachable ping -t <VM IP>
</p>
<br />

<p>
<img src="https://i.imgur.com/zg2Vwi6.png"/>
</p>
<p>
Step 8: Scripting the Security Log Exporter
In VM open Powershell ISE
Set up Edge without signing in
Copy Powershell script into VM's Powershell (Written by Josh Madakor)
Select New Script in Powershell ISE and paste script
Save to Desktop and give it a name (Log_Exporter)
</p>
<br />

<p>
<img src="https://i.imgur.com/GZmK6zk.png"/>
</p>
<p>
Make an account with Free IP Geolocation API and Accurate IP Lookup Database
This account is free for 1000 API calls per day. Paying 15.00$ will allow 150,000 API calls per month.

Copy API key once logged in and paste into script line 2: $API_KEY = "<API key>"
Hit Save
Run the PowerShell ISE script (Green play button) in the virtual machine to continuously produce log data
</p>
<br />

<p>
<img src="https://i.imgur.com/n3K9t4S.png"/>
</p>
<p>
Step 9: Create Custom Log in Log Analytics Workspace
Create a custom log to import the additional data from the IP Geolocation service into Azure Sentinel
Search "Run" in VM and type "C:\ProgramData"
Open file named "failed_rdp" hit CTRL + A to select all and CTRL + C to copy selection
Open notepad on Host PC and paste contents
Save to desktop as "failed_rdp.log"
In Azure go to Log Analytics Workspaces > Log Analytics workspace name (honeypot-log) > Custom logs > Add custom log
Sample
Select Sample log saved to Desktop (failed_rdp.log) and hit Next
Record delimiter
Review sample logs in Record delimiter and hit Next
Collection paths
Type > Windows
Path > "C:\ProgramData\failed_rdp.log"
Details
Give the custom log a name and provide description (FAILED_RDP_WITH_GEO) and hit Next
Hit Create
</p>
<br />

<p>
<img src="https://i.imgur.com/jYhpxro.png"/>
</p>
<p>
Step 10: Query the Custom Log
In Log Analytics Workspaces go to the created workspace (honeypot-log) > Logs
Run a query to see the available data (FAILED_RDP_WITH_GEO_CL)
May take some time for Azure to sync VM and Log Analytics
<br />

<p>
<img src="https://i.imgur.com/U4tTr5v.png"/>
</p>
<p>
Custom query
</p>
<br />

<p>
<img src="https://i.imgur.com/bIRuJBQ.jpg"/>
</p>
<p>
Step 12: Map Data in Microsoft Sentinel
Go to Microsoft Sentinel to see the Overview page and available events
Click on Workbooks and Add workbook then click Edit
Remove default widgets (Three dots > Remove)
Click Add > Add query
Copy/Paste the following query into the query window and Run Query
FAILED_RDP_WITH_GEO_CL | summarize event_count=count() by sourcehost_CF, latitude_CF, longitude_CF, country_CF, label_CF, destinationhost_CF
| where destinationhost_CF != "samplehost"
| where sourcehost_CF != ""
Kusto Query Language (KQL) - Azure Monitor Logs is based on Azure Data Explorer. The language is designed to be easy to read and use with some practice writing queries and basic guidance.

Once results come up click the Visualization dropdown menu and select Map
Select Map Settings for additional configuration
Layout Settings
Location info using > Latitude/Longitude
Latitude > latitude_CF
Longitude > longitude_CF
Size by > event_count
Color Settings
Coloring Type: Heatmap
Color by > event_count
Aggregation for color > Sum of values
Color palette > Green to Red
Metric Settings
Metric Label > label_CF
Metric Value > event_count
Select Apply button and Save and Close
Save as "Failed RDP World Map" in the same region and under the resource group (honeypotlab)
Continue to refresh map to display additional incoming failed RDP attacks
NOTE: The map will only display Event Viewer's failed RDP attempts and not all the other attacks the VM may be receiving.
</p>
<br />



