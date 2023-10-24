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

- Create sample file shares with various permissions
- Attempt to access file shares as a normal user
- Create a new security group, assign permissions, and test access

<h2>Actions and Observations</h2>

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

Attempt to access the folders you just created and see what happens. You should be hit with a Network Error notification telling you that you do not have access the folder.
</p>
<br />

<p>
<a href="https://ibb.co/D8YY9Vx"><img src="https://i.ibb.co/cCww6gG/Screen-Shot-2023-10-24-at-11-01-19-AM.png" alt="Screen-Shot-2023-10-24-at-11-01-19-AM" border="0" /></a>
</p>
<p>
On Client-1 (normal user), navigate to the shared folder (start, run, \\DC-1).

Attempt to access the folders you just created and see what happens. You should be hit with a Network Error notification telling you that you do not have access the folder.
</p>
<br />
