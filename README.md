# Active Directory Project
### This project simulates a basic Windows Server Active Directory domain with users in a virtualized environment using VirtualBox. This lab builds foundational skills in Windows Server, Active Directory, networking, and client domain integration.
<br>

<h2>Network Diagram:</h2>

![network diagram](https://github.com/ecankaya1/ActiveDirectoryLab/blob/bce32288a571cef22219bc40b103f5ed5e003760/Images/Network%20Diagram.png)

## Objective
This project was created to simulate a basic enterprise Active Directory environment using VirtualBox. The goal was to deepen my understanding of Windows Server infrastructure, including domain controller setup, DHCP, DNS, RAS/NAT, and client domain integration. By building this lab from the ground up, I aimed to gain hands-on experience with core systems administration tasks and foundational networking concepts, which are critical for roles in IT. <br>

## Skills Learned
- Setting up VMs in Oracle VM VirtualBox.<br>
- Active Directory Domain Services (AD DS): Installed and configured a domain controller and promoted it within a new forest. <br>
- DHCP: Deployed and configured DHCP to automatically assign IP addresses to client machines. <br>
- DNS Configuration: Set up local DNS to support domain functionality and internal name resolution. <br>
- RAS/NAT Setup: Enabled internet access for internal network clients by configuring Routing and Remote Access. <br>
- PowerShell: Automated user account creation. <br>
- Troubleshooting & Testing: Verified client IP assignment, name resolution, and domain login functionality. <br>

## Tools Used
- Oracle VM VirtualBox: Hosted virtual machines for the lab environment. <br>
- Windows Server 2019:	Installed and configured as the Domain Controller (DC). <br>
- Windows 10 Pro:	Used as the client machine to join the domain. <br>
- Active Directory Domain Services (AD DS):	Created and managed the domain, users, and groups. <br>
- DHCP (Dynamic Host Configuration Port):	Automatically assign IP addresses to clients. <br>
- DNS: Resolved domain and client names within the network. <br>
- Routing and Remote Access: Enabled internet access for internal network clients. <br>  
- PowerShell: Script for the creation of users. <br>
- Command Prompt (CMD): Used to verify IP configurations. <br>

# Steps

## VM Installation

### *1. Install Oracle VM VirtualBox*

Navigate to https://www.virtualbox.org/wiki/Downloads, download the compatible version along with downloading the VirtualBox extension pack. <br>

### *2. Install Windows 10 ISO*

Navigate to https://www.microsoft.com/en-us/software-download/windows10, and install. <br>

### *3. Install Windows Server 2019 ISO*

Navigate to https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019, and install the ISO 64-bit edition. <br>

## Setting up the Domain Controller (DC)

- Open VirtualBox <br>
- Select NEW <br>
- Name it 'DC' <br>
- In 'version' drop down select 'Other Windows (64-bit)' <br>
- Assign it the appropriate amount of RAM & CPU Cores depending on your hardware. <br>
- In settings for the DC VM (Virtual Machine) set 'Shared Clipboard' and 'Drag'n'Drop' both to bidirectional (this allows for you to copy and paste/drag and drop of files/text from your machine into the VM & vice versa). <br>

![DC VM bidirectional](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/DC%20VM%20Settings.png)

- In settings (make sure you're in the expert tab) go to network and set Adaptor 1 to 'NAT' & Adaptor 2 to 'Internal Network' (Following the network diagram Adaptor 1 will allow the DC connection to the internet via your home router and Adaptor 2 will connect to the internal network)

![Adaptor 1](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/DC%20Nic%201.png)

![Adaptor 2](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/DC%20Nic%202.png)

-The DC VM config should look like this: <br>

![DC VM config](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/DC%20Virtualbox%20Config.png)

## Install VM Guest Additions

- Power on the DC VM and when prompted open the Windows Server 2019 ISO which was installed. <br>
- Continue through the setup. <br>
- When you arrive on the desktop click the 'devices' drop down at the top of the screen. <br>
- Click 'Insert Guest Additions CD Image'. <br>
- Navigate to file explorer on the VM > This PC > CD Drive (D:) VirtualBox Guest Additions. <br>
- Run the 'VBoxWindowsAdditions-amd64' and go through the wizard and reboot the VM. <br>
- (This will allow for a smoother experience within the VM). <br>

## Setting IP Address for the DC

- Go into Network Settings on the PC <br>
- Click 'Change Adaptor Options'  <br>
- There will be 2 Ethernets connected, rename 1 to 'internet' and the other to 'internal' (to know which is which, the internal adaptor will have a link-local address assigned (169.254.X.X) this can be checked by right clicking on an adaptor and clicking status) <br>

![DC Network Connections](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/DC%20Network%20Connections.png)

- Go into properties of the Internal adaptor. <br>
- Then go into properties of IPv4. <br>
- Manually assign the IP address: 172.16.0.1 Subnet Mask: 255.255.255.0 (Don't put anything in default gateway as the DC itself will serve as the default gateway as it has 2 NIC's, 1 on the internet & 1 on the inside so this NIC (internal) will not need a default gateway). <br>
- Manually assign preferred DNS server address to 172.16.0.1 (When Active Directory is installed it automatically installs DNS so this server will use itself as the DNS server). <br>

![DC Internal IP config](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/IP%20addr.%20config%20internal.png)

## Rename the PC

- Right click start menu and go into system. <br>
- Click 'Rename this PC' <br>
- Name it 'DC' (DC stands for Domain Controller). <br>
- Click restart now <br>

## Installing AD DS (Active Directory Domain Services)

- In the Server Manager click 'Add roles and features'. <br>
- In the wizard in Server Roles, select 'Active Directory Domain Services' and add features. <br>
- Go through the wizard to install. <br>
- In the server manager click the flag at the top right and click 'Promote this server to a domain controller'. <br>
- In the wizard, select 'Add a new forest' and add a Root Domain Name e.g 'mydomain.com'. <br>
- Go through the wizard, add a password of your choosing, then continue through the wizard to install. <br>
- The PC will now restart. <br>
- (On the log in screen after restart it will now say 'MYDOMAIN\Administrator' instead of just 'Administrator' that's how you will know it worked). <br>

## Creating a dedicated domain admin 

- Go to start > Administrative Tools > Active Directory Users and Computers. <br>
- Right click on 'mydomain.com' > New > Organisational Unit (OU). <br>
- Name this OU 'ADMINS'. <br>
- Right click on ADMINS > New > User. <br>
- Fill out the info and assign it a user logon name e.g. 'a-ecankaya'. <br>
- Assign a password of your choosing (uncheck 'User must change password at next logon' and check 'Password never expires' for ease of life in the lab environment). <br>

![Dedicated admin](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/Dedicated%20Admin%20Account.png)

- Right click on the user that has been created > properties. <br>
- Go into the 'member of' tab > click add > type in 'Domain Admins' and click check name > click ok and apply. <br>

![Dedicated admin member of](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/Dedicated%20Admin%20Account%20Member%20Of.png)

- You can now log off and log in as your dedicated domain admin account. <br>

## Install RAS/NAT (Remote Access Server/Network Address Translation)

- This is installed so when the Windows 10 Client is created, it's going to allow the client to be in a private virtual network but still be able to access the internet through the DC. <br>
- In server manager go click 'Add roles and features'. <br>
- Click next until Server Roles and select 'Remote Access'. <br>
- Click next until Roles Services and select 'Routing' and add features ('DirectAccess and VPN(RAS) will automatically be selected when Routing is selected, don't unselect it). <br>
- Click next through to install. <br>
- When installed, in server manager select tools at the top left > Routing and remote access. <br>
- Right click 'DC(local)' > Configure and Enable Routing and Remote Access. <br>
- Select 'Network address translation (NAT)'
- Click next and select 'Use this public interface to connect to the internet' (if this option is greyed out as shown in the image below, just close all the routing and remote access windows and go back to it through tools in server manager and follow the steps again). <br>

![NAT select issue](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/NAT%20install%20issue.png)

- Select the adaptor that is labelled internet. <br>

![NAT select](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/NAT%20install%20internet%20selection.png)

- Click next on through to finish. <br>
- Within the Routing and Remote Access, 'DC(local)' should now have a green arrow. <br>

## Setting up a DHCP server on the DC

- In server manager click 'Add roles and features'. <br>
- Click next to Server Roles then select 'DHCP Server' and features. <br>
- Click next through to install. <br>
- When installed, click tools at the top right of server manager > DHCP. <br>
- Expand the domain and right click on 'IPv4' > New scope. <br>
- Name it e.g. 172.16.0.100-200 (I use this name as it will be scope range) then click next. <br>
- In accordance with the network diagram the start IP address will be: 172.16.0.100 and end IP address: 172.16.0.200 <br>
- Set the length to 24 which will give a subnet mask of 255.255.255.0 <br>

![DHCP scope](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/DHCP%20range.png)

- Click next, no need to add any exclusions unless you want to, the click next. <br>
- For lease duration set the amount of time you want a client to hold a specific IP address from DHCP. <br>
- Click next, select 'Yes, I want to configure these options'. <br>
- For the default gateway set the IP address of the DC: 172.16.0.1 (Make sure to click add or this will cause problems later on when connecting clients). <br>
- Click next, for Parent domain type: mydomain.com (the DC is DNS server). <br>
- Click next, ignore WINS, click next again and select 'Yes, I want to activate this scope now'. <br>
- Click next through to finish. <br>
- Right click the domain in DHCP > click 'Authorize' > then right click the domain again and click refresh. <br>
- Expand IPv4 and the scope will be there. <br>

![DHCP scope range](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/DHCP%20scope%20range.png)

## Using PowerShell to create multiple users

- This script is ran to simulate a corporate network full of user and is quicker than manually making new users. <br>
- Copy the AD_PS-Master folder from here https://github.com/ecankaya1/ActiveDirectoryLab/tree/main/PowerShell%20Script on to the desktop of the DC VM. <br>
- When copied into the DC VM, open the names.txt file and add your name to the top. <br>
- Open up start > Windows PowerShell folder > right click on 'Windows PowerShell ISE' and run as an administrator. <br>
- In the toolbar click open script > navigate to desktop and open the AD_PS-Master folder then open '1_CREATE_USERS'. <br>
- Before anything can be done we have to enable the execution of any scripts (Do not do this in real life as this is a security feature). <br>
- In the command line in PowerShell ISE type 'Set-ExecutionPolicy Unrestricted' hit enter and click yes to all. <br>
- In the command line you want to change the directory where the AD_PS-Master folder is saved, type 'cd C:\Users\a-ecankaya\Desktop\AD_PS-Master', hit enter. <br>
- If you type in 'ls' and hit enter you will see the 'names.txt' file is listed. <br>
- In the toolbar, click play, this will run the script and lots of users will be created. (the script will set all the users usernames to their first initial of the first name and last name e.g. 'ecankaya' and the passwords to 'Password1') <br>

![PS Script](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/PS%20Users%20Script.png)

- When the script has finished running, navigate into Active Directory Users and Computers. <br>
- In _USERS, you will see lots of user has been created. <br>

![AD Users](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/AD%20Users.png)

## Creating Windows 10 Client

- In VirtualBox create a new VM and name it 'CLIENT1'. <br>
- In the version dropdown select 'Windows 10 (64-bit)'. <br>
- Assign it RAM & CPU cores depending on your hardware. <br>
- In settings set both 'Shared Clipboard' and 'Drag'n'Drop' to bidirectional like you did when setting up the windows server in VirtualBox. <br>
- In settings for adaptor 1, select 'Internal Network' this is so it can get assigned an address by DHCP. <br>
- Power on the client VM and select the windows 10 ISO which was installed earlier. <br>
- Go through the set up for windows, select that you don't have a product key. <br>
- For the operating system select 'Windows 10 Pro' (Don't select Windows 10 Home as this won't allow you to connect to the domain. <br>
- Continue through the setup until install. <br>
- When installed go through the user config, if it asks, say that you don't have internet and continue with limited setup. <br>
- When you arrive on the 'Who's going to use this PC?' page, type 'user' and for password just click next. <br>
- When you arrive on the desktop, open up Command Prompt (CMD). <br>
- In the command line type 'ipconfig'. (In here you will see that it has been assigned an IP address and a default gateway by the DHCP server. <br>
- In the command line type 'ping mydomain.com' (This will show it has connection to the DC). <br>

![Client IPCONFIG](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/Client%20ipconfig.png)

- In the command line again, ping something on the internet e.g. 'ping www.google.com' (This will show the DNS server is working). <br>

![Client IPCONFIG ping internet](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/client%20internet%20ping.png)

## Joining the domain on the client VM

- In the client VM, right click start > system. <br>
- Click 'Rename this PC (advanced)' (Don't click 'Rename this PC'). <br>
- Click Change. <br>
- In computer name type 'CLIENT1'. <br>
- Select Domain in Member of and type in 'mydomain.com'. <br>

![Client domain](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/Client%20Rename%20%26%20Domain%20Join.png)

- Click ok and type in the dedicated admin account created earlier and restart the PC. <br>

- Whilst the client is restarting, go back into the DC VM. <br>
- Open DHCP and expand the scope that was created. <br>
- In address leases you will see there a lease from the client computer. <br>

![Client DHCP lease](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/DHCP%20Address%20Lease.png)

- Open Active Directory User and Computers. <br>
- Navigate to computers and you will see the client computer that was created in here. This means the computer is now a part of the domain. <br>

![AD client computer](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/AD%20Computers%20OU.png)

- Open the client VM as it should have finished restarting. <br>
- Click log in as other user. <br>
- Log in as the yourself (from when you added your name to the top of the PowerShell script) e.g. username will be 'ecankaya' and the password will be 'Password1' <br>
- When logged in, naviage to Command Prompt (CMD) and in the command line type 'whoami'. <br>

![whoami](https://github.com/ecankaya1/ActiveDirectoryLab/blob/main/Images/Signing%20In%20User%20On%20Client.png)

- The Active Directory Lab will now be successfully setup to simulate a small corporate network. <br>













  










