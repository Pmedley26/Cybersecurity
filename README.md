# This README serves as documentation for the Lab Setup and includes the steps taken during the initial setup as well as any changes along the way. 

# Summarized Video Version: https://youtu.be/O_EqyqlHc-M
# Part 2: Configuring DC  https://youtu.be/HT9EUnJRs7g

# Part 3(Final): https://www.youtube.com/watch?v=QlcXdttQFqQ


- Started off by installing Pfsense. I initially had issues installing pfsense through Wifi so I changed to a stable ethernet connection and installed it without issues. The lab Has a Pfsense firewall, a handful of VMs in what’s called the “ECORP” LAN/subnet, and another LAN called the “Attacker LAN”. The Pfsense was configured to have both LANs on on different subnet. I configured the WAN and both LAN interfaces on the Pfsense firewall. The Pfsense firewall has 3 adapters. The first one is for the WAN connection and is configured as a bridged adapter which allows it to connect to my home router for IP distribution and internet access. The other two adapters will be used to connect to the other LANs from the firewall. 

<img width="722" height="502" alt="Pfsense configuration" src="https://github.com/user-attachments/assets/c276af1d-cf8d-43fa-9b43-a8397cd72a0c" />


# The connection to Pfsense is initially marked as  insecure because it’s using a self signed certificate

- Upon Logging in, you can see the two previously configured LANs as 10.0.1.1 and 10.0.3.1 under “Interfaces” 

<img width="1282" height="1412" alt="Initial Pfsense Configuration" src="https://github.com/user-attachments/assets/cac6eae6-e97c-4d87-9ea3-e7bc93cd60dd" />


# RFC1918 is the standard for Private IP addresses. On the WAN interface these are blocked by default, but for Lab purposes I disabled the block so that the internal devices can communicate through the firewall

- Afterwards, changed the name of the LAN interface to Ecorp. The IP of 10.0.1.1 is the gateway IP address for the LAN 1 subnet. 

- Did the same with LAN 2 or OPT1. This is the attack LAN and the LAN that will be used for penetration/attacks on LAN 1. 

- Afterwards, enabled the DNS Resolver Service. This allows for devices that request a DHCP lease to be registered to the DNS resolver so their names can be resolved. Prefetch Support and Prefetch DNS Key support were also enabled. 

- Under system —> Advanced, Enabled Hardware Checksum Offloading. Had to reboot the firewall after this. 

* Throughout this process I learned about a classification of malware called “Stealer Malware” which is capable of stealing all browsing data, including Cached Passwords stored. 

- After reboot, went to Status —> DHCP Leases

# At this point, my Windows 11 VM/Host machine should be the only lease, and it is as shown here: 


<img width="1190" height="602" alt="DHCP Lease 1" src="https://github.com/user-attachments/assets/3fe6250c-d8f1-486e-b3de-2392c350cf52" />


# The next thing I did was create an alias. These are used so IP addresses don’t have to be typed out all the way*

<img width="1352" height="780" alt="pfsense alias" src="https://github.com/user-attachments/assets/859a47b9-a92f-4012-8373-aeb7d2a8317a" />


# Next is the firewall rules: 

- By default Pfsense OOB has an Anti-Lockout rule which as the name implies, prevents you from locking yourself out of the firewall. Blocking 443 and 80 would mean no access to the firewall. In Pfsense, the very first rule takes precedence over the rules below it. I want to allow for internal communication within the Ecorp LAN first. Below you’ll see the added firewall rule configuration: 

<img width="1246" height="1187" alt="Ecorp Firewall Rule" src="https://github.com/user-attachments/assets/688be6c6-4c96-4c8a-adba-187dfb96feeb" />



# The next part will sound ironic, but I also created a rule for the ECORP subnet to be able to communicate with the Attack LAN. This is for lab purposes only of course. The Below rule allows for the ECorp LAN to communicate with public IP addresses. 

<img width="1271" height="1032" alt="Ecorp to Internet" src="https://github.com/user-attachments/assets/1d88773d-1937-4f15-9fe8-2b950cde5345" />


# Moving on to the Attack LAN firewall rules(This has no default rules). 

- Set the rule for the Attack LAN to reach Ecorp LAN. 

- Later in the lab, a Kali linux box is going to be used for the attacking. 


<img width="1306" height="1352" alt="Block Internet Access from ATTACK LAN" src="https://github.com/user-attachments/assets/40a86e8c-5091-475a-9b39-a181b22e7fc0" />


# The last  rule is related to the WAN interface which in this case is my home router. It’s still providing an IP address through Virtual Box as a Bridged adapter, so I want to block coming out the WAN interface from the Attack LAN to prevent interference. 

- Rebooted the firewall again after this. 


# Next Steps are to add the Domain Controller to the Lab by Installing Windows Server 2025 Evaluation and Installing Active Directory on it. 

- The DC was configured as the Root Certificate authority for the domain. A static IP was also given to the server as best practice. 


<img width="1887" height="970" alt="Certificate Authority Ecorp DC" src="https://github.com/user-attachments/assets/e9ec0ca3-3257-40a6-89e0-e67297a6ede2" />

# User Accounts and Service Account

<img width="1872" height="1006" alt="AD Users and Service Account" src="https://github.com/user-attachments/assets/d3781c3b-784c-49ff-8016-890e339b000e" />


Group Policy to Prevent Windows Defender from Running on any Ecorp Nodes


<img width="1046" height="755" alt="Group Policy" src="https://github.com/user-attachments/assets/59776a3f-8940-47c7-9b85-a78deb9c5166" />


# Additional Steps: 
- Added Windows 11 VM to the Domain
- Added a Service Principal name to the SQL Service Account

  Next Steps: Install and Configure Metaspolitable.

  # What is Metasploitable?
  - Vulnerable linux Vm developed by Rapid 7
  - Designed to be a safe environment for vulnerability scanning and penetration testing(Red and Blue Team Median)

# Since this is an already existing VM with a preconfigured Virtual Hard disk, I just need to add it to Virtualbox. Metasploitable runs under Legacy Debian Settings so I have to set it up a bit differently from the other VMs, specifically the network adapter used. This VM will be on the Ecorp Subnet and not the Attacker LAN. 


<img width="1842" height="925" alt="Metasploitable Configuration" src="https://github.com/user-attachments/assets/a839bc9b-1c65-48b5-943b-e643ae8c792a" />


  # Upon signing in, the VM immediately got an IP address from the Ecorp LAN Subnet

<img width="1513" height="958" alt="Metaspolitable IP Configuration" src="https://github.com/user-attachments/assets/82f73ac6-1b66-46ec-b22f-2435c85b9c99" />


Next Steps: Configuring Kali Linux

# Installed Kali Linux and Assigned it to the Attack LAN. It obtained an IP address on 10.0.3 subnet as specified in the Pfsense configuration through DHCP


Final Steps For Lab Setup: Install Sysmon on Windows 11

# What is sysmon? 
- A windows system service and device driver
- Monitors and Logs system activity to the Windows Event Log
- It is a very useful tool for Identifying suspicious activity and knowing what processes, files, etc are involved in a potential cyber incident.


#  Sysmon has a configuration file which determines which logs and activity are monitored/logged and accessed through Event Viewer. 
- Installed Sysmon from official Microsoft link, then grabbed a configuration file needed for the purpose of the lab. 
- Navigated to sysmon directory using cmd and entered the following command
- Sysmon.exe -accepteula -i sysmonconfig.xml

- <img width="1890" height="981" alt="Sysmon Logs" src="https://github.com/user-attachments/assets/3d70bace-44c8-41d5-8f17-f5570265447f" />

Next Steps: Install Splunk on Windows 11 VM. 

# What is Splunk? 
- Used to collect, search, analyze, and visualize machine generated data.
- Splunk is often used for Log Management, Security and Event Management across IT and Security teams globally. 
- SIEM = Security Information and Event Management, Similar to Microsoft Sentinel.


# Splunk Installation

- Installed splunk from the official website link, configuring all of this on the VM and not setting up forwarders or a centralized splunk server to make the lab easier to configure

- Installed Splunk Add-On for Sysmon

- Also Installing the Add-On for Microsoft Windows. 

- Afterwards, I went to Settings, Data Inputs, and Enabled Remote Log collections. 

- I Ran the CMD and entered IP config on the Windows 11VM, which splunk should’ve ingested. 

<img width="1888" height="1003" alt="Splunk Log Example" src="https://github.com/user-attachments/assets/f0e6a891-0142-495c-8a40-fdf52cc42ed6" />


# This completes the Lab Setup and the End of the Readme. There is another Readme in the same repository that will be using this setup for future Lab demonstrations

# Thank you for taking the time to view the ReadMe as well as Watch the Videos. My goal for this is to demonstrate my expertise in the field of cybersecurity, as well as show my eagerness to learn and develop my skillset.



