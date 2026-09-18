
![[Evidencias/Screenshot 2026-09-18 at 9.42.28 a.m..png]]

![[Evidencias/Screenshot 2026-09-18 at 9.46.48 a.m..png]]

----
**Intructions**

***Part 1: Configure Basic Security on the Router***

a.     Configure IP addressing on **PCA** according to the Addressing Table.
![[Evidencias/Screenshot 2026-09-18 at 9.57.17 a.m..png]]

b.     Console into **RTA** from the Terminal on PCA.
![[Evidencias/Screenshot 2026-09-18 at 10.02.38 a.m..png]]
![[Evidencias/Screenshot 2026-09-18 at 10.03.47 a.m..png]]
c.     Configure the hostname as **RTA**
```bash
en
conf t
hos RTA

```
d.     Configure IP addressing on **RTA** and enable the interface.
```bash
int g0/0/0
ip add 172.16.126.1 255.255.255.0
no sh
```
e.     Encrypt all plaintext passwords.
```bash
service password-encryption
```
f.      Set the minimum password length to 10.
```bash
security passwords min-length 10
```
 g.     Set  password cisco12345
```bash
line v 0 4
pass cisco12345
exi
ena s cisco12345
```
h.     Disable DNS lookup.
```bash
no ip domain-lookup
```
i.      Set the domain name to **server1.com** (case-sensitive for scoring in PT).
```bash
ip domain-name server1.com
```
j.  Create a user of your choosing with a strong encrypted password.
```bash
username any_user secret cisco12345
```
k.     Generate 1024-bit RSA keys.
**Note**: In Packet Tracer, enter the crypto key generate rsa command and press Enter to continue.
```bash
cry k g r
```
![[Evidencias/Screenshot 2026-09-18 at 10.28.04 a.m..png]]
l.      Block anyone for three minutes who fails to log in after four attempts within a two-minute period.
```bash
login block-for 180 attempts 4 within 120
```
m.   Configure all VTY lines for SSH access and use the local user profiles for authentication.
```bash
lin v 0 4
tra i s
logi l
```
n.     Set the EXEC mode timeout to 6 minutes on the VTY lines.
```bash
exec-timeout 6
```
o.     Save the configuration to NVRAM.
```bash
do wr
```

---

**Part 2: Configure Basic Security on the Switch**
Configure switch **SW1** with corresponding security measures. Refer to the configuration steps on the router if you need additional assistance.
a.     Console into **SW1** from the Terminal on PCA.
![[Evidencias/Screenshot 2026-09-18 at 10.46.36 a.m..png]]
![[Evidencias/Screenshot 2026-09-18 at 10.47.32 a.m..png]]
b.     Configure the hostname as **SW1**.
```bash
en
conf t
hos SW1
```
c.     Configure IP addressing on SW1 **VLAN1** and enable the interface.
```bash
int v 1
ip add 172.16.126.2 255.255.255.0
```
d.     Configure the default gateway address.
```bash
ip de 172.16.126.1
```
e.     Disable all unused switch ports.
**Note**: On a switch it is a good security practice to disable unused ports. One method of doing this is to simply shut down each port with the ‘**shutdown**’ command. This would require accessing each port individually. There is a shortcut method for making modifications to several ports at once by using the **interface range** command. On **SW1** all ports except FastEthernet0/1 and GigabitEthernet0/1 can be shutdown with the following command:
```bash
int ran f0/1 - 24, g0/2
sh
```

f.      Encrypt all plaintext passwords.
```bash
se p
```

g.     Set a strong secret password of your choosing.
```bash
no ip domain-lo
```
i.      Set the domain name to **server1.com** (case-sensitive for scoring in PT).
```bash
ip domain-name server1.com
```

j.      Create a user of your choosing with a strong encrypted password. Use "**switchpass**" as password.
```bash
u admin s switchpass
```

k.     Generate 1024-bit RSA keys.
```bash
cry k g r
```
![[Evidencias/Screenshot 2026-09-18 at 11.27.09 a.m..png]]

l.      Configure all VTY lines for SSH access and use the local user profiles for authentication.
```bash
lin v 0 4
login l
tr i s
```

m.   Set the EXEC mode timeout to 6 minutes on all VTY lines.
```bash
exe 6
```

n.     Save the configuration to NVRAM.
```bash
do wr
```

---
![[Evidencias/Screenshot 2026-09-18 at 11.33.56 a.m..png]]
