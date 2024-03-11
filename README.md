<h1>Practice Competition Lab Creation</h1>

<h2>Description</h2>
This project is to create a personal sandbox environment modeled after the NCAE Mini Hack sandbox. The purpose of this project is to practice the necessary technical skills for the NCAE competition. 
<br />

<h2>Making the Virtual Machines</h2>

For this project, I wanted to mimic the setup for the NCAE Mini Hack sandbox network. I decided on three machines: (1) an Ubuntu machine to host the services necessary for competition, (2) a Kali machine for some variety and to test connectivity inside of the network, and (3) a CentOS 7 machine to serve as a router. 

I started by creating three VM profiles for the three machines, assigning each with 6GB of RAM, two CPU threads, and 128MB of VRAM. To accomplish the network typology shown in the diagram (fancy, I know), I set one adapter each on the Ubuntu and Kali machines and two adapters on the CentOS machine. I set the adapters for the Ubuntu and Kali machines to the “Internal Network” mode, that way they can only communicate with other VMs on the internal network and they themselves cannot access the internet. For the CentOS machine, I configured one adapter to face the internal network and the other to face the external network, AKA my home network, so it can both route traffic from the internal network to the internet and be assigned an IP address from my home network’s router.

<h2>Installing the Operating Systems</h2>

I’m no stranger to installing various flavors of Linux, so the installation process went smoothly. I hadn’t ever installed CentOS myself, and I was worried about the difficulty of the installation process going into this project. Thankfully, despite CentOS being a primarily CLI environment, I was greeted with a fantastically simple GUI interface to punch in my desired settings. All usernames were set to “quinn”, and all passwords were set to be the same for ease of use. 

<h2>Configuring the IP Address Schemes</h2>

Following the hand-written network typology, I configured each machine with its proper IP address, subnet mask, and gateway. Since my home network uses a classful, private class C address scheme (192.168.#.#/24), I wanted to use a classful, private class A scheme (10.#.#.#/8) to discern the two networks more easily. For the sake of ease, I configured the Ubuntu and Kali machines via their GUI menus with the appropriate settings. 

For the CentOS machine, I opted for the more manual route by editing the file at the following path: /etc/sysconfig/network-scripts/ifcfg-enp0s#, where ‘#’ is either 3 or 8 depending on which of the two interfaces is being configured. I configured the interface labelled “enp0s3” as my internally facing adapter and “enp0s8” as my externally facing adapter. Since this was going to serve as the router, and the external interface would be assigned via DHCP, I did not set any default gateways or routes on this machine. Since the IP addresses on this machine were assigned via editing the configuration files, the network service needed to be restarted. 

<h2>Router Configuration</h2>

At this point, all three VMs can ping one another. Amazing! There’s only one step left in order to connect this internal network to the outside world, and that is configuring the CentOS machine to route traffic between the two networks. On CentOS, there are different pre-defined zones that interfaces can be assigned to determine the interface's properties. By default, all interfaces are set to the public zone, which might be alright, but there is a better option available. The internal interface should be set to the internal zone and the external interface to the external zone. For this typology, these predefined zones will get the job done perfectly. Since the external zone has masquerading enabled by default, the CentOS machine can function as the default gateway for the internal network with little additional configuration.

Now, this part was where I hit a little roadblock. I had configured all the VMs’ IP addresses and assigned the interfaces their proper zones on the CentOS machine. All interfaces could ping their neighbors and the internal machines could ping both the internal and external interfaces on the CentOS machine. The CentOS machine could curl Google’s website, but neither internal machine could access the internet- why? After a couple of quick Google searches, I discovered a setting in CentOS that I was entirely unaware of; there is a single setting that was left to be enabled that would ultimately allow CentOS to forward IP traffic from inside the network. The configuration file was located at /etc/sysctl.conf, and the line that needed to be added was “net.ipv4.ip_forward = 1”. With this setting written to the proper configuration file, the CentOS machine routed traffic, and the internal systems could access the internet. 

<h2>Screenshots:</h2>

<p align="center">
(1 / 11) Creating the Network Typology: <br/>
<img src="https://i.imgur.com/KK8y7zb.jpg" height="60%" width="60%" alt="(1 / 11) Creating the Network Typology"/>
<br />
<br />
(2 / 11) Creating the VMs: <br/>
<img src="https://i.imgur.com/7jl69rF.png" height="80%" width="80%" alt="(2 / 11) Creating the VMs"/>
<br />
<br />
(3 / 11) Ubuntu and Kali Network Adapters: <br/>
<img src="https://i.imgur.com/Sr9DAVT.png" height="70%" width="70%" alt="(3 / 11) Ubuntu and Kali Network Adapters"/>
<br />
<br />
(4 / 11) CentOS Network Adapters:  <br/>
<img src="https://i.imgur.com/UzumAFK.png" height="70%" width="70%" alt="(4 / 11) CentOS Network Adapters"/>
<br />
<br />
(5 / 11) Ubuntu Configuration:  <br/>
<img src="https://i.imgur.com/xllRIAY.png" height="70%" width="70%" alt="(5 / 11) Ubuntu Configuration"/>
<br />
<br />
(6 / 11) CentOS Configuration:  <br/>
<img src="https://i.imgur.com/0EaHIgW.png" height="70%" width="70%" alt="(6 / 11) CentOS Configuration"/>
<br />
<br />
(7 / 11) Kali Configuration:  <br/>
<img src="https://i.imgur.com/iv171zk.png" height="70%" width="70%" alt="(7 / 11) Kali Configuration"/>
<br />
<br />
(8 / 11) All Systems Running:  <br/>
<img src="https://i.imgur.com/afXdBho.png" height="70%" width="70%" alt="(8 / 11) All Systems Running"/>
<br />
<br />
(9 / 11) All Systems with Configured IPs:  <br/>
<img src="https://i.imgur.com/XSvpOog.png" height="70%" width="70%" alt="(9 / 11) All Systems with Configured IPs"/>
<br />
<br />
(10 / 11) All Systems can Ping Each Other:  <br/>
<img src="https://i.imgur.com/C2LUwM1.png" height="70%" width="70%" alt="(10 / 11) All Systems can Ping Each Other"/>
<br />
<br />
(11 / 11) All Systems Online:  <br/>
<img src="https://i.imgur.com/ueHSa8E.png" height="70%" width="70%" alt="(11 / 11) All Systems Online"/>
<br />
</p>
