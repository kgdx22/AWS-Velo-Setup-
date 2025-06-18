# AWS-DFIR-LAB-Setup-
<br />
This lab was built to create a scalable and cloud-based Digital Forensics and Incident Response (DFIR) environment using AWS services. By following *Adam Messer’s* guide, I was able to configure a Windows forensic workstation and deploy Velociraptor for endpoint telemetry and threat hunting. This environment serves as the foundation for simulating realistic attack scenarios, collecting forensic evidence, and developing custom detection capabilities — all within a safe and isolated cloud setting.

### Necessary Tools

- AWS EC2 – Used to provision Windows and Linux instances for DFIR operations.
- Velociraptor – Installed for endpoint visibility, artifact collection, and live threat hunting.
- EZ Tools – A suite of forensic tools used to extract event logs and system data for offline analysis.
- Sysmon – Installed on the Windows instance to generate detailed security logs.
- Amazon S3 – Optional cloud storage for artifacts or logs.
- Windows Server 2016/2019 – Used as the target system for testing and analysis.
- Ubuntu Linux (SIFT or custom) – Used as the analyst workstation for interacting with Velociraptor and hosting files.
- PuTTY – For remote SSH access to Linux systems from a local Windows host.
- Visual Studio Code – To view and analyze logs and configuration files.
  
## Workstation Setup

**Create secure access to your AWS environment with SSO**
- Open the EC2 resource console and select “*Launch Instances*”.
- Choose *SIFT Workstation Server - t2.medium* > Repeat and choose *Windows Server 2016 Workstation*
  
SIFT Workstation            |  Windows Server 2016 Workstation
:-------------------------:|:-------------------------:
<img width="959" alt="awssiftws" src="https://github.com/user-attachments/assets/fccd4721-bb39-43b8-bff5-8bc81cf55ae6" /> |  <img width="959" alt="awswinws" src="https://github.com/user-attachments/assets/b3b4d380-3c65-4fa8-9e65-1fe8f178c556" />

**Create a Private RSA Key Pair for both machines (Select .ppk for PuTTY use)**
SIFT Key                   | Windows Key
:-------------------------:|:-------------------------:
<img width="500" alt="awskey" src="https://github.com/user-attachments/assets/d6efd606-c9dd-46b0-a0fe-000409ede3b8" /> | <img width="500" alt="awswkey" src="https://github.com/user-attachments/assets/15d29295-68f1-43b3-9cbf-af2b5e203327" />

### Network
- Adjust network settings on the SIFT machine to allow SSH from "My IP"
- Adjust network settings on the Windows machine to allow RDP from "My IP"
- Edit inbound Security Group rules > "All TCP" - (Add in a CIDR range that covers the two IPs in your EC2 instances’ VPC)
- Create the necessary inbound rules as shown below allowing SSH/RDP traffic from your local device to both instances 
<br /> ***I used two different local machines in this lab, hence the extra ip address for each rule***

SIFT WS Security Group | Windows 2016 WS Security Group
:------------------------:|:---------------------------:
<img width="959" alt="AWSsiftsecg" src="https://github.com/user-attachments/assets/b4c01c5d-edd1-42b2-aee3-644939def448" /> | <img width="959" alt="AWSWindSecGroup" src="https://github.com/user-attachments/assets/82b336de-3ac0-4e97-bf50-e91eb9500bcb" />

*A GUI needs to be configured on the SIFT workstation to access GUI-based tools like Autopsy and Velociraptor*
<br /> Digital Ocean has a guide on SIFT configuration:
<br /> [![Ubuntu xRDP Tutorial](https://github.com/kgdx22/AWS-Velo-Setup-/blob/main/DO.jpg?raw=true)](https://www.digitalocean.com/community/tutorials/how-to-enable-remote-desktop-protocol-using-xrdp-on-ubuntu-22-04)


## SIFT Setup 
<br /> Obtain the Private IP DNS name of the SIFT machine from the EC2 resource console to insert in the Host Name section of PuTTY
![Screenshot 2025-06-04 200649](https://github.com/user-attachments/assets/6b4c65b6-97b3-4e07-8e23-37f404e177fd)

***<br />Allow your local machine ip address through the firewall at the end of setup if you want to get back in!!***
![awsrdpinsta](https://github.com/user-attachments/assets/9709e26a-83c6-4c6d-b1e9-fefbf4412c3e)

***You may run into this error where the *sansforensics* account will not load past startup**
![Screenshot 2025-06-04 204135](https://github.com/user-attachments/assets/ab4cfc66-8bf7-4a87-ae8b-f16507ecdcb6)

Head back to PuTTY and create a *User* account like so:
<img width="907" alt="awsadduser" src="https://github.com/user-attachments/assets/5a61b809-b27c-4117-a12a-1e9a35f42b9c" />
<br />
After you have successfully logged in you will be able to obtain the latest Velociraptor download
![Screenshot 2025-06-07 082130](https://github.com/user-attachments/assets/2a0d0cec-f5d3-40a2-8578-73da17e36de3)
Confirm your firewall state and ensure that your local machine and two instances can communicate

| Firewall Rule Creation | Current Firewall State |
|:----------------------:|:----------------------:|
| <img width="450" alt="awsinactfw" src="https://github.com/user-attachments/assets/b7fd8ff5-0b3a-4af7-a4a5-44d18b6a1aad" /> | <img width="450" alt="aws2ndufwr" src="https://github.com/user-attachments/assets/4956e477-8276-4600-ad4e-300cbeaa4ffc" /> |



## EZ Tools Installation
<br /> Now we will install Eric Zimmerman's Tools for Digital Forensics and Windows artifact analysis 
 - https://ericzimmerman.github.io/ (Must install at minimum .NET 6.0 prior)

![awsdskrun](https://github.com/user-attachments/assets/68b7947e-aeb5-491f-a84f-f41b30ccaea6)
![awseztools](https://github.com/user-attachments/assets/436a67b5-b723-4385-b774-1df6d48ddd2c)


**Check out your EZ Tools Installation** - Event Viewer/Timeline Explorer
***<br /> Sysmon installation command -***
> sysmon -accepteula -i ./sysmonconfig-export.xml

![awssysmon](https://github.com/user-attachments/assets/80ea64f6-8203-4147-aec4-79a637a4b483)
![awssysmon2](https://github.com/user-attachments/assets/ac6307e1-8273-4a67-8518-1d98cd3a45b6)

## Velociraptor Configuration
<br /> SIFT = Server | Windows = Client
- Install the latest Velociraptor on SIFT machine
- Chmod the file to enable execution:
> chmod +x velociraptor-v0.72.1-linux-amd64

<br /> Then run the following:
> ./velociraptor-v0.72.1-linux-amd64 config generate -i
<br />
<br /> This will initiate a wizard that will walk you through all the steps you need to generate configuration files for both the server and the client.
<img width="959" alt="awsveloset" src="https://github.com/user-attachments/assets/9f50d931-e145-49ae-9d26-f2ac8c2771b1" />

<br />**Copy everything but the *Master Frontend*. You will need to create a Self Signed SSL for configuration**
<br /> Here is the detailed guide: https://docs.velociraptor.app/docs/deployment/quickstart/

<br /> As you can see bind addresses will need to be changed to allow for communication to the front end.

<img width="959" alt="awsvelbind" src="https://github.com/user-attachments/assets/8514d39f-7a70-4ac4-b361-e566f5b7b9cc" />
<img width="947" alt="awsvelinsl" src="https://github.com/user-attachments/assets/f52e9226-d716-4eb5-a7cc-4b0ca57fbace" /> 

**Velociraptor Connection** - We have successfully connected to Velociraptor on our Server
![awsvelociraptor](https://github.com/user-attachments/assets/9e1bfe25-1f65-4de9-ae5a-27bc0489cf1c)
![awsvelss](https://github.com/user-attachments/assets/2b82b3f7-8cc0-423b-81a4-0aa8c44a8651)
- Open the server artifacts tab (shown below)
- Then the plus sign to open the server artifact menu.
<br />From there, search for “create” and select *“Server.Utils.CreateMSI”*.

<br /> After launching and downloading this artifact, we will use Python Simple HTTP to transfer the file to our Windows Client

![awsvelmsi2](https://github.com/user-attachments/assets/a7163700-c718-4e9d-8c43-bfa103292c77)
<br />

**<br /> Below is an extra sshd configuration file in the case that yours is inoperable** 

![awspspe](https://github.com/user-attachments/assets/e74bf3ec-b7be-451d-a0a9-954107b588ea)
<br />
*<br /> Credit: Adam Messer - Medium*









