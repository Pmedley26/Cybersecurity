# Windows Forensics Demonstration


In the following Demonstration, I will be going through how do generate and collect Windows Artifacts from a Vmware VM with Windows Installed on it. I'll be simulating a Script Execution Attack From Atomic Red Team(Blue Cape) and using a tool called gkape to collect the artifacts. There will also be a video demonstration. 

# Who or What is Atomic Red Team?
# https://www.atomicredteam.io/
# Feel free to scan the url using a tool such as urlscan.io as a proactive best security measure!
Atomic Red Team is an open source library used for testing organizational security implementations. The link to their website is above. The script used in this demonstration was prepared by their team. 



<img width="1902" height="878" alt="APT Script" src="https://github.com/user-attachments/assets/7bead671-5326-4b5b-9eb6-7e7cb638527f" />

The image above shows the Atomic Red Team Script in action. The script works by using multiple MITRE Attack TTPs on the VM so that I can investigate and view the system artifacts once the script has completed. 

# Kape Software
The Kape Software is what I'll use to collect and export the Artifact information on the C Drive. gkape is the GUI version of Kape. 

<img width="1703" height="917" alt="gkape" src="https://github.com/user-attachments/assets/9580059e-a575-467d-8eaa-16ee707a3ede" />


# Timestamps
- Timestands are a fundamental yet crucial aspect of Windows Forensics that allows us to establish a timeline on Cybersecurity Incidents. 
- Each File System has its own nuance for how it handles timestamps and updates. 


For the NTFS filesystem, the following rules apply: 
- M = Content Modification
- A = File Access Time
- C = Metadata Change Time(MFT record moditifations)
- B = Birth Time(File Creation Time)



# MFT Record
- MFT stands for Master File Table
- It is a crucial component of NTFS as it serves as a Master Database for all files and directories stored on a windows system that contains an NTFS volume. 

Key Features: 
- MetaData Storage
- Indexing
- Resilience
- Allocation
- MFT Zone
- Alternate Data Streams

<img width="1919" height="1079" alt="Structure of an MFT file record" src="https://github.com/user-attachments/assets/b3764955-e9e5-4098-a1dc-3b967ee30d42" />




- When a file is deleted in NTFS, it's actually the entry in the Master File Table that's deleted from the disk, not the file itself. 
- Each MFT entry is 1024 bits


# Analyzing the MFT in the Home Lab

In the Lab VM, Navigated to EzTools in the vmware VM. 

Ran the following Command: 

MFTECmd.exe -f C:\Users\Elliot\Desktop\Kape_Out\C\$MFT --csv C:\Users\Elliot\Desktop\Case_Files\File_System --csvf MFT.csv

This essentially parses the MFT to make it human readable, otherwise the information wouldn’t be usable. 

<img width="1706" height="873" alt="Creating MFT" src="https://github.com/user-attachments/assets/f546d2dc-58d7-4f9e-94f7-b85774d7c10f" />



# A very useful tool for analyzing MFT files is Timeline Explorer:

<img width="1698" height="868" alt="Timeline Explorer" src="https://github.com/user-attachments/assets/e309df9f-2b30-44dc-b326-eccb657218e5" />


Here, I decided to look for entries pertaining to the powershell script I ran before from Atomic Red Team: 

<img width="1696" height="840" alt="Time Explorer ART Entry" src="https://github.com/user-attachments/assets/189485e7-d406-4c69-a2bd-4c774f7993c2" />


Where this becomes useful is that you can see when files were originally created, deleted, modified, etc. Unfortunately however, removing these entries in the MFT only requires the file to be simply deleted from the system, however, the files can be recovered.

# Another useful tool is the USN Journal($): 
- It keeps track of whenever a file is modified. 
- USN stands for Update Sequence Number. 
- It provides data showing the operations performed on files
- This provides more than just a simple timestamp when the last operation of a given type was performed
- It captures significantly more operation types than just MACB. 
- It may include references to operations on deleted files. 


We can use the same command MFTECmd.exe -f C:\Users\Elliot\Desktop\Kape_Out\C\$MFT --csv C:\Users\Elliot\Desktop\Case_Files\File_System --csvf MFT.csv

To parse the USN Journal as well. There are a couple differences in how we’ll run the command though: 

>MFTECmd.exe -f C:\Users\Elliot\Desktop\Kape_Out\C\$Extend\$J -m  C:\Users\Elliot\Desktop\Kape_out\C\$MFT --csv C:\Users\Elliot\Desktop\Case_Files\File_System --csvf $J.csv

Atomic Red Team has this file called Deleteme_ that I’m going to use to demonstrate the useful information you can find from the $USN journal. 


<img width="1711" height="876" alt="USN Journal" src="https://github.com/user-attachments/assets/9fed1525-b1bd-4c6b-ada6-77835edd1d5f" />



As you can see, it shows 3 different timestamps, but provides update reasons, such as when the file was created, and when the file was deleted. 

Taking Note of the Event ID, i’ll now perform some additional analysis. I’ll use the same command from above, but this time, I’ll filter out by that particular event ID

MFTECmd.exe -f C:\Users\Elliot\Desktop\Kape_Out\C\$MFT —de 334357

In the screenshot below, this tells us that the Event ID for this particular file has already been overwritten in NTFS: 



<img width="1420" height="897" alt="Event 334357" src="https://github.com/user-attachments/assets/b78997d5-7499-410a-8e9a-418a18112c22" />


Next Steps: Windows Registry Artifacts. 

# What is the Windows Registry? 
- You can think of the registry as a database of all user and system configurations. It contains elements called "Hives" and "Keys" that store both user specific and system specific configuration changes.
- This becomes valuable during incident response and forensic investigation

# Lab Tools
- For this section of the Lab I used Registry Explorer and RegRipper
- These tools were very useful for viewing Registry Keys in Human Readable Format for Investigative Purposes.


# Registry Explorer
- This software allows me to load registry Hives into the software both live and offline for further analysis on individual Keys
- I was able to view each of the Hives above and look at the various system and user level keys.
- Below is an image of the default Registry Explorer Interface, and Two Hives Associated with the SOFTWARE and NTUser.DAT Registry Hive

# What is a "Dirty Hive?"
 - A dirty hive is an indication that a hive may not be up to date due to Transaction Logs not being imported over
 - Transaction logs are logs that service as the intermediary between the current state of the Registry Keys and Changes about to be made to them
 - Once imported, the Keys will then be up to date, allowing for more accurate, detailed forensic analysis and investigation.
 - Beyond understanding Operating System Telemetry, Making sure the data you're viewing is as accurate as possible is crucial when dealing with critical assets, especially Insider and Customer Data!
   
<img width="1901" height="897" alt="Registry Explorer" src="https://github.com/user-attachments/assets/de7a1616-bf7e-413d-87cd-08185e4b04f9" />


# RegRipper
- This tool was used to take the output of the initial Registry Hive Telemetry and Convert it into a Human Readable Format.
- I used the CMD tool, but it does have a GUI tool.

  <img width="1908" height="982" alt="RegRipper" src="https://github.com/user-attachments/assets/79cdb525-4c9b-45ed-bae2-749582f62ae5" />
- There isn't much to see in the image above, but the following Command: for /r %i in (*) do (C:\Users\Elliot\Desktop\Tools\RegRipper3.0-master\rip.exe -r %i -a > %i.txt)
- To loop through each of the Registry Hives and used a program called rip.exe to convert them to human readable syntax. 


# Root Keys
There are 5 root keys(Hives) in Windows: 
- HKEY_CURRENT_USER: This key holds settings for the currently logged-on user, such as folder preferences and Control Panel Configurations. 
- HKEY_USERS
- HKEY_LOCAL_MACHINE
- HKEY_CLASSES_ROOT: Merges data from HKEY_Local_Machine\Software\Classes and Hkey_Current_User\Software\Classes for file associations. 
- HKEY_CURRENT_CONFIG


$NTUser.dat and UseClass.dat store files that contain user-specific registry data, including evidence of software use and file access. 


# Windows Run Keys: 
- Another type of registry Key
- One associated with the user, and another with the system. 
- If an entry is placed  in the run key associated with the system, then it will run each time the system is booted. For the user run key, each time the user logs in, the file is going to run
- This leaves concerns about a potential persistence mechanism being left by a threat actor

  Below you'll see an example of a Run Key that was created using the Atomic Red Team Script from Earlier in the Lab. 



<img width="1900" height="1002" alt="NtUser Run Keys" src="https://github.com/user-attachments/assets/f7d17366-8441-4fd7-b9f9-d7769dc5f5e1" />


# Windows Services
- The Script also created and launched a service called "AtomicRedService"
- We can also Use Registry Explorer to Locate this service

<img width="1896" height="1021" alt="Atomic Test Service" src="https://github.com/user-attachments/assets/b92a2be8-c7aa-42bf-a89a-749a76f227b7" />


# Scheduled Task Key
- Found in the Software Hive
- Navigated to SOFTWARE_clean: Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tree
- There is a MITRE attack Technique number of T1053 discovered in the artifacts. This TTP is linked to Scheduled Tasks. 
- https://attack.mitre.org/techniques/T1053/

<img width="1885" height="988" alt="MITRE Attack T1053" src="https://github.com/user-attachments/assets/dc3ae5cd-8de5-4b56-9ade-47585ea31266" />


# Prefetch Files 
- Speeds up the startup of applications
- Cached data related to applications
- They leave timestamps just like any other artifact
- They primarily exist for the experience but can also be abused by attackers. 
- By default on Windows Workstations, Prefetch is installed and accessible under the SYSTEM hive under prefetch parameters

The VM has a built in tool for parsing prefetch artifacts. I'll also be using Timeline Explorer to view the timeline of the prefetch files as well. 

   <img width="1902" height="922" alt="Prefetch Parameters" src="https://github.com/user-attachments/assets/ea94d402-b916-4499-b5cc-769c1e672183" />

- Looking at the Image Below, we get a list of events that took place during the time the attack took place.
- Processes such as Powershell.exe, Whoami.exe, and Hostname.exe are all common initial reconnaissance methods used by attackers to determine what permissions they may already have, and what resources they can immediately exploit, and what the attack surface is your an organization. 

  <img width="1908" height="993" alt="Prefetch artifacts" src="https://github.com/user-attachments/assets/b6150c68-e2b9-4261-8646-481264119d79" />

# Windows User Assist:  
- Tracks the execution of GUI based applications by users
- Offer a timestamped history of user activity
- Can help understand behavior by recording activity within the system. 
- This Artifact is located under HKEY_Current_user\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist
- The example below shows that gkape from earlier and powershell was opened. As a reminder. Gkape is the gui form of kape. 

<img width="1702" height="735" alt="Windows User Assist" src="https://github.com/user-attachments/assets/b43f7647-22fa-4834-bd74-b0afd81be123" />


# RecentDocs
- Tracks user activity by providing insights into files and folders accessed by the user
- Stored in the REGISTRY UNDER SOFTWARE KEYS
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs
- The MRU represents the document order from most recent to least recent. 
- RecentDocs has a limited number of entries, so older entries are overwritten by newer ones as files are accessed. 




# Windows Shell Bags
- A feature used to remember the settings and view preferences for folders. 
- If the original files or folders are no longer on the system, the shell bag information remains. 
- They are a set of registry keys that store information about folders viewed through Windows Explorer. 
- They track the user’s folder preferences, such as view settings, sizes, and positions.
- There is a tool called Shellbags Explorer in the VM that allows me to view this

<img width="1898" height="1071" alt="Shell Bag and Shellbad-Explorer" src="https://github.com/user-attachments/assets/53d3d812-78fd-4193-b3f3-3f012b6b71c4" />





# Background Activity Monitor
- Introduced in Windows 10 version 1803
- Manages activity of background applications
- Records metadata of background application executions
- Stored in a Windows Registry Key under the system’s hive
- It records the path to executables as well as the last run time. 
- BAM data is stored in the Windows Registry under the key:
- HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\bam\\State\\UserSettings\\<User SID>




# Windows ShimCache
- Evidence of program execution even when other logs have been tampered with
- Shimcache is designed to maintain compatibility of older software with newer versions of windows. It tracks executables that have been run on the system, -recording key details such as file paths and timestamps. 
- Found in the SYSTEM Hive
- Shimcache is only written to when the system is shut down. 

- Shim Cache data is stored in the Windows Registry at:
- For Windows XP through Windows 7: HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\Session Manager\\AppCompatCache
- For Windows 8 and later: HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\Session Manager\\AppCompatCache\\AppCompatCache
- We can also use another Eric Zimmerman tool call AppCompatParse. Navigate to where the Zimmerman tools are store and run the following command and it will produce a csv.
- AppCompatCacheParser.exe -f C:\\Users\\Elliot\\Desktop\\Case_Files\\Clean_Registry_Hives\\SYSTEM_clean --csv C:\\Users\\Elliot\\Desktop\\Case_Files\\Execution





# Browser Forensics
- Used gkape to collect browser forensic artifacts as well. 
- Can be used for Evidence Collection, User Activity Analysis, Malware Analysis, Incident Reconstruction, Digital Footprint Analysis, and Legal Compliance and Documentation



# Google Chrome Forensics: 
Chromium browsers store all of the browser data in SQLite Databases
Used a tool called DB browser, which is a SQLite reader


<img width="1906" height="998" alt="DB Browser for SQLite" src="https://github.com/user-attachments/assets/41658195-2264-4860-903a-210eb4d465d8" />


There is another tool that’s used to actually view the last_visit_time in the VM called decode. This allows me to manually copy the timestamp and have it decoded into a more human-readable format. 

More useful information includes the Visits Table and the URLs table, which can give insights as to what links someone accessed, and even the time they visited the link and for how long they visited the link. 


<img width="927" height="475" alt="Decode Software" src="https://github.com/user-attachments/assets/b1e47d0f-10a4-457f-b1f5-7ce94bd36c87" />


# Hindsight: 
- Another forensics tool designed specifically for analyzing web browser history. It is commonly used by digital forensic investigators and security professionals to extract, parse, and analyze historical data from web browsers, including Chrome and Edge. 
- It is a command line tool
- hindsight.exe -i "C:\Users\Elliot\Desktop\Kape_Out\C\Users\Elliot\AppData\Local\Google\Chrome\User Data" -o C:\Users\Elliot\Desktop\Case_Files\WebBrowser\Chrome
Output viewed with EZViewer


# Browsing History View By Nirsoft:
- The last  tool I used for collecting Browser Artifacts, even from multiple users at once.

<img width="1901" height="987" alt="Browsing History View" src="https://github.com/user-attachments/assets/4f84e196-2fd8-4e08-aa82-4c43e95166a2" />



# Next Steps: Memory Analysis 

