# MaLab
Full Walkthrough on making a very practical malware analysis setup. Both could be done in Cloud Environment or Local.
We will only be going over Local setup in this walkthrough as cloud instances vary from provider to provider. If planning to do this for a cloud envoirnment factor in editing LDAP-RBAC policies, Vnets to segment this dangerous part of the network

# Requirements

1: Platform for hypervisor
 
   A. Workstation with 16gb RAM, 8 cores + hyper/multi-thread
 
   B. Cloud instance with 8 cores 4gb for each VM instance (Roughly)

2: Ubuntu LTS 20.4 ISO (LTS 22 does not work)

3: HyperVisor, VMWare, Virtual Box. (I use VMWare Workstation Pro, but something less fancy will do. Make sure to have SNAPSHOT capibilities)

4: Sift Workstation

5: Remnux Ubuntu Addon

# DISCLAIMER

I am not responsible for any damages done to a machine with carelessness or improper setup. Handling/Detonating malware is like Handling/Detonating a bomb. You want to be as far away from the source as possible. Implementing segmented parts of the network that only have two way communication between the Forensic machine and Victim machine is absolutely necessary. The goal isn't to bomb ourselves its to analyze the bombs impact.

# Start Here

First we need to download and install hypervisor of our choice. While we do that go here to download Ubuntu 20.4 LTS 
https://ubuntu.com/download/alternative-downloads

Next we upload the Ubuntu 20.04 ISO to the hypervisor platform

From here startup Ubuntu and go through the normal setup process
Once the desktop is loaded go to terminal, download Sift Repository. 

---------------------------------------------------------------------
cd Downloads/

sudo mv sift-cli-linux /usr/local/bin/sift

sudo chmod 755 /usr/local/bin/sift

sudo apt-get update

sudo sift install
----------------------------------------------------------------------

![image](https://user-images.githubusercontent.com/110573266/186030045-e0acdfe6-7c49-4903-a747-88408395100c.png)



AFTER Completing Sift Install.

sudo reboot

# Installing REMnux Distro
![image](https://user-images.githubusercontent.com/110573266/186029473-22f484d7-9440-4a9a-b8d2-217df9ebbfc7.png)

----------------------------------------------------------------------------------------------------------------
wget https://REMnux.org/remnux-cli

mv remnux-cli remnux <For Easy Access>

sudo chmod +x remnux

sudo mv remnux /usr/local/bin

sudo remnux install --mode=addon
----------------------------------------------------------------------------------------------------------------
![image](https://user-images.githubusercontent.com/110573266/186030869-9010a03a-0db1-4ee3-a1a8-6ec69debde61.png)


# Windows VM Installation
Install Windows on your Hypervisor (There are many guides to help with this I am not going to go into too much detail here)

![image](https://user-images.githubusercontent.com/110573266/186035045-5cef82b1-89d8-41f9-a70c-1907bb1c86a0.png)

Once Installed we need to configure our DNS/Network traffic to route to the Analysis Enviornment. This Varies Hypervisor to HyperVisor but the general idea is to subnet the two machines to its own VM subnet for the two to communicate. As we want all Windows traffic to be routed to our essentially malware honeypot.

We first create the subnet to do this.
Here is a screenshot of my setup in VMware both hosts are setup the same. I used a nice feature that comes with this version allowing me to create a VMnet. Host-Only will work just as well. 

![image](https://user-images.githubusercontent.com/110573266/186047984-92fa5fd0-90e2-4969-a260-c1769c9656fe.png)

--------------------------------------------------------------------------------

Now we switch over to our REMnux workstation
We need to edit our inetsim config. Inetsim fakes all internet services on your machine. Allowing for DNS and HTTP requests to be somewhat completed. This enables us to sit in the middle and analyze any traffic we want, to reverse engineer the malware/ransomware we are doing analysis on. You name it, it fakes it.


--------------------------------------------------------------------------------
sudo vim /etc/inetsim/inetsim.conf
--------------------------------------------------------------------------------

Copy the settings I have configured here:

![image](https://user-images.githubusercontent.com/110573266/186050338-0a948964-7b19-4e14-8eeb-c12b550cc67b.png)


As well we need to change the "service_bind_address". Since my IP is redacted. The IP used for this setup will be whatever ifconfig shows in your REMnux Workstation. Since we want all of our traffic to be resolved in the box. We need to configure the inetsim to do so. 

![image](https://user-images.githubusercontent.com/110573266/186052768-6a279742-05c0-46e5-8253-a865fb7f46f5.png)

We shall do the same for the DNS address as well:

![image](https://user-images.githubusercontent.com/110573266/186052895-b5bcfe15-1232-471b-81ff-c10e6f526c64.png)

Now since we have established a network for communication and configured our victim box to route its traffic to our analysis box. We can continue. To get this up and running:

sudo inetsim
---------------------

![image](https://user-images.githubusercontent.com/110573266/186053468-d82aac31-306b-4b76-9428-4a676b2b0fa5.png)

This is the home stretch. There are additional tools to be used on the windows machine. I will just list them here. DO NOTE you have to switch back to the NAT option before installing the additional tools to the Victim Machine. If you are new I reccomend almost everything to learn what works best for you. If you are experienced of course, pick your poison.

-----------------------------------------------------------------------------------------------------------------------------
https://blog.didierstevens.com/my-software/

https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite ---> Suite

https://docs.microsoft.com/en-us/sysinternals/downloads/procmon ---> Single

https://www.winitor.com/

https://processhacker.sourceforge.io/

https://sourceforge.net/projects/regshot/

https://www.mcafee.com/en-us/consumer-corporate/mcafee-labs/free-tools/stinger.html


![image](https://user-images.githubusercontent.com/110573266/186064331-1e63c7c7-a41e-4a87-a9d8-f3c9c0c8a112.png)


------------------------------------------------------------------------------------------------------------------------------
# END 
To finalize our setup. This is where the SNAPSHOT capabilities come into play. We need to take snapshots of each machine before use. So when the bomb is detonated we can reverse all effects done to both systems (if malware was dangerous enough). This ensures Host system security on top of REMnux Workstation Security, plus resets victim machine to our defaults for quick plug and play.

For this to work flawlessly I reccomend safely shutting down both VMs and creating snapshots. Since creating snapshots differ from Hypervisor to Hypervisor I will only show a screenshots of both snapshots for my setup. These snapshots can also be used to move hosts with proper care of preserving data.

Victim Defaults
----------------

![image](https://user-images.githubusercontent.com/110573266/186066463-d799e42e-cdb2-4a10-a102-62f888985c08.png)

REMnux Lab
----------------

![image](https://user-images.githubusercontent.com/110573266/186066602-da2b4372-2797-4a5a-adc2-d46d8752dc20.png)


I hope this helped someone!

8/22/2022 Fathedd
