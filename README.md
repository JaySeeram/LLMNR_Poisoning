# LLMNR Poisoning

## Description
**LLMNR** is an acronym for Link-Local Multicast Name Resolution. It is a name resolution protocol or service used on windows environments. Its purpose is to resolve the IP address of a host situated on the identical local network in instances where the DNS server is not accessible. 

It works by sending the request to all the devices across the network requesting a specific host name. It does this, using a **name resolution request packet**.

It broadcasts that packet to all the devices on the network, and if there is a device with that host name, it will respond with the **name resolution response packet**. This packet contains its IP address and then the connection will be established with the requesting device.

## Disclaimer ⚠

The information provided in this project is for educational purposes only. It is not intended to promote or encourage any illegal activities. Any attempt to exploit vulnerabilities discussed in this project should only be performed in a controlled environment with proper authorization. The author and contributors of this project shall not be held responsible for any misuse or damage caused by the implementation of the techniques described herein.

## How LLMNR poisoning works

Lets say, a user or victim is requesting for any shared drive or folder. The problem here is that, the DNS can't connect to that shared drive because it does not even exist.

So the server replies back saying that the victim cannot connect to that resource. Now the victim multicasts that request to the entire network, in case any other user on the network knows the root to the shared drive.

You can observe in the diagram below, how the victim is multicasting his request to the enitre network. The victim does not know that the attacker is also connected to the network.

<p align="center">
<b>Victim is multicasting the request</b>
<br/>
  <img src="https://github.com/JaySeeram/LLMNR_poisoning/blob/main/GIFs/Multicast.gif" height="95%" width="95%"/>
<br/>
<br/>
</p>

#

Now the attacker will spoof himself as an authorized user and respond back to the victims request with a **name resolution packet** saying that he knows the root of that resource, and asks the victim for his username and hash.

And this is how LLMNR poisoning works.

<p align="center">
<b>NTLM Hash obtained</b>
<br/>
  <img src="https://github.com/JaySeeram/LLMNR_poisoning/blob/main/GIFs/Harvesting.gif" height="95%" width="95%"/>
<br/>
<br/>
</p>

## Practical Demonstration

Elevate your privileges by executing the following command:
```
sudo su
```

<p align="center">
<b>sudo su</b>
<br/>
  <img src="https://github.com/JaySeeram/LLMNR_poisoning/blob/main/GIFs/Sudo%20Su.gif" height="95%" width="95%"/>
<br/>
<br/>
</p>

#

There is a tool that comes pre-installed with kali linux, called **Responder**. We use this tool to capture the multicast request from the victim. So lets run this tool by executing the following command:
```
responder -I eth0 -d -w -v
```
> **Note** Let's see what this command does
> 1. `responder` this is the command to run the tool.
> 2. `-I eth0` this option specifies the network interface (**eth0** in this case) on which responder will listen for network traffic.
> 3. `-d` this option tell Responder to enable HTTP and SMB Direct Host capture mode. It captures NTLMv1/NTLMv2 challenges and hashes. It also enables the DNS and WPAD spoofers.
> 4. `-w` this option tells Responder to spawn a web server for drive-by attacks.
> 5. `-v` this option enables verbose mode, providing more detailed output about the actions being performed by the Responder.

<p align="center">
<b>responder -I eth0 -d -w -v</b>
<br/>
  <img src="https://github.com/JaySeeram/LLMNR_poisoning/blob/main/GIFs/Responder.gif" height="95%" width="95%"/>
<br/>
<br/>
</p>

Now it's listening for events. Any request that victim makes now will be captured here.

#


