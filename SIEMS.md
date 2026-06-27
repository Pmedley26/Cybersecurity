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

# Question 3: There was a cloud drive used to Distribute malware to the endpoint. Which one was it and how do you identify it? 

- This can actually be found in the community section on virustotal, but through splunk, we can pivot from the malware name and the FileCreate Event Code 11
- index="sysmonsplunklab" Preventivo24.02.14.exe.exe EventCode=11

- <img width="1664" height="762" alt="image" src="https://github.com/user-attachments/assets/ff910187-9f96-44b9-acea-8d51ea8fe61a" />

- We can also look into the surrounding events by filtering the time and selecting +/-5 seconds. This gives us all the events that occured before and after the time the file was written to the disk.

- <img width="1512" height="529" alt="image" src="https://github.com/user-attachments/assets/e825c84b-fd9b-49ce-a79a-928fe930bec1" />

- Moving down to the first event, we see EventCode 22(DNS). After Expanding, we see the DNS query for Dropbox.

- <img width="1617" height="761" alt="image" src="https://github.com/user-attachments/assets/3fd077fa-6e29-4b1a-b979-5c367736e602" />

# Question 4: Attackers will sometimes us a defensive evasion technique where the file creation date is changed to make it appear old. What was the timestamp changed to for a PDF File? 

- Sysmon actually has an Eventcode for this (EventCode 2 Specifically). We can also simply add "PDF" to the end of the query to further filter the results
- index="sysmonsplunklab" EventCode=2 pdf
- As seen below, if we expand that event we can see the time was changed to 2024-01-14 08:10:06.029 and timestomping was the technique used.
- <img width="1383" height="737" alt="image" src="https://github.com/user-attachments/assets/f1f3e3a4-2bf1-4301-b513-68ff1ffd8c37" />








