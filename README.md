# LLMNR Poisoning

## Description
**LLMNR** is an acronym for Link-Local Multicast Name Resolution. It is a name resolution protocol or service used on windows environments. Its purpose is to resolve the IP address of a host situated on the identical local network in instances where the DNS server is not accessible. 

It works by sending the request to all the devices across the network requesting a specific host name. It does this, using a **name resolution request packet**.

It broadcasts that packet to all the devices on the network, and if there is a device with that host name, it will respond with the **name resolution response packet**. This packet contains its IP address and then the connection will be established with the requesting device.

## Disclaimer ⚠

The information provided in this project is for educational purposes only. It is not intended to promote or encourage any illegal activities. Any attempt to exploit vulnerabilities discussed in this project should only be performed in a controlled environment with proper authorization. The author and contributors of this project shall not be held responsible for any misuse or damage caused by the implementation of the techniques described herein.

## How LLMNR poisoning works

<p align="center">
<b>Root User</b>
<br/>
  <img src="https://github.com/JaySeeram/LLMNR_poisoning/blob/main/GIFs/Multicast.gif" height="40%" width="40%"/>
<br/>
<br/>
</p>

<br>


https://github.com/JaySeeram/LLMNR_poisoning/blob/main/Render.mp4

