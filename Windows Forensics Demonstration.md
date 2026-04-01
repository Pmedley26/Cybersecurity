# Windows Forensics Demonstration


In the following Demonstration, I will be demonstrating how do generate and collect Windows Artifacts from a Vmware VM with Windows Installed on it. I'll be simulating a Script Execution Attack From Atomic Red Team(Blue Cape) and using a tool called gkape to collect the artifacts. There will also be a video demonstration. 

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
Timestands are a fundamental yet crucial aspect of Windows Forensics that allows us to establish a timeline on Cybersecurity Incidents
Each File System has its own nuance for how it handles timestamps and updates. 


For the NTFS filesystem, the following rules apply: 
M = Content Modification
A = File Access Time
C = Metadata Change Time(MFT record moditifations)
B = Birth Time(File Creation Time)



# MFT Record
MFT stands for Master File Table
It is a crucial component of NTFS as it serves as a Master Database for all files and directories stored on a windows system that contains an NTFS volume. 
Key Features: 
- MetaData Storage
- Indexing
- Resilience
- Allocation
- MFT Zone
- Alternate Data Streams

<img width="1919" height="1079" alt="Structure of an MFT file record" src="https://github.com/user-attachments/assets/b3764955-e9e5-4098-a1dc-3b967ee30d42" />




When a file is deleted in NTFS, it's actually the entry in the Master File Table that's deleted from the disk, not the file itself. 
Each MFT entry is 1024 bits
