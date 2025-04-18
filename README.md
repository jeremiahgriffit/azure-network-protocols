<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Video Demonstration</h2>

- ### [YouTube: Azure Virtual Machines, Wireshark, and Network Security Groups](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Deploy two Azure Virtual Machines (Windows & Ubuntu)
- Create and assign Network Security Groups (NSGs) to each VM
- Configure NSG rules to allow RDP, SSH, HTTP, and ICMP traffic
- Capture and analyze traffic using Wireshark and command-line tools (e.g., ping, curl, tracert)

<h2>Actions and Observations</h2>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Initially, we set up Network Security Groups (NSGs) on the two virtual machines (one running Windows and the other Ubuntu) in Microsoft Azure. We established inbound rules to permit ICMP (Ping), SSH, RDP, and HTTP traffic. This enabled us to keep track of how certain port openings impacted the connectivity between the two VMs. We confirmed that we could send ICMP traffic through to the VMs only when the NSG allowed it.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
With connectivity confirmed, we started Wireshark on the Windows VM and did packet captures while we sent HTTP, RDP, and ping traffic to the Ubuntu VM. We filtered on various protocols—including icmp, tcp.port == 80, and dns—to really narrow down the relevant traffic. And then we got to see traffic types and headers confirmed right in the packet analyzer. It was kind of fun. Not in a go-to-the-movies-for-fun kind of way, but in a more geeky, look-how-NSGs-work-for-us kind of way.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We assessed NSG rules by eliminating permitted traffic types, such as using Wireshark to confirm that traffic had ceased reaching its target. One way we did this was by trying to send ICMP packets after "removing" ICMP from the NSG. We got no replies back, either from the destination or from any of the intermediate Azure virtual network layer 3 and layer 4 elements that might have controlled the traffic.
</p>
<br />
