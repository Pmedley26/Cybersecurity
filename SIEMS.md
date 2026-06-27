# SIEMS: Splunk, Wazuh, Elastic Demonstration

Video Demonstration: *In Progress*

# Splunk: 

Splunk is one of the most common Security Information and Event Management Tools. It uses a query language called SPL in order to search through data that's been ingested and indexed through the Splunk Search Head. For this demonstration I'll be using a sysmon dataset extracted from a Windows Endpoint. 

<img width="1643" height="1216" alt="Splunk Instance" src="https://github.com/user-attachments/assets/987d353e-9ac2-4324-b16b-c4b799f81a85" />

Above you can see the splunk instance created and it's searching through an index that was created manually in order to search through security/sysmon logs. 


# Scenario: 

“Palo Alto's Unit42 recently conducted research on an UltraVNC campaign, wherein attackers utilized a backdoored version of UltraVNC to maintain access to systems. This lab is inspired by that campaign and guides participants through the initial access stage of the campaign.” 
- *Please Note, this scenario uses a different dataset than the default one created above*


# Question 1: How many Files were Created? How would you find this in splunk?
- Files created can be found by querying the index and using EventCode 11 for File Creation. Each result found would count as a created file.

<img width="3815" height="1911" alt="File Creation Splunk" src="https://github.com/user-attachments/assets/d4a813c7-617c-420f-b966-4ad51bdfb97e" />


# Question 2:  In splunk, what steps would you take to identify malicious processes? What is the malicious process that infected the victim's system?
- We can query the Index as well as Event Code 1 using the following: Index="SysmonSplunkLab" "EventCode=1"
- Afterwards, I'd start by filtering the data and looking at images. In this context, images are paths to file or applications that were executed and launched a process.  Once I find something that looks suspicious, I can expand and look for any captured hashes related to that image, then use Virustotal to invesitgate further.

<img width="903" height="588" alt="image" src="https://github.com/user-attachments/assets/7f14bbf8-50b7-481e-b8f7-7b0e9dc5d62b" />

- If I expand it, I can see one file that looks suspicious. I'm referring to the Preventivo24.02.01. If I expand the entry I can find the hash, then enter it into VirusTotal.

- <img width="1555" height="759" alt="image" src="https://github.com/user-attachments/assets/68366b68-1356-47fa-b262-a4a75cf20ea5" />

<img width="1325" height="811" alt="image" src="https://github.com/user-attachments/assets/c9e96b8e-c4c2-4a25-8491-635c6ef68f3e" />

-The image above shows that 47 vendors have detected this file and marked it malicious. 



