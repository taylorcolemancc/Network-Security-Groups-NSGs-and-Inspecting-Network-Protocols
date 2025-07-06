<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we will observe various network traffic to and from Azure Virtual Machines using Wireshark, while experimenting with Network Security Groups.

<h2>Environments and Technologies Used</h2>
<ul>
  <li>Microsoft Azure (Virtual Machines/Compute)</li>
  <li>Remote Desktop</li>
  <li>Various Command-Line Tools</li>
  <li>Various Network Protocols (SSH, RDP, DNS, HTTP/S, ICMP)</li>
  <li>Wireshark (Protocol Analyzer)</li>
</ul>

<h2>Operating Systems Used</h2>
<ul>
  <li>Windows 10 (21H2)</li>
  <li>Ubuntu Server 20.04</li>
</ul>

<h2>High-Level Steps</h2>
<ol>
  <li>Create Resource Groups</li>
  <li>Install Wireshark</li>
  <li>Observe Network Protocols</li>
</ol>

<h2>Actions and Observations</h2>

<p> 
 1. Create Resource Group:
To create the Resource Group, you have two options:
1. Perform a quick search for "Resource Group" at the top of the Azure portal, or
2. Select "Create a Resource" and choose to create the Resource Group from the Azure Marketplace.  
</p>
<p>
Select the subscription, `Azure subscription 1`, and enter your custom-created Resource Group. Select the preferred region nearest to you to save on costs.
</p>

<p>
 2. Create Virtual Machines:
Note: The two virtual machines will allow us to send traffic between them. You can name them whatever you prefer, as long as you can remember their names. 

To create the first virtual machine, which will be running the "Windows operating system" and named `VM1`, search for "Virtual Machine" at the top of the Azure portal, and select "Virtual machines" from the search results. Choose `+ Create`, then select "Create a virtual machine hosted by Azure".
</p>

<p>
Select the "subscription", the same " resource group " (RG-LAB-2), name the virtual machine as `VM1`, select the same region, and set the image to `Windows 10 Pro Version`. 
</p>

<p>
Note: Ensure to check the licensing checkbox to avoid encountering errors during validation. Allow port 3389 for remote desktop access.
</p>

<p>
Allow the networking and other settings to remain as default, then select `Review`. Review the details and click `Create` once satisfied.
</p>

<p>
Now, create Virtual Machine 2 (`VM2`) with Linux Ubuntu Server. Use a password instead of an SSH public key for authentication. After configuring the remaining settings, click `Review`, then `Create`.
</p>

<p>
These two Virtual Machines will be used for Remote Desktop and observing different network traffic between them.
</p>

<p>
 3. Install Wireshark:
To install Wireshark, search for "remote desktop connection" and use the public IP address details of `VM1` to connect. Once connected, download and install Wireshark on `VM1`.
</p>

<p>
Note: Npcap is a required driver for capturing packets on Windows. Install Npcap with the default settings. After installation, search for Wireshark and open it.
</p>

<p>
Once Wireshark opens, click `Ethernet` and then click the blue fin icon to begin capturing packets. You will now see the network traffic in the Windows Virtual Machine.
</p>

<p>
 4. Observe Network Protocols:
Retrieve the private IP address of `VM2` and **ping** it from `VM1` to test the connection. Filter for **ICMP** packets in Wireshark to observe the traffic from `VM1` to `VM2`. You can also **ping** other IP addresses or domains, such as "_www.google.com". This can be seen in the filtered traffic in Wireshark and the PowerShell output below.
</p>

<p>
Next, add a rule to the Network Security Group in `VM2` to deny the ping request. This will cause the PowerShell output to show a timeout, and Wireshark will no longer display a reply for the ping request.
</p>

<p>
To configure this rule in the Azure portal, search for Network Security Group and click on `VM2` (Linux Ubuntu Server). Add a new inbound security rule to deny ICMP traffic by selecting **ICMP** under the protocol options and setting the action to **Deny**. Ensure the priority is set before 300 to ensure it takes effect.
</p>

<p>
After creating the rule, PowerShell will display **" Request timed out"**, and Wireshark will only show the *ICMP* requests.
</p>

<p>
To re-enable the rule, either delete it from the network security group or select it and choose to allow the rule again.
</p>

<p>
In Wireshark, apply the filter "SSH" or "tcp.port == 22" to capture SSH traffic. Use PowerShell to log in to the Linux Ubuntu Server with the command "ssh username@ip_address", followed by the _private IP address_ of `VM2`.
</p>

<p>
After logging in, execute Linux commands like touch, pwd, or ls in the SSH session. You will see the SSH traffic captured in Wireshark. To exit the SSH session, type "exit" and press `Enter`.
</p>

<p>
To capture DHCP traffic, filter for it in Wireshark. Execute the command "ipconfig /renew" in `VM1` to obtain a new IP address. Wireshark will capture the DHCP traffic.
</p>

<p>
Apply a filter in Wireshark for DNS traffic and execute the command "nslookup www.disney.com" in PowerShell to retrieve the IP addresses associated with the domain.
</p>

<p>
To capture RDP traffic, filter for tcp.port == 3389 in Wireshark to capture live stream data between the computers during Remote Desktop sessions.
</p>

<p>
 5. Deleting Resource Group:
To delete the Resource Group, search for "Resource Group" and select the one containing the two virtual machines. After selecting the Resource Group, click "Delete" at the top, then confirm by clicking the final delete button at the bottom.
</p>

<h2>Terms and Descriptions</h2>
<table>
  <tr>
    <th>Terms</th>
    <th>Descriptions</th>
  </tr>
  <tr>
    <td>Remote Desktop</td>
    <td>Remote desktop enables users to establish a connection with a computer located elsewhere, providing a view of its desktop and allowing interaction as if it were a local machine.</td>
  </tr>
  <tr>
    <td>DHCP</td>
    <td>Dynamic Host Configuration Protocol is a network management protocol that automates the process of assigning IP addresses and other communication parameters to devices connected to an IP network. This uses the port UDP 67 and 68.</td>
  </tr>
  <tr>
    <td>DNS</td>
    <td>Domain Name System converts easily recognizable domain names (such as www.disney.com) into numerical IP addresses that computers can understand. This uses port TCP and UDP 53.</td>
  </tr>
  <tr>
    <td>SSH</td>
    <td>Secure Shell facilitates secure remote connections between computers, enabling command line access from one system to another. This uses the port TCP 22.</td>
  </tr>
  <tr>
    <td>RDP</td>
    <td>Remote Desktop Protocol is used when remotely connecting from one computer to another to gain a remote desktop GUI (Graphical User Interface) This uses the port TCP 3389.</td>
  </tr>
  <tr>
    <td>ICMP</td>
    <td>Internet Control Message Protocol is used for diagnostics and error reporting in IP networks. It helps verify connectivity, troubleshoot issues, and report errors, commonly using the ping utility. This one does not use a port.</td>
  </tr>
</table>
