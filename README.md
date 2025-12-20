#This README serves as documentation for the Lab Setup and includes the steps taken during the initial setup as well as any changes along the way. 


#Started off by installing Pfsense. I initially had issues installing pfsense through Wifi so I changed to a stable ethernet connection and installed it without issues. The lab Has a Pfsense firewall, a handful of VMs in what’s called the “ECORP” LAN/subnet, and another LAN called the “Attacker LAN”. The Pfsense was configured to have both LANs on on different subnet. I configured the WAN and both LAN interfaces on the Pfsense firewall. The Pfsense firewall has 3 adapters. The first one is for the WAN connection and is configured as a bridged adapter which allows it to connect to my home router for IP distribution and internet access. The other two adapters will be used to connect to the other LANs from the firewall. 

<img width="722" height="502" alt="Pfsense configuration" src="https://github.com/user-attachments/assets/c276af1d-cf8d-43fa-9b43-a8397cd72a0c" />


#The connection to Pfsense is initially marked as  insecure because it’s using a self signed certificate

Upon Logging in, you can see the two previously configured LANs as 10.0.1.1 and 10.0.3.1 under “Interfaces” 

<img width="1282" height="1412" alt="Initial Pfsense Configuration" src="https://github.com/user-attachments/assets/cac6eae6-e97c-4d87-9ea3-e7bc93cd60dd" />


#RFC1918 is the standard for Private IP addresses. On the WAN interface these are blocked by default, but for Lab purposes I disabled the block so that the internal devices can communicate through the firewall

Afterwards, changed the name of the LAN interface to Ecorp. The IP of 10.0.1.1 is the gateway IP address for the LAN 1 subnet. 

Did the same with LAN 2 or OPT1. This is the attack LAN and the LAN that will be used for penetration/attacks on LAN 1. 

Afterwards, enabled the DNS Resolver Service. This allows for devices that request a DHCP lease to be registered to the DNS resolver so their names can be resolved. Prefetch Support and Prefetch DNS Key support were also enabled. 

Under system —> Advanced, Enabled Hardware Checksum Offloading. Had to reboot the firewall after this. 

Throughout this process I learned about a classification of malware called “Stealer Malware” which is capable of stealing all browsing data, including Cached Passwords stored. 

After reboot, went to Status —> DHCP Leases

At this point, my Windows 11 VM/Host machine should be the only lease, and it is as shown here: 


<img width="1190" height="602" alt="DHCP Lease 1" src="https://github.com/user-attachments/assets/3fe6250c-d8f1-486e-b3de-2392c350cf52" />


#The next thing I did was create an alias. These are used so IP addresses don’t have to be typed out all the way*

<img width="1352" height="780" alt="pfsense alias" src="https://github.com/user-attachments/assets/859a47b9-a92f-4012-8373-aeb7d2a8317a" />


# Next is the firewall rules: 

By default Pfsense OOB has an Anti-Lockout rule which as the name implies, prevents you from locking yourself out of the firewall. Blocking 443 and 80 would mean no access to the firewall. In Pfsense, the very first rule takes precedence over the rules below it. I want to allow for internal communication within the Ecorp LAN first. Below you’ll see the added firewall rule configuration: 

<img width="1246" height="1187" alt="Ecorp Firewall Rule" src="https://github.com/user-attachments/assets/688be6c6-4c96-4c8a-adba-187dfb96feeb" />



# The next part will sound ironic, but I also created a rule for the ECORP subnet to be able to communicate with the Attack LAN. This is for lab purposes only of course. The Below rule allows for the ECorp LAN to communicate with public IP addresses. 

<img width="1271" height="1032" alt="Ecorp to Internet" src="https://github.com/user-attachments/assets/1d88773d-1937-4f15-9fe8-2b950cde5345" />


# Moving on to the Attack LAN firewall rules(This has no default rules). 

Set the rule for the Attack LAN to reach Ecorp LAN. 

Later in the lab, a Kali linux box is going to be used for the attacking. 


<img width="1306" height="1352" alt="Block Internet Access from ATTACK LAN" src="https://github.com/user-attachments/assets/40a86e8c-5091-475a-9b39-a181b22e7fc0" />


# The last  rule is related to the WAN interface which in this case is my home router. It’s still providing an IP address through Virtual Box as a Bridged adapter, so I want to block coming out the WAN interface from the Attack LAN to prevent interference. 

Rebooted the firewall again after this. 


# Next Steps are to add the Domain Controller to the Lab by Installing Windows Server 2025 Evaluation and Installing Active Directory on it. 


