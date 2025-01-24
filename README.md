<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-prmises Active Directory within Azure Virtual Machines. <br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

  <h2>Operating Systems Used </h2>

  - Windows Server 2022
  - Windows 10 (21H2)
 
    <h2>High-Level Deployment and Configuration Steps<h2>

  - Step 1: Setup Resources in Azure
  - Step 2: Ensure Connectivity between the Client and Domain Controller
  - Step 3: Install Active Directory
  - Step 4: Create an Admin and Normal User Account in Active Directory
  - Step 5: Join Client-1 to your domain (mydomain.com)
  - Step 6: Setup Remote Desktop for non-administrative users on Client-1
  - Step 7: Create a bunch of additional user to attempt to log into Client-1 with one of the users
 
  <h2>Deployment and Configuration Steps<h2><

  1. Steup Resources in Azure
     - Create virtual machines in Azure (Note: when you create the virtual machine, it will automatically create a resource group and virtual network for you)
     - Create the Domain Controller VM and name it 'DC-1'
         - Name your resource group 'AD-Lab' by selecting 'Create New' and select 'OK'
         - Enter DC-1 in the 'Virtual machine name' field
         - Select US West 2 or 3 region (whichever works for you)
         - Ensure the 'Availability options' field is 'No infrastructure redundancy required'
         - Ensure the 'Security type' field is 'Standard'
         - Select Windows Server 2022 in the 'Image' field
         - Select at least 2 vcpus for the 'Size' field
         - Create your own user name and password
         - Select 'Review + create -> then create
      
     - Create the Client-1 VM and name it 'Client-1'
          - Select 'AD-Lab' as the resource group
          - Enter Client-1 in the 'Virtual machine name' field
          - Select US West 2 or 3 region  (whichever works for you)
          - Ensure the 'Availability opgtions' field is 'No infratsructure redundancy required'
          - Ensure the 'Security type' field is 'Standard'
          - Select Windows 10 Pro in the 'Image' field
          - Select at least 2 vcpus for the 'Size' field
          - Create your own user name and password
          - Check the 'Licensing' box
          - Click 'Next' to get to the 'Networking tab'
          - Select the same Virtual network as the DC-1 VM (Note: if you do not do this step, you will not be able to connect to the Domain Controller)
      
              *Note: Give DC-1 at least 10 minutes to completely deploy in order to select same virtual network
          - Select 'Review + create -> then Create
      

![image](https://github.com/user-attachments/assets/c5bc243c-1028-4110-a106-4aea023d4e6f)
![image](https://github.com/user-attachments/assets/2e65c3f8-93ce-4a68-ad03-a0c06eca6d02)
![image](https://github.com/user-attachments/assets/36d40345-1f0a-4c2c-b7e6-6580b283eb4a)
![image](https://github.com/user-attachments/assets/6962c21b-5f9d-4cfc-a0b2-66b888d36196)

 - Set Domain Controller's NIC Private IP address bto be static
  - Go to Virtual Machines
  - Select DC-1
  - Select 'Networking' -> 'Network Settings'
  - Click on the 'Network Interface' (NIC) hyperlink
  - Click "IP Configurations'
  - Click the 'ipconfig1' hyperlink to change the Private IP Address from 'Dynamic' to 'Static'
  - Select 'Static' and Save


![image](https://github.com/user-attachments/assets/5dd7e72d-6e50-422e-999e-ccd6c09abe0e)
![image](https://github.com/user-attachments/assets/93607e80-d8f4-48eb-b794-e3ae71709cdc)
![image](https://github.com/user-attachments/assets/85868360-1eda-470b-8fdb-d912f4630858)


2. Ensure Connectivity between the Client and Domain Controller

- Log into Client-1 and ping DC-1'S IP address with 'ping -t' (perpetual ping)
   - Connect and log in with credentials created in Azure
   - Copy the Domain Controllers Private IP Address -> Azure -> Virtual Machines -> DC-1 -> scroll until you see 'Networking' -> copy the Private IP Address
   - Go to 'Start' and open the 'Command Prompt' app
   - Ping the DC'S Private IP address
   - You'll notice the request will time out due to DC-1'S Windows Firewall blocking ICMPv4 traffic
-  Minimize Client-1 VM and log into the Domain Controller (DC-1) via Remote Desktop Connection and enable ICMPv4 on the local Windows Firewall
   - Click start and open 'Server Manager' if it has not opened automatically after logging in
   - Click start to open up the Windows Firewall
        - Click Inbound rules (maximize the Firewall)
        - Click 'Protocol'
        - Enable ICMPv4 (select line, right click -> enable)
        - Minimize DC-1
    - Check back at Client-1 to see ping succeed a.k. 'Reply'
        -Stop the ping by hitting 'crtl +c' on your keyboard


      ![image](https://github.com/user-attachments/assets/a0dccba9-6e45-4360-8d40-a9781edca290)
      ![image](https://github.com/user-attachments/assets/cc44493d-4f08-4635-949a-67a8c05d2659)
      ![image](https://github.com/user-attachments/assets/4ba0fd96-95b6-47df-890a-deccd80f90a2)
      ![image](https://github.com/user-attachments/assets/ae62f8e2-6283-496b-9a52-2dc584a79254)




3. Install Active Directory

   - Go to DC-1 VM
   - Open Server Manager (if it is not already opem, click the start menu and select 'Server Manager')
   - Open Server Manager is open, click 'Add roles and features
   - Click 'Next' until you reach 'Server Roles' -> select 'Active Dierectory Domain Services' -> you'll get a pop up to 'Add Features' (click that)
   - Click 'Next' through all of the dependencies and select Install -> Click 'Close' once the install finishes
   - After the install, go back to Server Manager and click on the yellow caution icon -> click on 'Promote this server to domain controller'
   - Once 'Deployment Configuration' opens click on 'Add new forest' -> name the domain in the 'Root domain name' field (create any name) -> click 'Next" -> create your own password -> click 'Next' until you reach 'Install'
   - After the installation you'll automatically be kicked out of DC-1 VM
   - Log back into DC-1 VM using your original credentials + Root Domain Name created when installing AD (Reason bring is, we have now turned this VM into a Domain Controller)
  
![image](https://github.com/user-attachments/assets/62bd70d2-3e16-4c62-903f-60507770e271)
![image](https://github.com/user-attachments/assets/8cc3fa93-78fc-4316-a5d5-3526d4e52612)
![image](https://github.com/user-attachments/assets/2631a51d-33c4-4396-97fa-e4afa365a2da)
![image](https://github.com/user-attachments/assets/f12c6742-fd2e-4f12-8b7a-8f08662d8990)
![image](https://github.com/user-attachments/assets/19446fe7-d96a-41e3-81e9-bd54d27d7014)
![image](https://github.com/user-attachments/assets/1369b009-d003-4714-bef5-7aad346f58bf)
![image](https://github.com/user-attachments/assets/fc9cd921-1c27-40fe-8a46-c52b2d620ec3)


4. Create an Admin and Normal User Account in AD

   - Once logged back into DC-1 VM, open Active Directory
       - Go to tools within 'Server Manager' -> click Active Directory Users and Computers
       - Create a couple of Organizational Units (OU) [think of them like folders]
          - Right click 'myhdomain.com' -> go to 'New' -> click 'Ok'
          - In the 'Name' field, enter _EMPLOYEES -> click 'Ok'
          - Repeat steps and name another Organizational Unit _ADMINS
    - Right click 'mydomain.com' and click refresh to view the OU's created
    - Create an administrative account
        - Go to '_Admins' and right click -> go to 'New' -> select 'User'
        - Create your own credentials -> click 'Nexr -> create your own password -> uncheck the box that says 'User must change password at next logon' -> 'Next' and then 'Finish'
        - After the user is created, right click the user's name -> go to 'Properties' -> click the 'Mmeber of' tab -> click 'Add' -> enter 'domain' in the empty field -> click 'Check Names' -> join the 'Domain Admins' group -> click 'Ok' and apply
     

  - Log off of DC-1 VM and log back in using the admin credentials we just created
     - Go to start and open the 'Command Prompt' app -> type 'logoff'
     - Log back in using admin credentials
     - Click start and open the 'Command Prompt' app -> 'whoami' and you'll see you're now logged in as jane_admin in DC-1 VM
   

![image](https://github.com/user-attachments/assets/15cd8e78-8985-4e4c-bf9c-be950998c098)
![image](https://github.com/user-attachments/assets/45ab2fc1-8cc3-4329-935f-de233f4e14f8)
![image](https://github.com/user-attachments/assets/c38f75ae-5c0b-4874-b953-32eb120a6372)
![image](https://github.com/user-attachments/assets/14aae951-904e-4022-9924-6f7418a4277a)
![image](https://github.com/user-attachments/assets/ab9cd843-8555-4dff-adcc-1704554f2cd2)
![image](https://github.com/user-attachments/assets/ba3ad8c6-8206-4977-bf92-2e7be2a1ed19)
![image](https://github.com/user-attachments/assets/4a8528ef-fa15-4315-a2f6-61d35129b03b)
![image](https://github.com/user-attachments/assets/d2f5a00c-baa1-418c-a106-8117241f0223)
![image](https://github.com/user-attachments/assets/659c7700-1719-4151-8424-f7c00697a4d8)



5. Join Client-1 to your domain (mydomain.com)

   - From the Azure Portal, set Client-1's VM DNS settingd to the Private IP address
      - Get DC-1 Private IP Address in Azure
      - Go to Azure -> Virtual Machines -> Client-1 -> click on Network -> click on Networksettings -> click on the blue hyperlink under NIC that says 'client-1852' -> go to 'DNS Servers'
   - Restart Client-1 VM in Azure
      - Go to Virtual Machines -> Client-1 -> click 'Restart'
   - Log into Client-1 as the original local admin and join it to the domain (computer will restart)
        - Once logged in, right click the start menu -> go to System -> click 'Rename this PC (advanced) on the right -> click 'Change' -> select 'Domain' -> enter your Domain Name -> click 'Ok' -> enter the admins credentials 'mydomain.com\jane_admin' -> click 'Ok' -> a window will pop up asking to restart the computer, click 'Restart now' (if not, go to the start menu and click 'Restart' to force it, log back into Client-1 VM w/ thge admin credentials)
   - You have now joined Client-1 to the Domain successfully
  
  6. Setup Remote Desktop  for non-administrative users on Client-1

     - Log into Client-1 as 'mydomain.com\jane_admin' -> right click the start menu -> click 'System' -> select 'Remote Desktop' on the right -> scroll down and click 'Select users that acn remotely access this PC' -> click 'Add' -> enter 'domain user' in the empty box field -> click 'Check Names' -> click 'Ok'
     - Minimize Client-1
     - Open up DC-1 VM -> open up Active Directory -> go to 'mydomain.com' -> click the drop down -> click 'Users' -> you should see 'Domain Users' added -> double click 'Domain User's -> select the 'Members' tab to view the users of this sec urity group that are allowed to remotely log into Client-1
    


![image](https://github.com/user-attachments/assets/6b23c964-a901-47f0-b96b-481bbe00c7a9)
![image](https://github.com/user-attachments/assets/d5f3f2a6-33b3-4527-b4a7-9cf8ecb8ce46)
![image](https://github.com/user-attachments/assets/932c3c64-9416-42ef-ae7e-fea3ace53fad)



7. Create a bunch of additional users and attempt to log into Client-1 with one of the users

   - Open up DC-1 and log in as 'mydomain.com\jane_admin'
   - Click the start menu and open 'Windows Powershell ISE' (right click to 'Run as administrator') -> click 'Yes' for the pop up
   - Click on the blank paper icon to create a new file
   - Paste your script under the 'Untitled' tab
   - Run the script by click the green play icon at the top
   - You'll start to see random user names being created
   - Minimize Powershell
   - Go back into Active Directory -> click on 'mydomain.com' -> click'mydomain.com' -> click _EMPLOYEES' -> you should see all the users that) are being created from PowerSXhell ISE in this folder (if you do not see them, right click '_EMPLOYEES' and click 'Refresh')
   - Double click on a random user name from the 'EMPLOYEES' folder (pick on easy username)
   - Copy the 'Display name' also known as the 'Account Name'
   - Minimize DC-1
   - Log otu of Client-1 and log back in with the users credentials we copied (in this case: 'mydomain.com\ban.jot)
      *Note: The password for all these users is 'Password1' per the script we pasted into PowerShell ISE
   - Open the command line and type 'whoami' to view user name -> type 'hostname' to view the host you're logged into


  ![image](https://github.com/user-attachments/assets/c7971f26-2564-42b0-b0ee-350f904b2692)
  ![image](https://github.com/user-attachments/assets/9feb9f8a-e144-4e25-900e-0cea2e074fba)
  ![image](https://github.com/user-attachments/assets/0f35a0e6-477e-42ab-90a5-e7e91e739404)
  ![image](https://github.com/user-attachments/assets/532f55f3-05e8-4e66-95b5-ed5219b0ebdf)
  ![image](https://github.com/user-attachments/assets/edd1635e-cbff-436d-9b88-2f8dd23d98f0)
  ![image](https://github.com/user-attachments/assets/880ff555-34d6-4368-8259-8f6344c6d711)
  ![image](https://github.com/user-attachments/assets/eec5529e-e5a0-410c-9c1c-cb6cf5f78ded)
  ![image](https://github.com/user-attachments/assets/4f5d9ed7-96e2-49fc-98cd-3e7a505946e2)

  You have successfully configured Active Directory!
























    











     







