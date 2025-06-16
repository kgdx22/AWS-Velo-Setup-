# AWS-DFIR-LAB-Setup-

Create secure access to your AWS environment with SSO
<br /> Choose SIFT Workstation Server - t2.medium > Repeat for a Windows Workstation
SIFT Workstation            |  Windows Server 2016 Workstation
:-------------------------:|:-------------------------:
<img width="959" alt="awssiftws" src="https://github.com/user-attachments/assets/fccd4721-bb39-43b8-bff5-8bc81cf55ae6" /> |  <img width="959" alt="awswinws" src="https://github.com/user-attachments/assets/b3b4d380-3c65-4fa8-9e65-1fe8f178c556" />

Create a Private RSA Key Pair for both machines 
SIFT Key                   | Windows Key
:-------------------------:|:-------------------------:
<img width="500" alt="awskey" src="https://github.com/user-attachments/assets/d6efd606-c9dd-46b0-a0fe-000409ede3b8" /> | <img width="500" alt="awswkey" src="https://github.com/user-attachments/assets/15d29295-68f1-43b3-9cbf-af2b5e203327" />

Adjust network settings on the SIFT machine to allow SSH from "My IP" > Adjust network settings on the Windows machine to allow RDP from "My IP"
<br /> Edit inbound Security Group rules > "All TCP" - (Add in a CIDR range that covers the two IPs in your EC2 instances’ VPC)
<br /> Create the necessary inbound rules as shown below allowing SSH/RDP traffic from your local device to both instances 
<br /> **I used two different local machines in this lab, hence the extra ip address for each rule**
SIFT WS Security Group | Windows 2016 WS Security Group
:------------------------:|:---------------------------:
<img width="959" alt="AWSsiftsecg" src="https://github.com/user-attachments/assets/b4c01c5d-edd1-42b2-aee3-644939def448" /> | <img width="959" alt="AWSWindSecGroup" src="https://github.com/user-attachments/assets/82b336de-3ac0-4e97-bf50-e91eb9500bcb" />

A GUI needs to be configured on the SIFT workstation to access GUI-based tools like Autopsy and Velociraptor
<br /> Digital Ocean has a guide on SIFT configuration:
<br /> [![Ubuntu xRDP Tutorial](https://github.com/kgdx22/AWS-Velo-Setup-/blob/main/DO.jpg?raw=true)](https://www.digitalocean.com/community/tutorials/how-to-enable-remote-desktop-protocol-using-xrdp-on-ubuntu-22-04)


**SIFT Setup**: Obtain the Private IP DNS name of the SIFT machine to insert in the Host Name section of PuTTY
![Screenshot 2025-06-04 200649](https://github.com/user-attachments/assets/6b4c65b6-97b3-4e07-8e23-37f404e177fd)
<br /> *Don't forget to allow your local machine ip address through the firewall at the end of setup*
![awsrdpinsta](https://github.com/user-attachments/assets/d9c6a6cb-b8e5-404b-a507-4b02afbf18ae)
You may run into this error where the *sansforensics* account will not load past startup 
![Screenshot 2025-06-04 204135](https://github.com/user-attachments/assets/ab4cfc66-8bf7-4a87-ae8b-f16507ecdcb6)

Head back to PuTTY and create a User account like so:
<img width="907" alt="awsadduser" src="https://github.com/user-attachments/assets/5a61b809-b27c-4117-a12a-1e9a35f42b9c" />

After you have successfully logged in you will be able to obtain the latest Velociraptor download
![Screenshot 2025-06-07 082130](https://github.com/user-attachments/assets/2a0d0cec-f5d3-40a2-8578-73da17e36de3)
Confirm your firewall state and ensure that your local machine and two instances can communicate
Firewall Rule Creation      | Current Firewall State
:--------------------------:|:------------------------:
<img width="700" alt="awsinactfw" src="https://github.com/user-attachments/assets/af4d4110-58f0-4741-8efa-a7ec1aee6ae7" /> | <img width="700" alt="aws2ndufwr" src="https://github.com/user-attachments/assets/259ebd02-c1ee-40d6-9950-c1eb5f71f3c1" />

**EZ Tools Installation** - https://ericzimmerman.github.io/ (Must install at minimum .NET 6.0 prior)
![awseztools](https://github.com/user-attachments/assets/436a67b5-b723-4385-b774-1df6d48ddd2c)
![awsdskrun](https://github.com/user-attachments/assets/68b7947e-aeb5-491f-a84f-f41b30ccaea6)

**These photos show proof of EZTools and Sysmon installation** - Event Viewer/Timeline Explorer
<br /> Sysmon installation command - *sysmon -accepteula -i ./sysmonconfig-export.xml*
![awssysmon](https://github.com/user-attachments/assets/80ea64f6-8203-4147-aec4-79a637a4b483)
![awssysmon2](https://github.com/user-attachments/assets/ac6307e1-8273-4a67-8518-1d98cd3a45b6)

**Velociraptor Configuration** - SIFT = Server|Windows = Client
<br /> Install the latest Velociraptor on SIFT machine
<br /> Chmod the file to enable execution: *chmod +x velociraptor-v0.72.1-linux-amd64*
<br /> Then run the following: *./velociraptor-v0.72.1-linux-amd64 config generate -i*
<br />
<br /> This will initiate a wizard that will walk you through all the steps you need to generate configuration files for both the server and the client.
<img width="959" alt="awsveloset" src="https://github.com/user-attachments/assets/9f50d931-e145-49ae-9d26-f2ac8c2771b1" />
<br /> Copy everything but the Master Frontend. You will need to create a Self Signed SSL for Velociraptor configuration. Here is the detailed guide: https://docs.velociraptor.app/docs/deployment/quickstart/
<br /> As you can see bind addresses will need to be changed to allow for communication to the front end.
<img width="959" alt="awsvelbind" src="https://github.com/user-attachments/assets/8514d39f-7a70-4ac4-b361-e566f5b7b9cc" />
<img width="947" alt="awsvelinsl" src="https://github.com/user-attachments/assets/f52e9226-d716-4eb5-a7cc-4b0ca57fbace" /> 

**Velociraptor Connection** - We have successfully connected to Velociraptor on our Server
![awsvelociraptor](https://github.com/user-attachments/assets/9e1bfe25-1f65-4de9-ae5a-27bc0489cf1c)
![awsvelss](https://github.com/user-attachments/assets/2b82b3f7-8cc0-423b-81a4-0aa8c44a8651)
<br /> Open the server artifacts tab (shown below) and then the plus sign to open the server artifact menu. From there, search for “create” and select “Server.Utils.CreateMSI”.
<br /> After launching and downloading this artifact, we will use Python Simple HTTP to transfer the file to our Windows Client
![awsvelmsi2](https://github.com/user-attachments/assets/a7163700-c718-4e9d-8c43-bfa103292c77)
<br />
**<br /> Below is an extra sshd configuration file in the case that yours is inoperable** 

![awspspe](https://github.com/user-attachments/assets/e74bf3ec-b7be-451d-a0a9-954107b588ea)
<br />
*<br /> Credit: Adam Messer - Medium*









