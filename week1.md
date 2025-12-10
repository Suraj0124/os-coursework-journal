
Phase 1: System Planning and Distribution Selection (Week 1)
OVERVIEW

•	Systems:
1.	Ubuntu server 22.04 
Network mode: NAT AND host only 
IP Address: 192.168.56.103/24
2.	Ubuntu Desktop
   Network mode: NAT AND host only
   IP Address: 192.168.56.102/24
•	Host environment
1.	 Physical Host Machine (Laptop)
2.	Virtualization Platform: Oracle VirtualBox

System Architecture Diagram showing both systems and network connections

![image alt](image/week1/System Architecture Diagram.png)

Distribution Selection Justification comparing your chosen server distribution with alternatives
Ubuntu Server
Pros
•	Easy to use as a beginner-friendly supercomputer requiring just a few minutes to start, suitable as a course work tool.  
•	Tremendous storage of the most current packages on demand to install.  
•	An enjoyable community and dozens of it all answered documentation.  
•	Very well compatible with VirtualBox and other cloud services.  
•	Frequent security releases and a 5-year LTS release cycle.  

Cons
•	A little bit more unstable than Debian where you want a long-term zero-change environment.  
•	Consumes multiple system resources more than ultra-lightdistros, which could chew of older laptops.  
•	There are certain Ubuntu Pro subscriptions required with some features of the enterprises.  
Debian
Pros
•	Very stable and reliable- backbone to millions of servers.  
•	Works best with long-term manufacturing when you have to make a few stops.  
•	Golden security due to intense checking of packages.  
•	Extremely light, very low-installation, fits well used on low-spec machines.  
Cons
•	Packages are out of date and maintain a slow update pace.  
•	Not a beginner-friendly interface; you will have to do manual set up.  
•	Newbies can find community docs to be very technical.  
•	Uncommonly found in the cloud platforms than Ubuntu.  

The Red Hat Enterprise Linux, or RHEL.  
Pros
•	Very stable, well-tested and proven to work in the enterprise.  
•	Superior security and high life cycle.  
•	Red Hat is officially supported through subscription.  
•	Good performance of corporate servers and virtualization.  
Cons
•	To have full features and updates, there is need to have a paid subscription.  
•	More difficult with amateurs or students.  
•	Restricted free repository- in most cases you will have to use EPEL or repos.  
•	Software upgrades happen at a slow pace.  

Conclusion
Ubuntu Server will be the most appropriate solution to use in this deployment as it provides the best combination of ease of use, the availability of modern software, excellent support of the community and compatibility with VirtualBox. Debian is not as user-friendly and with older packages which do well in stability. RHEL is good in enterprise processes but expensive to learn as it has to be subscribed to.  Hence, Ubuntu Server is the most convenient and effective solution in case of learning or laboratory based server environment.
 

Workstation configuration decision justifying your choice of workstation option
Workstation Configuration Decision
  Workstation OS: Ubuntu Workstation
    VirtualBox Network Adapters:
           Adapter 1: NAT
         Adapter 2: Host-Only (Static IP: 192.168.56.102/24)
Workstation Role

I installed the Ubuntu Workstation as a client computer with the sole purpose to:
•	access the Ubuntu Server
•	test client /server communication.
•	run administrative tasks
•	update da pull or pull packages over the internet.
•	be a service control terminal of the server services.
Since there will be an internal lab traffic together with access to the internet, I will require a dual-adapter setup.
Rationale of Workstation Set up.
 NAT Adapter – Internet Access
  Why use NAT:
The NAT allows the workstation to access the foreign sites of the network provided by the host.
It makes it possible to:
•	upgrade and download packages.
•	integrating utilities (such as curl, opensshnclient, browsers)
•	check documentation, tutorials and repositories.

Pros:
•	Does not require installation of a complex network - it is plug and play.
•	The VM remains out of the limelight.
•	secures the system and at the same time permits outbound traffic.
Host Only Adaptor- Internal, Network and Server Access.

Why Host‑Only:
I would require a straight connection to Ubuntu Server at 192.168.56.103.
I have a private LAN with Host Only and it consists of:
•	the host machine
•	the Ubuntu Server
•	the Ubuntu Workstation
Pros:
•	Static, fixed IPs (e.g. 192.168.56.102 of the workstation)
•	SSH panic and testing and user tests are predictable.
•	Secure and private - does not present itself to the external network.
Reasons why Ubuntu Workstation was selected (justification of choice of OS).

Professor wanted me to know why we were utilizing Ubuntu and here is a brief overview of why we were using Ubuntu as a student. At last we agreed on Ubuntu since it fulfills all our requirements in class and in the laboratory.

Pros of Ubuntu Workstation
•	Easy to use interface that does not misguide you.
•	Huge software shop and the vibrant community to reach out.
•	Ideal when it comes to practical training, fast tests, and spin-ups.
•	LTS releases are robust and long time stable.
•	VirtualBox is a breeze because it gets along well with it.

Why not alternatives of workstations?
•	Windows VM: occupies high RAM usage, is slow and the license fees will accumulate.
•	CentOS/RHEL Workstation: unnecessary complication and weight, unfriendly to beginners.
•	Debian Desktop: is tested, more dated in packages and a wee bit more difficult to navigate.

At that, Ubuntu Workstation is right in the middle of convenience, functionality and general compatibility with our projects.

Network configuration documentation covering VirtualBox settings and IP addressing

This documentation describes the network setup for a VirtualBox-based environment containing:
Ubuntu Server
VirtualBox Settings:
•	Adapter 1 → Host-Only Adapter (vboxnet0)
•	Adapter 2 →NAT
•	IP Address: 192.168.56.103/24
•	Subnet Mask:	255.255.255.0
•	Gateway: None (Host-Only network)
•	DNS: From host (optional)
![Server Host](../image/week1/serverho1.png)
 ![Server NAT](../image/week1/servernat.png)
 












Ubuntu Workstation
•	Adapter 1 → NAT
•	Adapter 2 → Host-Only Adapter 
•	Host-Only IP Address:192.168.56.102
•	Subnet Mask:255.255.255.0
•	Gateway:192.168.56.1 (host)
•	DNS: Uses NAT DHCP DNS
   


Host (Laptop)
The system uses two VirtualBox network modes:
•	Host-Only Network → internal communication
•	NAT Network → internet access 
Using a CLI, document system specifications using `uname`, `free`, `df -h`, `ip addr`, and `lsb_release`
 
•	Kernel Version: 6.14.0-27-generic
•	Architecture: x86_64 (64-bit)
•	Operating System: GNU/Linux
•	Build: Ubuntu SMP, PREEMPT_DYNAMIC
 
•	Total RAM: 3.8 GiB
•	Used RAM: 1.0 GiB
•	Available RAM: 2.8 GiB
•	Swap: 0 B (no swap configured
 
•	Primary Disk Size: 25 GB
•	Used: 5.1 GB
•	Available: 19 GB
•	Root Filesystem: /dev/sda2

 
Interface	Network Type	IP Address	Purpose
lo	Loopback	127.0.0.1	Local processes
enp0s3	NAT	10.0.2.15/24	Internet access
enp0s8	Host-Only	192.168.56.102/24	Communication with server

 
•	Distro: Ubuntu
•	Version: 24.04.3 LTS
•	Codename: noble
•	Support: Long-term support (LTS)



