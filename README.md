#This README serves as documentation for the Lab Setup and includes the steps taken during the initial setup as well as any changes along the way. 


Started off by installing Pfsense. I initially had issues installing pfsense through Wifi so I changed to a stable ethernet connection and installed it without issues. The lab Has a Pfsense firewall, a handful of VMs in what’s called the “ECORP” LAN/subnet, and another LAN called the “Attacker LAN”. The Pfsense was configured to have both LANs on on different subnet. I configured the WAN and both LAN interfaces on the Pfsense firewall. The Pfsense firewall has 3 adapters. The first one is for the WAN connection and is configured as a bridged adapter which allows it to connect to my home router for IP distribution and internet access. The other two adapters will be used to connect to the other LANs from the firewall. 

<img width="722" height="502" alt="Pfsense configuration" src="https://github.com/user-attachments/assets/c276af1d-cf8d-43fa-9b43-a8397cd72a0c" />
