# SIEMS: Splunk, Wazuh, Elastic Demonstration

Video Demonstration: *In Progress*

# Splunk: 

Splunk is one of the most common Security Information and Event Management Tools. It uses a query language called SPL in order to search through data that's been ingested and indexed through the Splunk Search Head. For this demonstration I'll be using a sysmon dataset extracted from a Windows Endpoint. 

<img width="1643" height="1216" alt="Splunk Instance" src="https://github.com/user-attachments/assets/987d353e-9ac2-4324-b16b-c4b799f81a85" />

Above you can see the splunk instance created and it's searching through an index that was created manually in order to search through security/sysmon logs. 


# Scenario: 

“Palo Alto's Unit42 recently conducted research on an UltraVNC campaign, wherein attackers utilized a backdoored version of UltraVNC to maintain access to systems. This lab is inspired by that campaign and guides participants through the initial access stage of the campaign.”


# Question 1: How many Files were Created? How would you find this in splunk?
- Files created can be found by querying the index and using EventCode 11 for File Creation. Each result found would count as a created file.

<img width="3815" height="1911" alt="File Creation Splunk" src="https://github.com/user-attachments/assets/d4a813c7-617c-420f-b966-4ad51bdfb97e" />
