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

<h2>Operating Systems Used</h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Inspect/filter traffic between VMs using Wireshark
- Create sample file shares with various permissions
- Attempt to access file shares as a normal user
- Create a new security group, assign permissions, and test access

<h2>Actions and Observations</h2>

<p>
<a href="https://ibb.co/gjGmWqG"><img src="https://i.ibb.co/ZL3Gg43/Screen-Shot-2023-10-24-at-11-49-43-AM.png" alt="Screen-Shot-2023-10-24-at-11-49-43-AM" border="0" /></a>
</p>

<p>
<a href="https://ibb.co/KFkQCHk"><img src="https://i.ibb.co/hDjnpvj/Screen-Shot-2023-10-24-at-12-37-11-PM.png" alt="Screen-Shot-2023-10-24-at-12-37-11-PM" border="0" /></a>
</p>

<p>
<a href="https://ibb.co/c2TH3Qv"><img src="https://i.ibb.co/L9pWR1P/Screen-Shot-2023-10-24-at-12-42-54-PM.png" alt="Screen-Shot-2023-10-24-at-12-42-54-PM" border="0" /></a>
</p>

<p>
Make sure both VMs are in the same Vnet when created.

First log in to your Client-1 VM and install Wireshark. Once installed, select the ethernet adapter and click the blue fin icon in the top left corner to start capturing packets. This is going to let us see the actual live traffic that is happening on our VM. To filter this traffic using the ICMP protocol, we need to type to apply it as a display filter. We will now only be able to observe ICMP traffic. This same step can be done to filter following traffic: SSH, DHCP, DNS, RDP.

Since both of our VMs are in the same Vnet, we can get the Private IP Address of our DC-1 VM and ping it using Powershell from our Client-1 VM and observe the ping requests and replies.

Next, we are going to initiate a perpetual ping (ping -t) from our Client-1 VM to our DC-1 VM and then change the firewall to not allow ICMP traffic to come through. Because ping uses ICMP protocol, we are going to actually block ICMP traffic on the firewall, and once that happens we should stop seeing receiving echo replies.

In Azure, go to your VM and under settings, and select inbound security rules. Here, we are going to add a new inbound security rule to "Deny" ICMP Protocol. Click "Add" and wait for the rule to be created on the VMs Network Security Group. Go back into your Client-1 VM and you will notice that the ping immediately started timing out. It's getting blocked by DC-1s firewall. You can see in Wireshark that we are getting a lot of requests, but no replies now. This is because it is being blocked by the Network Security Group, so there is no chance to receive it to send a reply anymore.
</p>

<p>
<a href="https://ibb.co/fSSGXq6"><img src="https://i.ibb.co/KGGz5b4/Screen-Shot-2023-10-24-at-10-48-24-AM.png" alt="Screen-Shot-2023-10-24-at-10-48-24-AM" border="0" /></a>
</p>
<p>
Start by connecting/logging in to the DC-1 VM as your Domain Admin account, and in to your Client-1 VM as a normal user.

On DC-1, on the C:\drive, create four folders:

- read-access
- write-access
- no-access
- accounting

Set permissions for the folders for the Domain Users group.

</p>
<br />

<p>
<a href="https://ibb.co/D8YY9Vx"><img src="https://i.ibb.co/cCww6gG/Screen-Shot-2023-10-24-at-11-01-19-AM.png" alt="Screen-Shot-2023-10-24-at-11-01-19-AM" border="0" /></a>
</p>
<p>
On Client-1 (normal user), navigate to the shared folder (start, run, \\DC-1).

Attempt to access the folders you just created and see what happens. You should be hit with a Network Error notification telling you that you do not have access to the folder. Because we set permissions for viewing for the Domain Users group only, it should make sense that when logged in as a normal user, we are not authorized to perform this action. To change this, go back to where we created the folders (c:\drive on DC-1), and toggle the permission settings. Go back to Client-1 as a normal user and test access again until successful.
</p>
<br />

<p>
<a href="https://ibb.co/4ZTmtLy"><img src="https://i.ibb.co/svwmR4S/Screen-Shot-2023-10-24-at-11-22-06-AM.png" alt="Screen-Shot-2023-10-24-at-11-22-06-AM" border="0" /></a>
</p>

<p>
<a href="https://ibb.co/1Z33KqL"><img src="https://i.ibb.co/XX99psb/Screen-Shot-2023-10-24-at-11-28-23-AM.png" alt="Screen-Shot-2023-10-24-at-11-28-23-AM" border="0" /></a>
</p>

<p>
<a href="https://ibb.co/zrYLdpJ"><img src="https://i.ibb.co/c1Hpq0L/Screen-Shot-2023-10-24-at-11-36-10-AM.png" alt="Screen-Shot-2023-10-24-at-11-36-10-AM" border="0" /></a>
</p>

<p>
Now we are going to create a new security group, add a user, set permissions, and test access. Go back to DC-1, in Active Directory, create a new Security Group called "Accountants". On the accounting folder we created in the first step, set the permissions for the Accounting Security Group, and the security permissions to "read/write". Return to Client-1 (logged in a the same normal user), and try to access the accountants folder. It should fail.

To fix this, we need to log out of Client-1 as our normal user, return to our DC-1 connection, and navigate to Active Directory to add our normal user as a member of the Accountants Security Group.

Now, return to your Client-1 connection, log in as the normal user you added to the Accountants Security Group, and try accessing the accountants folder again. It should work.
</p>
<br />
