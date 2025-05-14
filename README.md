<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (22H2)
- Ubuntu Server 24.04.2

<h2>High-Level Steps</h2>

- Creating Azure Resources and Logging in RDP
- Installing Wireshark
- Filtering Wireshark for ICMP
- Filtering Wireshark for SSH, DCHP, DNS & RDP

<h2>Actions and Observations</h2>

<p>
<img src="https://i.imgur.com/GOd4VAc.gif" height="80%" width="80%" alt="Creating a Resource Group"/>
</p>
<p>
In the Azure portal, navigate to the Resource Groups page, create a Resource group and give a name. 
</p>
<br />

<p>
<img src="https://i.imgur.com/oaeDYlh.gif" height="80%" width="80%" alt="Creating a VM"/>
</p>
<p>
Now navigate to the Virtual Machines page, create a Windows 10 machine (make sure it's selected within the image). Fill in the required information and then navigate to the Networking tab and create a new virtual network. Finally, go to the review page and deploy the virtual machine.
</p>
<br />

<p>
<img src="https://i.imgur.com/NXxFJse.gif" height="80%" width="80%" alt="Creating a Ubuntu VM"/>
</p>
<p>
Do the same for the Ubuntu VM (make sure in the image section you select the Ubuntu image instead of Windows). Navigate to the networking tab and select the virtual network that you created before. (you may have to restart the page if it doesn't appear) Finally, go to the review page and deploy the virtual machine.
</p>
<br />

<p>
<img src="https://i.imgur.com/AhXooV4.gif" height="80%" width="80%" alt="Installing Wireshark, starting a packet share and filtering for ICMP"/>
</p>
<p>
Log into Windows VM using the public IP address from the virtual machine on Remote Desktop. Once logged in, launch Edge and search up for wireshark downloads and install Wireshark. Open Wireshark when you're done.
</p>
<br />


<p>
<img src="https://i.imgur.com/QmPdhi8.gif" height="80%" width="80%" alt="Starting a nonstop ping"/>
<img src="https://i.imgur.com/jf1ocv3.png" height="80%" width="80%" alt="Inspecting ping"/>
</p>
<p>
Within Wireshark, start a packet capture (double-clicking Ethernet). Filter for ICMP by going to the filter textbox and typing icmp. Open up powershell and start a nonstop ping (ping -t ip) the ubuntu vm using it's private IP address. The private IP address can be found when expanding details of the ubuntu vm. Notice in Wireshark there are echo requests and echo replies indicating connectivity between both of the vms. 
</p>
<br />

<p>
<img src="https://i.imgur.com/cjs7nYb.gif" height="80%" width="80%" alt="Creating a firewall rule for Ubuntu"/>
</p>
<p>
Go back to the Azure portal and click on the Ubuntu vm for more details. Go to Network -> Network Settings -> Network security group -> Inbound security rules -> and create a rule that denies all ICMPv4 traffic and save.
</p>
<br />


<p>
<img src="https://i.imgur.com/YhoYipx.gif" height="80%" width="80%" alt="Noticing changes after firewall rule"/>
</p>
<p>
Back to the Windows vm, notice that it says request timed out in Powershell and only echo requests in Wireshark. This means that pinging the ubuntu vm doesn't give a response back which means its not reachable due to the firewall. This is common occurence where and issues like the system is offline or network configuration issues can also prompt this to happen.
</p>
<br />


<p>
<img src="https://i.imgur.com/WIfVpqU.gif" height="80%" width="80%" alt="Deleting rule and noticing changes"/>
</p>
<p>
Back to the Azure portal, disable the rule. Go back to the Windows machine and notice that it works. You can do Ctrl+C to stop the ping requests.
</p>
<br />


<p>
<img src="https://i.imgur.com/PTruRwE.gif" height="80%" width="80%" alt="Doing ssh into ubuntu vm"/>
<img src="https://i.imgur.com/rPg0I1G.png" height="80%" width="80%" alt="Inspecting SSH"/>
</p>
<p>
Now ssh into the Ubuntu vm via ssh account_user@private_ip. Accept the fingerprint. In wireshark, filter by ssh. Type any linux commands like whoami, hostname, ls, or pwd. Notice the encrypted packets being sent everytime a character is typed. This is because, SSH encrypts all traffic inside these packets. Type exit when you're done.
</p>
<br />

<p>
<img src="https://i.imgur.com/sgTxbSW.gif" height="80%" width="80%" alt="Inspecting DHCP"/>
</p>
<p>
In wireshark, filter for DHCP traffic. Type in ipconfig /renew in Powershell and enter. If it requires admin privileges, close and reopen powershell in admin mode. Notice the traffic displayed. DHCP works by broadcasting from the computer for a DHCP server, the DHCP server then offers with an ip address, the computer then sends a request for that offer and then DHCP acknowledges the computer within the network. 
</p>
<br />


![14](https://github.com/user-attachments/assets/58c32455-62fe-4c7d-8512-4b65c5a66efe)
<p>
In wireshark, filter for DNS traffic. In Powershell, use nslookup with any domain like google.com. Notice it returns with ip addresses pointing that domain. This is what DNS is, DNS stands for domain name system and it translates human-readable domain names into machine read-able IP addresses. 
</p>
<br />


<p>
<img src="https://i.imgur.com/LqBUcSN.gif" height="80%" width="80%" alt="Inspecting RDP traffic"/>
</p>
<p>
Finally in wireshark, filter for RDP traffic. Notice that the traffic is nonstop. This is because we are actively remoted in a computer over the internet and we're actively sending data back and forward in these encrypted connections.
</p>
<br />

