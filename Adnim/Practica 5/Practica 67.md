**CCNA Security**

**Lab - Securing the Router for Administrative Access**

**Topology**
![[Screenshot 2026-09-18 at 12.24.54 p.m..png]]

 **Nota: En las direcciones de las PC, utilizar las direcciones de la tabla.**

**IP Addressing Table**

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**Device**|**Interface**|**IP Address**|**Subnet Mask**|**Default Gateway**|**Switch Port**|
|R1|G0/1|192.168.48.1|255.255.255.0|N/A|S1 F0/5|
|S0/0/0 (DCE)|10.1.1.1|255.255.255.252|N/A|N/A|
|R2|S0/0/0|10.1.1.2|255.255.255.252|N/A|N/A|
|S0/0/1 (DCE)|10.2.2.2|255.255.255.252|N/A|N/A|
|R3|G0/1|192.168.131.1|255.255.255.0|N/A|S3 F0/5|
|S0/0/1|10.2.2.1|255.255.255.252|N/A|N/A|
|PC-A|NIC|192.168.48.3|255.255.255.0|192.168.48.1|S1 F0/6|
|PC-C|NIC|192.168.131.3|255.255.255.0|192.168.131.1|S3 F0/18|

**Objectives**

**Part 1: Configure Basic Device Settings**

•       Configure basic IP addressing for routers and PCs.

•       Configure OSPF routing.

•       Configure PC hosts.

•       Verify connectivity between hosts and routers.

---

R1
```bash
en
conf t
int g0/1
no sh

```
R2
```bash
en
conf t
int g0/1
no sh

```
R3
```bash
en
conf t
int g0/1
no sh

```

**Part 2: Control Administrative Access for Routers**

•       Configure and encrypt all passwords.

•       Configure a login warning banner.

•       Configure enhanced username password security.

•       Configure an SSH server on a router.

•       Configure an SSH client and verify connectivity.

•       Configure an SCP server on a router.

---
R1
```bash

```

**Part 3: Configure Administrative Roles**

•       Create multiple role views and grant varying privileges.

•       Verify and contrast views.

**Part 4: Configure Cisco IOS Resilience and Management Reporting**

•       Secure the Cisco IOS image and configuration files.

•       Configure SNMPv3 Security using an ACL.

•       Configure a router as a synchronized time source for other devices using NTP.

•       Configure Syslog support on a router.

•       Install a Syslog server on a PC and enable it.

•       Make changes to the router and monitor syslog results on the PC.

**Part 5: Secure the Control Plane**

•       Configure OSPF Authentication using SHA256

•       Verify OSPF Authentication

**Part 6: Configure Automated Security Features**

•       Lock down a router using AutoSecure and verify the configuration.

•       Contrast using AutoSecure with manually securing a router using the command line.

**Background / Scenario**

The router is a critical component in any network. It controls the movement of data into and out of the network and between devices within the network. It is particularly important to protect network routers because the failure of a routing device could make sections of the network, or the entire network, inaccessible. Controlling access to routers and enabling reporting on routers is critical to network security and should be part of a comprehensive security policy.

In this lab, you will build a multi-router network and configure the routers and hosts. Use various CLI tools to secure local and remote access to the routers, analyze potential vulnerabilities, and take steps to mitigate them. Enable management reporting to monitor router configuration changes.

The router commands and output in this lab are from a Cisco 1941 router using Cisco IOS software, release 15.4(3)M2 (with a Security Technology Package license). Other routers and Cisco IOS versions can be used. See the Router Interface Summary Table at the end of the lab to determine which interface identifiers to use based on the equipment in the lab. Depending on the model of the router, the commands available and output produced may vary from what is shown in this lab.

**Note**: Before you begin, ensure that the routers and the switches have been erased and have no startup configurations.

# Part 1: Configure Basic Device Settings

In Part 1, configure basic settings, such as interface IP addresses.

**Step 1: Configure basic settings for each router.**

a.     Configure interface IP addresses as shown in the IP Addressing Table.

b.     Configure a clock rate for routers with a DCE serial cable attached to their serial interface. R1 is shown here as an example.

R1(config)# **interface S0/0/0**

R1(config-if)# **clock rate 64000**

c.     To prevent the router from attempting to translate incorrectly entered commands as though they were host names, disable DNS lookup. R1 is shown here as an example.

R1(config)# **no ip domain-lookup**

**Step 2: Configure OSPF routing on the routers.**

a.     Use the **router ospf** command in global configuration mode to enable OSPF on R1.

R1(config)# **router ospf 1**

b.     Configure the **network** statements for the networks on R1. Use an area ID of 0.

R1(config-router)# **network 192.168.48.0 0.0.0.255 area 0**

R1(config-router)# **network 10.1.1.0 0.0.0.3 area 0**

c.     Configure OSPF on R2 and R3.

d.     Issue the **passive-interface** command to change the G0/1 interface on R1 and R3 to passive.

R1(config)# **router ospf 1**

R1(config-router)# **passive-interface g0/1** R3(config)# **router ospf 1**

R3(config-router)# **passive-interface g0/1**

**Step 3: Verify OSPF neighbors and routing information.**

a.     Issue the **show ip ospf neighbor** command to verify that each router lists the other routers in the network as neighbors.

R1# **show ip ospf neighbor**

Neighbor ID Pri State Dead Time Address Interface

10.2.2.2 0 FULL/ - 00:00:31 10.1.1.2 Serial0/0/0

b.     Issue the **show ip route** command to verify that all networks display in the routing table on all routers.

R1# **show ip route**

Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP

D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area

N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2 E1 - OSPF external type 1, E2 - OSPF external type 2 i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2 ia - IS-IS inter area, * - candidate default, U - per-user static route o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP a - application route + - replicated route, % - next hop override

Gateway of last resort is not set

10.0.0.0/8 is variably subnetted, 3 subnets, 2 masks

C 10.1.1.0/30 is directly connected, Serial0/0/0

L 10.1.1.1/32 is directly connected, Serial0/0/0

O 10.2.2.0/30 [110/128] via 10.1.1.2, 00:03:03, Serial0/0/0

192.168.48.0/24 is variably subnetted, 2 subnets, 2 masks

C 192.168.48.0/24 is directly connected, GigabitEthernet0/1

L 192.168.48.1/32 is directly connected, GigabitEthernet0/1

·                  192.168.131.0/24 [110/129] via 10.1.1.2, 00:02:36, Serial0/0/0

**Step 4: Configure PC host IP settings.**

Configure a static IP address, subnet mask, and default gateway for PC-A and PC-C as shown in the IP Addressing Table.

**Step 5: Verify connectivity between PC-A and PC-C.**

a.     Ping from R1 to R3.

If the pings are not successful, troubleshoot the basic device configurations before continuing.

b.     Ping from PC-A, on the R1 LAN, to PC-C, on the R3 LAN.

If the pings are not successful, troubleshoot the basic device configurations before continuing.

**Note**: If you can ping from PC-A to PC-C you have demonstrated that OSPF routing is configured and functioning correctly. If you cannot ping but the device interfaces are up and IP addresses are correct, use the **show run, show ip ospf neighbor,** and **show ip route** commands to help identify routing protocol-related problems.

**Step 6: Save the basic running configuration for each router.**

Save the basic running configuration for the routers as text files on your PC. These text files can be used to restore configurations later in the lab.

# Part 2: Control Administrative Access for Routers

In Part 2, you will:

•       Configure and encrypt passwords.

•       Configure a login warning banner.

•       Configure enhanced username password security.

•       Configure enhanced virtual login security.

•       Configure an SSH server on R1.

•       Research terminal emulation client software and configure the SSH client.

•       Configure an SCP server on R1.

**Note**: Perform all tasks on both R1 and R3. The procedures and output for R1 are shown here.

**Task 1: Configure and Encrypt Passwords on Routers R1 and R3.**

**Step 1: Configure a minimum password length for all router passwords.**

Use the **security passwords** command to set a minimum password length of 10 characters.

R1(config)# **security passwords min-length 10**

**Step 2: Configure the enable secret password.**

Configure the enable secret encrypted password on both routers.

R1(config)# **enable secret cisco12345**

How does configuring an enable secret password help protect a router from being compromised by an attack?

**Step 3: Configure basic console, auxiliary port, and virtual access lines.**

**Note**: Passwords in this task are set to a minimum of 10 characters but are relatively simple for the benefit of performing the lab. More complex passwords are recommended in a production network.

a.     Configure a console password and enable login for routers. For additional security, the **exec-timeout** command causes the line to log out after 5 minutes of inactivity. The **logging synchronous** command prevents console messages from interrupting command entry.

**Note**: To avoid repetitive logins during this lab, the **exec-timeout** command can be set to 0 0, which prevents it from expiring. However, this is not considered a good security practice.

R1(config)# **line console 0**

R1(config-line)# **password ciscocon**

R1(config-line)# **exec-timeout 5 0**

R1(config-line)# **login**

R1(config-line)# **logging synchronous**

When you configured the password for the console line, what message was displayed?

b.     Configure a new password of **ciscoconpass** for the console.

c.     Configure a password for the AUX port for router R1.

R1(config)# **line aux 0**

R1(config-line)# **password ciscoauxpass**

R1(config-line)# **exec-timeout 5 0**

R1(config-line)# **login**

d.     Telnet from R2 to R1.

R2> **telnet 10.1.1.1**

Were you able to login? Explain.

What messages were displayed?

e.     Configure the password on the vty lines for router R1.

R1(config)# **line vty 0 4**

R1(config-line)# **password ciscovtypass**

R1(config-line)# **exec-timeout 5 0**

R1(config-line)# **transport input telnet**

R1(config-line)# **login**

**Note**: The default for vty lines is now **transport input none**.

Telnet from R2 to R1 again. Were you able to login this time?

f.      Enter privileged EXEC mode and issue the **show run** command. Can you read the enable secret password? Explain.

Can you read the console, aux, and vty passwords? Explain.

g.     Repeat the configuration portion of steps 3a through 3g on router R3.

**Step 4: Encrypt clear text passwords.**

a.     Use the **service password-encryption** command to encrypt the console, aux, and vty passwords.

R1(config)# **service password-encryption**

b.     Issue the **show run** command. Can you read the console, aux, and vty passwords? Explain.

At what level (number) is the default enable secret password encrypted?

At what level (number) are the other passwords encrypted?

Which level of encryption is harder to crack and why?

**Task 2: Configure a Login Warning Banner on Routers R1 and R3.**

**Step 1: Configure a warning message to display prior to login.**

a.     Configure a warning to unauthorized users with a message-of-the-day (MOTD) banner using the **banner motd** command. When a user connects to one of the routers, the MOTD banner appears before the login prompt. In this example, the dollar sign ($) is used to start and end the message.

R1(config)# **banner motd $Acceso no autorizado estrictamente prohibido!$**

R1(config)# **exit**

b.     Issue the **show run** command. What does the $ convert to in the output?

**Task 3: Configure Enhanced Username Password Security on Routers R1 and R3.**

**Step 1: Create a new user account with a secret password.**

a.     Create a new user account with SCRYPT hashing to encrypt the password.

R1(config)# **username user01 secret user01pass**

b.     Exit global configuration mode and save your configuration.

c.     Display the running configuration. Which hashing method is used for the password?

**Step 2: Test the new account by logging in to the console.**

a.     Set the console line to use the locally defined login accounts.

R1(config)# **line console 0**

R1(config-line)# **login local**

R1(config-line)# **end**

R1# **exit**

b.     Exit to the initial router screen which displays: R1 con0 is now available, Press RETURN to get started.

c.     Log in using the previously defined username **user01** and the password **user01pass**.

What is the difference between logging in at the console now and previously?

d.     After logging in, issue the **show run** command. Were you able to issue the command? Explain.

e.     Enter privileged EXEC mode using the **enable** command. Were you prompted for a password? Explain.

**Step 3: Test the new account by logging in from a Telnet session.**

a.     From PC-A, establish a Telnet session with R1.

PC-A> **telnet 192.168.48.1**

Were you prompted for a user account? Explain.

b.     Set the vty lines to use the locally defined login accounts.

R1(config)# **line vty 0 4**

R1(config-line)# **login local**

c.     From PC-A, telnet to R1 again.

PC-A> **telnet 192.168.48.1**

Were you prompted for a user account? Explain.

d.     Log in as **user01** with a password of **user01pass**.

e.     During the Telnet session to R1, access privileged EXEC mode with the **enable** command.

What password did you use?

f.      For added security, set the AUX port to use the locally defined login accounts.

R1(config)# **line aux 0**

R1(config-line)# **login local**

g.     End the Telnet session with the **exit** command.

**Task 4: Configure the SSH Server on Router R1 and R3.**

In this task, use the CLI to configure the router to be managed securely using SSH instead of Telnet. Secure Shell (SSH) is a network protocol that establishes a secure terminal emulation connection to a router or other networking device. SSH encrypts all information that passes over the network link and provides authentication of the remote computer. SSH is rapidly replacing Telnet as the remote login tool of choice for network professionals.

**Note**: For a router to support SSH, it must be configured with local authentication, (AAA services, or username) or password authentication. In this task, you configure an SSH username and local authentication.

  

**Step 1: Configure a domain name.**

Enter global configuration mode and set the domain name.

R1# **conf t**

R1(config)# **ip domain-name ccnasecurity.com**

**Step 2: Configure a privileged user for login from the SSH client.**

a.     Use the **username** command to create the user ID with the highest possible privilege level and a secret password.

R1(config)# **username admin privilege 15 secret cisco12345**

**Note**: Usernames are not case sensitive by default.

b.     Exit to the initial router login screen. Log in with the username admin and the associated password. What was the router prompt after you entered the password?

**Step 3: Configure the incoming vty lines.**

Specify a privilege level of **15** so that a user with the highest privilege level (15) will default to privileged EXEC mode when accessing the vty lines. Other users will default to user EXEC mode. Use the local user accounts for mandatory login and validation and accept only SSH connections.

R1(config)# **line vty 0 4**

R1(config-line)# **privilege level 15**

R1(config-line)# **login local**

R1(config-line)# **transport input ssh**

R1(config-line)# **exit**

**Note**: The **login local** command should have been configured in a previous step. It is included here to provide all commands, if you are doing this for the first time.

**Note**: If you add the keyword **telnet** to the **transport input** command, users can log in using Telnet as well as SSH, however, the router will be less secure. If only SSH is specified, the connecting host must have an SSH client installed.

**Step 4: Erase existing key pairs on the router.**

R1(config)# **crypto key zeroize rsa**

**Note**: If no keys exist, you might receive this message: % No Signature RSA Keys found in configuration.

**Step 5: Generate the RSA encryption key pair for the router.**

The router uses the RSA key pair for authentication and encryption of transmitted SSH data.

a.     Configure the RSA keys with **1024** for the number of modulus bits. The default is 512, and the range is from 360 to 2048.

R1(config)# **crypto key generate rsa general-keys modulus 1024**

The name for the keys will be: R1.ccnasecurity.com

% The key modulus size is 1024 bits

% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

R1(config)#

*Dec 16 21:24:16.175: %SSH-5-ENABLED: SSH 1.99 has been enabled

b.     Issue the **ip ssh version 2** command to force the use of SSH version 2.

R1(config)# **ip ssh version 2**

R1(config)# exit

**Step 6: Verify the SSH configuration.**

a.     Use the **show ip ssh** command to see the current settings.

R1# **show ip ssh**

b.     Fill in the following information based on the output of the **show ip ssh** command.

SSH version enabled:

Authentication timeout:

Authentication retries:

**Step 7: Configure SSH timeouts and authentication parameters.**

The default SSH timeouts and authentication parameters can be altered to be more restrictive using the following commands.

R1(config)# **ip ssh time-out 90**

R1(config)# **ip ssh authentication-retries 2**

**Step 8: Save the running-config to the startup-config.**

R1# **copy running-config startup-config**

**Task 5: Configure an SCP server on R1.**

Now that SSH is configured on the router, configure the R1 router as a secure copy (SCP) server.

**Step 1: Use the AAA authentication and authorization defaults on R1.**

Set the AAA authentication and authorization defaults on R1 to use the local database for logins.

**Note**: SCP requires the user to have privilege level 15 access. a. Enable AAA on the router.

R1(config)# **aaa new-model**

b.     Use the **aaa authentication** command to use the local database as the default login authentication method.

R1(config)# **aaa authentication login default local**

c.     Use the **aaa authorization** command to use the local database as the default command authorization.

R1(config)# **aaa authorization exec default local**

d.     Enable SCP server on R1.

R1(config)# **ip scp server enable**

**Step 2: Copy the running config on R1 to flash.**

SCP server allows files to be copied to and from a router’s flash. In this step, you will create a copy of the running-config on R1 to flash. You will then use SCP to copy that file to R3.

a.     Save the running configuration on R1 to a file on flash called R1-Config.

R1# **copy running-config flash: R1-Config (en caso de no funcionar escribir solo flash: y posteriormente insertar el nombre del archivo)**

b.     Verify that the new R1-Config file is on flash.

R1# **show flash**

-#- --length-- -----date/time------ path

1             75551300 Feb 16 2015 15:19:22 +00:00 c1900-universalk9-mz.SPA.154-3.M2.bin

2             1643 Feb 17 2015 23:30:58 +00:00 R1-Config

181047296 bytes available (75563008 bytes used)

**Step 3: Use SCP command on R3 to pull the configuration file from R1.**

a.     Use SCP to copy the configuration file that you created in Step2a to R3.

R3# **copy scp: flash**:

Address or name of remote host []? **10.1.1.1**

Source username [R3]? **admin**

Source filename []? **R1-Config**

Destination filename [R1-Config]? [**Enter]**

Password: **cisco12345**

!

2007 bytes copied in 9.056 secs (222 bytes/sec)

b.     Verify that the file has been copied to R3’s flash.

R3# **show flash**

-#- --length-- -----date/time------ path

1             75551300 Feb 16 2015 15:21:38 +00:00 c1900-universalk9-mz.SPA.154-3.M2.bin

2             1338 Feb 16 2015 23:46:10 +00:00 pre_autosec.cfg

3             2007 Feb 17 2015 23:42:00 +00:00 R1-Config

181043200 bytes available (75567104 bytes used)

c.     Issue the **more** command to view the contents of the R1-Config file.

R3# **more R1-Config**

!

version 15.4 service timestamps debug datetime msec service timestamps log datetime msec no service password-encryption !

hostname R1 !

<Output omitted> ! end

R3#

**Step 4: Save the configuration.**

Save the running configuration to the startup configuration from the privileged EXEC prompt.

# R1# copy running-config startup-config

# Part 3: Configure Administrative Roles

In Part 3 of this lab, you will:

•       Create multiple administrative roles, or views, on routers R1 and R3.

•       Grant each view varying privileges.

•       Verify and contrast the views.

The role-based CLI access feature allows the network administrator to define views, which are a set of operational commands and configuration capabilities that provide selective or partial access to Cisco IOS EXEC and configuration (config) mode commands. Views restrict user access to the Cisco IOS CLI and configuration information. A view can define which commands are accepted and what configuration information is visible.

**Note**: Perform all tasks on both R1 and R3. The procedures and output for R1 are shown here.

**Task 1: Enable Root View on R1 and R3.**

If an administrator wants to configure another view to the system, the system must be in root view. When a system is in root view, the user has the same access privileges as a user who has level-15 privileges, but the root view user can also configure a new view and add or remove commands from the view. When you are in a CLI view, you have access only to the commands that have been added to that view by the root view user.

**Step 1: Enable AAA on router R1.**

To define views, be sure that AAA was enabled with the **aaa new-model** command in Part 2.

**Step 2: Enable the root view.**

Use the command **enable view** to enable the root view. Use the **enable secret** password **cisco12345**. If the router does not have an enable secret password, create one now.

R1# **enable view**

Password: **cisco12345**

R1#

**Task 2: Create New Views for the Admin1, Admin2, and Tech Roles on R1 and R3.**

**Step 1: Create the admin1 view, establish a password, and assign privileges****.**

a.     The admin1 user is the top-level user below root that is allowed to access this router. It has the most authority. The admin1 user can use all **show**, **config**, and **debug** commands. Use the following command to create the admin1 view while in the root view.

R1(config)# **parser view admin1**

R1(config-view)#

**Note**: To delete a view, use the command **no parser view** _viewname_.

b.     Associate the admin1 view with an encrypted password.

R1(config-view)# **secret admin1pass**

R1(config-view)#

c.     Review the commands that can be configured in the admin1 view. Use the **commands ?** command to see available commands. The following is a partial listing of the available commands.

R1(config-view)# **commands ?**

RITE-profile Router IP traffic export profile command mode

RMI Node Config Resource Policy Node Config mode

RMI Resource Group Resource Group Config mode

RMI Resource Manager Resource Manager Config mode

RMI Resource Policy Resource Policy Config mode SASL-profile SASL profile configuration mode aaa-attr-list AAA attribute list config mode aaa-user AAA user definition accept-dialin VPDN group accept dialin configuration mode accept-dialout VPDN group accept dialout configuration mode address-family Address Family configuration mode

<output omitted>

d.     Add all **config**, **show**, and **debug** commands to the admin1 view and then exit from view configuration mode.

R1(config-view)# **commands exec include all show**

R1(config-view)# **commands exec include all config terminal**

R1(config-view)# **commands exec include all debug**

R1(config-view)# **end**

e.     Verify the admin1 view.

R1# **enable view admin1** Password: **admin1pass**

R1# **show parser view**

Current view is ‘admin1’

f.      Examine the commands available in the admin1 view.

R1# **?**

Exec commands:

<0-0>/<0-4> Enter card slot/sublot number configure Enter configuration mode debug Debugging functions (see also 'undebug') do-exec Mode-independent "do-exec" prefix support enable Turn on privileged commands exit Exit from the EXEC show Show running system

**Note**: There may be more EXEC commands available than are displayed. This depends on your device and the IOS image used.

g. Examine the **show** commands available in the admin1 view.

R1# **show ?**

aaa Show AAA values access-expression List access expression access-lists List access lists acircuit Access circuit info adjacency Adjacent nodes aliases Display alias commands alignment Show alignment information appfw Application Firewall information archive Archive functions arp ARP table

<output omitted>

**Step 2: Create the admin2 view, establish a password, and assign privileges****.**

a.     The admin2 user is a junior administrator in training who is allowed to view all configurations but is not allowed to configure the routers or use debug commands.

b.     Use the **enable view** command to enable the root view, and enter the enable secret password **cisco12345**.

R1# **enable view**

Password: **cisco12345**

c.     Use the following command to create the admin2 view.

R1(config)# **parser view admin2**

R1(config-view)#

d.     Associate the admin2 view with a password.

R1(config-view)# **secret admin2pass**

R1(config-view)#

e.     Add all **show** commands to the view, and then exit from view configuration mode.

R1(config-view)# **commands exec include all show**

R1(config-view)# **end**

f.      Verify the admin2 view.

R1# **enable view admin2**

Password: **admin2pass**

R1# **show parser view**

Current view is ‘admin2’

g.     Examine the commands available in the admin2 view.

R1# **?**

Exec commands:

<0-0>/<0-4> Enter card slot/sublot number do-exec Mode-independent "do-exec" prefix support enable Turn on privileged commands exit Exit from the EXEC show Show running system information

**Note**: There may be more EXEC commands available than are displayed. This depends on your device and the IOS image used.

What is missing from the list of admin2 commands that is present in the admin1 commands?

**Step 3: Create the tech view, establish a password, and assign privileges****.**

a.     The tech user typically installs end-user devices and cabling. Tech users are only allowed to use selected **show** commands.

b.     Use the enable **view** command to enable the root view, and enter the enable secret password **cisco12345**.

R1# **enable view**

Password: **cisco12345**

c.     Use the following command to create the tech view.

R1(config)# **parser view tech**

R1(config-view)#

d.     Associate the tech view with a password.

R1(config-view)# **secret techpasswd**

R1(config-view)#

e.     Add the following **show** commands to the view and then exit from view configuration mode.

R1(config-view)# **commands exec include show version**

R1(config-view)# **commands exec include show interfaces**

R1(config-view)# **commands exec include show ip interface brief**

R1(config-view)# **commands exec include show parser view**

R1(config-view)# **end**

f.      Verify the tech view.

R1# **enable view tech**

Password: **techpasswd**

R1# **show parser view**

Current view is ‘tech’

g.     Examine the commands available in the tech view.

R1# **?**

Exec commands:

<0-0>/<0-4> Enter card slot/sublot number do-exec Mode-independent "do-exec" prefix support enable Turn on privileged commands exit Exit from the EXEC

show Show running system information

**Note**: There may be more EXEC commands available than are displayed. This depends on your device and the IOS image used.

h.     Examine the **show** commands available in the tech view.

R1# **show ?**

banner Display banner information flash0: display information about flash0: file system flash1: display information about flash1: file system flash: display information about flash: file system interfaces Interface status and configuration ip IP information parser Display parser information usbflash0: display information about usbflash0: file system version System hardware and software status

**Note**: There may be more EXEC commands available than are displayed. This depends on your device and the IOS image used.

i.      Issue the **show ip interface brief** command. Were you able to do it as the tech user? Explain.

j.      Issue the **show ip route** command. Were you able to do it as the tech user?

k.     Return to root view with the **enable view** command.

R1# **enable view**

Password: **cisco12345**

l.      Issue the **show run** command to see the views you created. For tech view, why are the **show** and **show ip** commands listed as well as **show ip interface** and **show ip interface brief**?

**Step 4: Save the configuration on routers R1 and R3.**

Save the running configuration to the startup configuration from the privileged EXEC prompt.

# Part 4: Configure IOS Resilience and Management Reporting

In Part 4 of this lab, you will:

•       Secure the Cisco IOS image and configuration files.

•       Configure SNMPv3 security using an ACL.

•       Using NTP, configure a router as a synchronized time source for other devices.

•       Configure syslog support on a router.

•       Install a syslog server on a PC and enable it.

•       Configure the logging trap level on a router.

•       Make changes to the router and monitor syslog results on the PC.

**Note**: Perform all tasks on both R1 and R3. The procedure and output for R1 is shown here.

**Task 1: Secure Cisco IOS Image and Configuration Files on R1 and R3.**

The Cisco IOS resilient configuration feature enables a router to secure the running image and maintain a working copy of the configuration. This ensures that those files can withstand malicious attempts to erase the contents of persistent storage (NVRAM and flash). This feature secures the smallest working set of files to preserve persistent storage space. No extra space is required to secure the primary Cisco IOS image file. In this task, you configure the Cisco IOS Resilient Configuration feature.

**Note**: Cisco IOS resilient configuration feature is not available on the Cisco 1921 router.

**Note**: The output of the commands in this Task are for example purposes only. Your output will be different.

**Step 1: Display the files in flash memory for R1.**

The **show flash:** command displays the contents of sub-directories. The **dir** command only displays contents of the current directory.

R1# **show flash:**

-#- --length-- -----date/time------ path

1                  75551300 Feb 5 2015 16:53:34 +00:00 c1900-universalk9-mz.SPA.154-3.M2.bin

2                  0 Jan 6 2009 01:28:44 +00:00 ipsdir

3                  334531 Jan 6 2009 01:35:40 +00:00 ipsdir/R1-sigdef-default.xml 4 461 Jan 6 2009 01:37:42 +00:00 ipsdir/R1-sigdef-delta.xml

5               8509 Jan 6 2009 01:33:42 +00:00 ipsdir/R1-sigdef-typedef.xml

6               38523 Jan 6 2009 01:33:46 +00:00 ipsdir/R1-sigdef-category.xml

7               304 Jan 6 2009 01:31:48 +00:00 ipsdir/R1-seap-delta.xml

8               491 Jan 6 2009 01:31:48 +00:00 ipsdir/R1-seap-typedef.xml

9               1410 Oct 26 2014 04:44:08 +00:00 pre_autosec.cfg

76265535 bytes available (180221889 bytes used)

R1# **dir**

Directory of flash:/

1   -rw- 75551300 Feb 5 2015 16:53:34 +00:00 c1900-universalk9-mz.SPA.1543.M2.bin

2   drw- 0 Jan 6 2009 01:28:44 +00:00 ipsdir

9 -rw- 1410 Oct 26 2014 04:44:08 +00:00 pre_autosec.cfg

256487424 bytes total (180221889 bytes free)

**Step 2: Secure the Cisco IOS image and archive a copy of the running configuration.**

a.     The **secure boot-image** command enables Cisco IOS image resilience, which hides the file from the **dir** command and **show** commands. The file cannot be viewed, copied, modified, or removed using EXEC mode commands. (It can be viewed in ROMMON mode.) When turned on for the first time, the running image is secured.

R1(config)# **secure boot-image**

.Feb 11 25:40:13.170: %IOS_RESILIENCE-5-IMAGE_RESIL_ACTIVE: Successfully secured running image

b.     The **secure boot-config** command takes a snapshot of the router running configuration and securely archives it in persistent storage (flash).

R1(config)# **secure boot-config**

.Feb 11 25:42:18.691: %IOS_RESILIENCE-5-CONFIG_RESIL_ACTIVE: Successfully secured config archive [flash:.runcfg-20150211-224218.ar]

**Step 3: Verify that your image and configuration are secured.**

You can use only the **show secure bootset** command to display the archived filename. Display the status of configuration resilience and the primary bootset filename.

R1# **show secure bootset**

IOS resilience router id FTX1111W0QF

IOS image resilience version 15.4 activated at 25:40:13 UTC Wed Feb 11 2015

Secure archive flash: c1900-universalk9-mz.SPA.154-3.M2.bin type is image (elf)

[]

file size is 75551300 bytes, run size is 75730352 bytes

Runnable image, entry point 0x8000F000, run from ram

IOS configuration resilience version 15.4 activated at 25:42:18 UTC Wed Feb 11 2015 Secure archive flash:.runcfg-20150211-224218.ar type is config configuration archive size 3293 bytes

What is the name of the archived running config file and on what is the name based?

**Step 4: Display the files in flash memory for R1.**

a.     Display the contents of flash using the **show flash** command.

R1# **show flash:**

-#- --length-- -----date/time------ path

2                  0 Jan 6 2009 01:28:44 +00:00 ipsdir

3                  334531 Jan 6 2009 01:35:40 +00:00 ipsdir/R1-sigdef-default.xml 4 461 Jan 6 2009 01:37:42 +00:00 ipsdir/R1-sigdef-delta.xml

5               8509 Jan 6 2009 01:33:42 +00:00 ipsdir/R1-sigdef-typedef.xml

6               38523 Jan 6 2009 01:33:46 +00:00 ipsdir/R1-sigdef-category.xml

7               304 Jan 6 2009 01:31:48 +00:00 ipsdir/R1-seap-delta.xml

8               491 Jan 6 2009 01:31:48 +00:00 ipsdir/R1-seap-typedef.xml

9               1410 Oct 26 2014 04:44:08 +00:00 pre_autosec.cfg

76265535 bytes available (180221889 bytes used)

Is the Cisco IOS image or the archived running config file listed?

b.     How can you tell that the Cisco IOS image is still there?

**Step 5: Disable the IOS Resilient Configuration feature.**

a.     Disable the Resilient Configuration feature for the Cisco IOS image.

R1# **config t**

R1(config)# **no secure boot-image**

.Feb 11 25:48:23.009: %IOS_RESILIENCE-5-IMAGE_RESIL_INACTIVE: Disabled secure image archival

b.     Disable the Resilient Configuration feature for the running config file.

R1(config)# **no secure boot-config**

.Feb 11 25:48:47.972: %IOS_RESILIENCE-5-CONFIG_RESIL_INACTIVE: Disabled secure config archival [removed flash:.runcfg-20150211-224218.ar]

**Step 6: Verify that the Cisco IOS image is now visible in flash.**

Use the **show flash:** command to display the files in flash.

R1# **show flash:**

-#- --length-- -----date/time------ path

1                  75551300 Feb 5 2015 16:53:34 +00:00 c1900-universalk9-mz.SPA.154-3.M2.bin

2                  0 Jan 6 2009 01:28:44 +00:00 ipsdir

3                  334531 Jan 6 2009 01:35:40 +00:00 ipsdir/R1-sigdef-default.xml 4 461 Jan 6 2009 01:37:42 +00:00 ipsdir/R1-sigdef-delta.xml

5               8509 Jan 6 2009 01:33:42 +00:00 ipsdir/R1-sigdef-typedef.xml

6               38523 Jan 6 2009 01:33:46 +00:00 ipsdir/R1-sigdef-category.xml

7               304 Jan 6 2009 01:31:48 +00:00 ipsdir/R1-seap-delta.xml

8               491 Jan 6 2009 01:31:48 +00:00 ipsdir/R1-seap-typedef.xml

9               1410 Oct 26 2014 04:44:08 +00:00 pre_autosec.cfg

76265535 bytes available (180221889 bytes used) **Step 7: Save the configuration on both routers.**

Save the running configuration to the startup configuration from the privileged EXEC prompt.

**Task 2: Configure a Synchronized Time Source Using NTP.**

R2 will be the master NTP clock source for routers R1 and R3.

**Note**: R2 could also be the master clock source for switches S1 and S3, but it is not necessary to configure them for this lab.

**Step 1: Set Up the NTP Master using Cisco IOS commands.**

R2 is the master NTP server in this lab. All other routers and switches learn the time from it, either directly or indirectly. For this reason, you must ensure that R2 has the correct Coordinated Universal Time set. a. Use the **show clock** command to display the current time set on the router.

R2# **show clock**

*19:48:38.858 UTC Wed Feb 18 2015

b.     To set the time on the router, use the **clock set** _time_ command.

R2# **clock set 20:12:00 Dec 17 2025**

R2#

*Dec 17 20:12:18.000: %SYS-6-CLOCKUPDATE: System clock has been updated from 01:20:26 UTC Mon Dec 15 2025 to 20:12:00 UTC Wed Dec 17 2025, configured from console by admin on console.

c.     Configure NTP authentication by defining the authentication key number, hashing type, and password that will be used for authentication. The password is case sensitive.

R2# **config t**

R2(config)# **ntp** **authentication-key 1 md5 NTPpassword**

d.     Configure the trusted key that will be used for authentication on R2.

R2(config)# **ntp trusted-key 1**

e.     Enable the NTP authentication feature on R2.

R2(config)# **ntp** **authenticate**

f.      Configure R2 as the NTP master using the **ntp master** _stratum-number_ command in global configuration mode. The stratum number indicates the distance from the original source. For this lab, use a stratum number of **3** on R2. When a device learns the time from an NTP source, its stratum number becomes one greater than the stratum number of its source.

R2(config)# **ntp master 3**

**Step 2: Configure R1 and R3 as NTP clients using the CLI.**

a.     Configure NTP authentication by defining the authentication key number, hashing type, and password that will be used for authentication.

R1# **config t**

R1(config)# **ntp authentication-key 1 md5 NTPpassword**

b.     Configure the trusted key that will be used for authentication. This command provides protection against accidentally synchronizing the device to a time source that is not trusted.

R1(config)# **ntp trusted-key 1**

c.     Enable the NTP authentication feature.

R1(config)# **ntp authenticate**

d.     R1 and R3 will become NTP clients of R2. Use the command **ntp server** _hostname_. The host name can also be an IP address. The command **ntp update-calendar** periodically updates the calendar with the NTP time.

R1(config)# **ntp server 10.1.1.2**

R1(config)# **ntp update-calendar**

e.     Verify that R1 has made an association with R2 with the **show ntp associations** command. You can also use the more verbose version of the command by adding the **detail** argument. It might take some time for the NTP association to form.

R1# **show ntp associations**

address ref clock st when poll reach delay offset disp ~10.1.1.2 127.127.1.1 3 14 64 3 0.000 -280073 3939.7

*sys.peer, # selected, +candidate, -outlyer, x falseticker, ~ configured

f.      Issue the **debug ntp all** command to see NTP activity on R1 as it synchronizes with R2.

R1# **debug ntp all**

NTP events debugging is on

NTP core messages debugging is on

NTP clock adjustments debugging is on

NTP reference clocks debugging is on

NTP packets debugging is on

Dec 17 20.12:18.554: NTP message sent to 10.1.1.2, from interface 'Serial0/0/0' (10.1.1.1).

Dec 17 20.12:18.574: NTP message received from 10.1.1.2 on interface 'Serial0/0/0' (10.1.1.1).

Dec 17 20:12:18.574: NTP Core(DEBUG): ntp_receive: message received

Dec 17 20:12:18.574: NTP Core(DEBUG): ntp_receive: peer is 0x645A3120, next action is 1.

Dec 17 20:12:18.574: NTP Core(DEBUG): receive: packet given to process_packet Dec 17 20:12:18.578: NTP Core(INFO): system event 'event_peer/strat_chg' (0x04) status 'sync_alarm, sync_ntp, 5 events, event_clock_reset' (0xC655) Dec 17 20:12:18.578: NTP Core(INFO): synchronized to 10.1.1.2, stratum 3

Dec 17 20:12:18.578: NTP Core(INFO): system event 'event_sync_chg' (0x03) status

'leap_none, sync_ntp, 6 events, event_peer/strat_chg' (0x664) Dec 17 20:12:18.578: NTP Core(NOTICE): Clock is synchronized.

Dec 17 20:12:18.578: NTP Core(INFO): system event 'event_peer/strat_chg' (0x04)

status 'leap_none, sync_ntp, 7 events, event_sync_chg' (0x673) Dec 17 20:12:23.554: NTP: Calendar updated.

g.     Issue the **undebug all** or the **no debug ntp all** command to turn off debugging.

R1# **undebug all**

h.     Verify the time on R1 after it has made an association with R2.

R1# **show clock**

*20:12:24.859 UTC Wed Dec 17 2025

**Task 3: Configure syslog Support on R1 and PC-A.**

**Step 1: Configure R1 to log messages to the syslog server using the CLI.**

a.      Verify that you have connectivity between R1 and PC-A by pinging the R1 G0/1 interface IP address 192.168.48.1. If it is not successful, troubleshoot as necessary before continuing.

b.      NTP was configured in Task 2 to synchronize the time on the network. Displaying the correct time and date in syslog messages is vital when using syslog to monitor a network. If the correct time and date of a message is not known, it can be difficult to determine what network event caused the message.

Verify that the timestamp service for logging is enabled on the router using the **show run** command. Use the following command if the timestamp service is not enabled.

R1(config)# **service timestamps log datetime msec**

c.      Configure the syslog service on the router to send syslog messages to the syslog server.

R1(config)# **logging host 192.168.48.3**

**Step 2: Configure the logging severity level on R1.**

Logging traps can be set to support the logging function. A trap is a threshold that when reached, triggers a log message. The level of logging messages can be adjusted to allow the administrator to determine what kinds of messages are sent to the syslog server. Routers support different levels of logging. The eight levels range from 0 (emergencies), indicating that the system is unstable, to 7 (debugging), which sends messages that include router information.

a.     Use the **logging trap** command to set the severity level for R1.

R1(config)# **logging trap debugging**

# Part 5: Configure Automated Security Features

In Part 6 of this lab, you will do as follows:

•       Use AutoSecure to secure R3.

•       Review router security configurations with CLI.

**Task 1: Use AutoSecure to Secure R3.**

By using a single command in CLI mode, the AutoSecure feature allows you to disable common IP services that can be exploited for network attacks. It can also enable IP services and features that can aid in the defense of a network when under attack. AutoSecure simplifies the security configuration of a router and hardens the router configuration.

**Step 1: Use the AutoSecure Cisco IOS feature.**

a.     Enter privileged EXEC mode using the **enable** command.

b.     Issue the **auto secure** command on R3 to lock down the router. R2 represents an ISP router, so assume that R3 S0/0/1 is connected to the Internet when prompted by the AutoSecure questions. Respond to the AutoSecure questions as shown in the following output. The responses are bolded.

R3# **auto secure**

--- AutoSecure Configuration ---

*** AutoSecure configuration enhances the security of the router, but it will not make it absolutely resistant to all security attacks ***

AutoSecure will modify the configuration of your device. All configuration changes will be shown. For a detailed explanation of how the configuration changes enhance security and any possible side effects, please refer to Cisco.com for Autosecure documentation.

At any prompt you may enter '?' for help.

Use ctrl-c to abort this session at any prompt.

Gathering information about the router for AutoSecure

Is this router connected to internet? [no]: **yes**

Enter the number of interfaces facing the internet [1]: **[Enter]**

Interface IP-Address OK? Method Status Protocol

Embedded-Service-Engine0/0 unassigned YES NVRAM administratively down down

GigabitEthernet0/0 unassigned YES manual administratively down down

GigabitEthernet0/1 192.168.3.1 YES manual up up

Serial0/0/0 unassigned YES NVRAM administratively down down

Serial0/0/1 10.2.2.1 YES manual up up

Enter the interface name that is facing the internet: **Serial0/0/1**

Securing Management plane services...

Disabling service finger

Disabling service pad

Disabling udp & tcp small servers

Enabling service password encryption

Enabling service tcp-keepalives-in

Enabling service tcp-keepalives-out

Disabling the cdp protocol

Disabling the bootp server

Disabling the http server

Disabling the finger service

Disabling source routing

Disabling gratuitous arp

Here is a sample Security Banner to be shown at every access to device. Modify it to suit your enterprise requirements.

Authorized Access only

This system is the property of So-&-So-Enterprise. UNAUTHORIZED ACCESS TO THIS DEVICE IS PROHIBITED. You must have explicit permission to access this device. All activities performed on this device are logged. Any violations of access policy will result in disciplinary action.

Enter the security banner {Put the banner between k and k, where k is any character}:

**# Unauthorized Access Prohibited #**

Enter the new enable password: **cisco123456**

Confirm the enable password: **cisco123456**

Configuring AAA local authentication Configuring console, Aux and vty lines for local authentication, exec-timeout, transport

Securing device against Login Attacks

Configure the following parameters

Blocking Period when Login Attack detected: **60**

Maximum Login failures with the device: **2**

Maximum time period for crossing the failed login attempts: **30**

Configure SSH server? [yes]: **[Enter]**

Configuring interface specific AutoSecure services

Disabling the following ip services on all interfaces:

no ip redirects no ip proxy-arp no ip unreachables no ip directed-broadcast no ip mask-reply

Disabling mop on Ethernet interfaces

Securing Forwarding plane services...

Enabling unicast rpf on all interfaces connected to internet

Configure CBAC Firewall feature? [yes/no]: **no**

This is the configuration generated:

no service finger no service pad no service udp-small-servers no service tcp-small-servers service password-encryption service tcp-keepalives-in service tcp-keepalives-out no cdp run no ip bootp server no ip http server no ip finger no ip source-route no ip gratuitous-arps no ip identd banner motd ^C Unaauthorized Access Prohibited ^C security authentication failure rate 10 log enable password 7 121A0C0411045A53727274 aaa new-model aaa authentication login local_auth local line console 0 login authentication local_auth exec-timeout 5 0 transport output telnet line aux 0 login authentication local_auth exec-timeout 10 0 transport output telnet line vty 0 4 login authentication local_auth transport input telnet line tty 1 2 login authentication local_auth exec-timeout 15 0

login block-for 60 attempts 2 within 30 crypto key generate rsa general-keys modulus 1024 ip ssh time-out 60 ip ssh authentication-retries 2

line vty 0 4 transport input ssh telnet service timestamps debug datetime msec localtime show-timezone service timestamps log datetime msec localtime show-timezone logging facility local2 logging trap debugging service sequence-numbers logging console critical logging buffered interface Embedded-Service-Engine0/0 no ip redirects no ip proxy-arp no ip unreachables no ip directed-broadcast no ip mask-reply no mop enabled interface GigabitEthernet0/0 no ip redirects no ip proxy-arp no ip unreachables no ip directed-broadcast no ip mask-reply no mop enabled interface GigabitEthernet0/1 no ip redirects no ip proxy-arp no ip unreachables no ip directed-broadcast no ip mask-reply no mop enabled interface Serial0/0/0 no ip redirects no ip proxy-arp no ip unreachables no ip directed-broadcast no ip mask-reply interface Serial0/0/1 no ip redirects no ip proxy-arp no ip unreachables no ip directed-broadcast no ip mask-reply access-list 100 permit udp any any eq bootpc interface Serial0/0/1 ip verify unicast source reachable-via rx allow-default 100

! end

Apply this configuration to running-config? [yes]: **[Enter]**

Applying the config generated to running-config

% You already have RSA keys defined named R3.ccnasecurity.com.

% They will be replaced.

% The key modulus size is 1024 bits % Generating 1024 bit RSA keys, keys will be non-exportable...

[OK] (elapsed time was 1 seconds)

*Feb 18 20:29:18.159: %SSH-5-DISABLED: SSH 2.0 has been disabled

R3#

000066: *Feb 18 20:29:21.023 UTC: %AUTOSEC-1-MODIFIED: AutoSecure configuration has been Modified on this device

R3#

**Note**: The questions asked and the output may vary depend on the features on the IOS image and device.

**Step 2: Establish an SSH connection from PC-C to R3.**

a.     Start PuTTy or another SSH client, and log in with the **admin** account and password **cisco12345** created when AutoSecure was run. Enter the IP address of the R3 G0/1 interface **192.168.3.1**.

b.     Because SSH was configured using AutoSecure on R3, you will receive a PuTTY security warning. Click **Yes** to connect anyway.

c.     Enter privileged EXEC mode, and verify the R3 configuration using the **show run** command.

d.     Issue the **show flash** command. Is there a file that might be related to AutoSecure, and if so what is its name and when was it created?

e.     Issue the command **more flash:pre_autosec.cfg**. What are the contents of this file, and what is its purpose?

f.      How would you restore this file if AutoSecure did not produce the desired results?

**Step 3: Contrast the AutoSecure-generated configuration of R3 with the manual configuration of R1.**

a.     What security-related configuration changes were performed on R3 by AutoSecure that were not performed in previous sections of the lab on R1?

b.     What security-related configuration changes were performed in previous sections of the lab that were not performed by AutoSecure?

c.     Identify at least five unneeded services that were locked down by AutoSecure and at least three security measures applied to each interface.

**Note**: Some of the services listed as being disabled in the AutoSecure output above might not appear in the **show running-config** output because they are already disabled by default for this router and Cisco IOS version.

Services disabled include:

For each interface, the following were disabled:

**Step 4: Test connectivity.**

Ping from PC-A on the R1 LAN to PC-C on the router R3 LAN. If pings from PC-A to PC-C are not successful, troubleshoot before continuing.

**Reflection**

1.     Explain the importance of securing router access and monitoring network devices.

2.     What advantages does SSH have over Telnet?

3.     How scalable is setting up usernames and using the local database for authentication?

4.     Why it is better to have centralized logging servers rather than to have the routers only log locally?

5.     What are some advantages to using AutoSecure?