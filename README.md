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

Now if the victim is trying to access any shared file that does not exist, for example let's say that the victim is looking the shared rseource donut.

Since the resource does not exist, the victim sends out a multicast request. The attacker will capture that request and tell the victim that he knows the path of the resource, give me your username and hash I will connect you with it.

But in reality the attacker stole the credentials and did not connect you with anything, hence the access denied, dialog box.

<p align="center">
<b>Multicast Request</b>
<br/>
  <img src="https://github.com/JaySeeram/LLMNR_poisoning/blob/main/PNGs/Multicast%20Request.png" height="95%" width="95%"/>
<br/>
<br/>
</p>

#

Before you know it, the attacker has gotten your username and password hash. The hacker will use his tricks to crack the hash.

<p align="center">
<b>Multicast Request</b>
<br/>
  <img src="https://github.com/JaySeeram/LLMNR_poisoning/blob/main/PNGs/Captured%20Credentials.png" height="95%" width="95%"/>
<br/>
<br/>
</p>

<p align="center">
<b>Password Hash</b>
<br/>
  <img src="https://github.com/JaySeeram/LLMNR_poisoning/blob/main/PNGs/Password%20Hash.png" height="95%" width="95%"/>
<br/>
<br/>
</p>

#

Copy the password hash and save it into a text file. By using hashcat along with the rockyou.txt wordlist we can crack the password if its not too complex. Execute the following hashcat command:
```
hashcat -m 5600 /usr/share/wordlists/rockyou.txt --force
```
> **Note** Let's see what this command does
> `hashcat` this is the name of the program you're executing.
> `m 5600` this is an argument specifying the hash mode. In this case, mode 5600 corresponds to HMAC-SHA256 (key = $pass, iv = $salt).
> `/usr/share/wordlists/rockyou.txt` this is the path to the wordlist file being used for password cracking. The file being used here is "rockyou.txt", which is a well-known and commonly used wordlist containing a large number of passwords.
> `--force` this is an option to force Hashcat to continue despite encountering runtime errors or warnings. It's typically used when you want to override certain safety checks or confirmations.

<p align="center">
<b>Password Hash</b>
<br/>
  <img src="https://github.com/JaySeeram/LLMNR_poisoning/blob/main/PNGs/Hashcat.png" height="95%" width="95%"/>
<br/>
<br/>
</p>

#

We have successfully cracked the password hash. The plain text password is **password.** This is one reason why not to use weak passwords. There is a high chance that these passwords would be lying wordlists.

<p align="center">
<b>Password Hash</b>
<br/>
  <img src="https://github.com/JaySeeram/LLMNR_poisoning/blob/main/PNGs/Decryption.png" height="95%" width="95%"/>
<br/>
<br/>
</p>

#

# Mitigation of LLMNR Poisoning
- Disable LLMNR
- Require Network Access Control
- Implement Network Segmentation
- Use Strong Passwords
