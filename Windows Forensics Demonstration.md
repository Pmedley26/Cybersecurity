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



