**Práctica de laboratorio: Configuración de un túnel VPN GRE de punto a punto**

# Utilice la Topología que ya tiene preconfiguradas las PCs y el ISP

# Tabla de direccionamiento

|                 |              |                  |                       |                            |
| --------------- | ------------ | ---------------- | --------------------- | -------------------------- |
| **Dispositivo** | **Interfaz** | **Dirección IP** | **Máscara de subred** | **Gateway predeterminado** |
| WEST            | G0/1         | 172.16.1.1       | 255.255.255.0         | N/A                        |
|                 | S0/0/0 (DCE) | 10.1.1.1         | 255.255.255.252       | N/A                        |
|                 | Tunnel0      | 172.16.12.1      | 255.255.255.252       | N/A                        |
| ISP             | S0/0/0       | 10.1.1.2         | 255.255.255.252       | N/A                        |
|                 | S0/0/1 (DCE) | 10.2.2.2         | 255.255.255.252       | N/A                        |
| EAST            | G0/1         | 172.16.2.1       | 255.255.255.0         | N/A                        |
|                 | S0/0/1       | 10.2.2.1         | 255.255.255.252       | N/A                        |
|                 | Tunnel0      | 172.16.12.2      | 255.255.255.252       | N/A                        |
| PC-A            | NIC          | 172.16.1.3       | 255.255.255.0         | 172.16.1.1                 |
| PC-C            | NIC          | 172.16.2.3       | 255.255.255.0         | 172.16.2.1                 |

# Objetivos

**Parte 1: configurar los parámetros básicos de los dispositivos**

**Parte 2: Configurar un túnel GRE**

**Parte 3: Habilitar el routing por el túnel GRE**

# Información básica/situación

La encapsulación de routing genérico (GRE) es un protocolo de tunneling que puede encapsular diversos protocolos de capa de red entre dos ubicaciones a través de una red pública, como Internet.

GRE se puede utilizar con lo siguiente:

-       La conexión de redes IPv6 a través de redes IPv4

-       Paquetes de multidifusión, como OSPF, EIGRP y aplicaciones de transmisión

En esta práctica de laboratorio, configurará un túnel VPN GRE de punto a punto sin cifrar y verificará que el tráfico de la red utilice el túnel. También configurará el protocolo de routing OSPF dentro del túnel VPN GRE. El túnel GRE se encuentra entre los routers EAST y WEST en el área OSPF 0. El ISP no tiene conocimiento del túnel GRE. La comunicación entre los routers EAST, WEST e ISP se logra mediante rutas estáticas predeterminadas.

**Nota**: los routers que se utilizan en las prácticas de laboratorio de CCNA son routers de servicios integrados

(ISR) Cisco 1941 con IOS de Cisco versión 15.2(4)M3 (imagen universalk9). Los switches que se utilizan son Cisco Catalyst 2960s con IOS de Cisco versión 15.0(2) (imagen de lanbasek9). Se pueden utilizar otros routers, switches y otras versiones del IOS de Cisco. Los ruteadores ya se encuentran configurados con la versión requerida.

# Parte 1: Configurar los parámetros básicos de dispositivos

En la parte 1, establecerá la topología de la red y configurará los parámetros básicos del router, como las direcciones IP de las interfaces, el routing, el acceso a los dispositivos y las contraseñas.

**Paso 1: configurar los parámetros básicos para cada router.**

a.     Desactive la búsqueda del DNS.
WEST
```bash
en
conf t
no ip domain-l
```

EAST
```bash
en 
conf t
no ip domain-l
```

ISP
```bash
en 
conf t
no ip domain-l
```


b.     Configure los nombres de los dispositivos.

WEST
```bash
hos WEST
```

ISP
```bash
hos ISP
```

EAST
```bash
hos EAST
```

c.     Habilite el servicio de encriptación de passwords.
WEST
```bash
ser p
```

EAST
```bash
ser p
```

ISP
```bash
ser p
```

d.     Cree un aviso de mensaje del día (MOTD) que advierta a los usuarios que se prohíbe el acceso no autorizado, mediante la siguiente leyenda. #**Prohibido el acceso al personal no autorizado ¡**#
WEST
```bash
ba m #Prohibido el acceso al personal no autorizado#
```

EAST
```bash
ba m #Prohibido el acceso al personal no autorizado#
```

ISP
```bash
ba m #Prohibido el acceso al personal no autorizado#
```

e.     Asigne **class** como la contraseña cifrada del modo EXEC privilegiado. Utilizando enable secret.
WEST
```bash
ena s class
```

EAST
```bash
ena s class
```

ISP
```bash
ena s class
```

f.      Aplique las direcciones IP a las interfaces Serial y Gigabit Ethernet según la tabla de direccionamiento y active las interfaces físicas. Todavía NO configure las interfaces Tunnel0.
WEST
```bash
int g0/1
ip add 172.16.1.1 255.255.255.0
no sh 
int s0/0/0
ip add 10.1.1.1 255.255.255.252
no sh

```

EAST
```bash
int g0/1
ip add 172.16.2.1 255.255.255.0
no sh 
int s0/0/1
ip add 10.2.2.1 255.255.255.252
no sh
```

ISP
```bash
int s0/0/0
ip add 10.1.1.2 255.255.255.252
no sh
int s0/0/1
ip add 10.2.2.2 255.255.255.252
no sh
```

**Paso 2: Configurar las rutas predeterminadas al router ISP.**

ISP
```bash
 ip route 0.0.0.0 0.0.0.0 10.1.1.1
 ip route 0.0.0.0 0.0.0.0 10.2.2.1
```
WEST
```bash
ip route 10.2.2.0 255.255.255.252 10.1.1.2
```

EAST
```bash
ip route 10.1.1.0 255.255.255.252 10.2.2.2
```

**Paso 3: Verificar la conectividad.**

En este momento, las computadoras no pueden hacerse ping entre sí. Cada computadora debe poder hacer ping a su gateway predeterminado. Los routers pueden hacer ping a las interfaces seriales de los demás routers en la topología. De lo contrario, lleve a cabo la resolución de problemas hasta que pueda verificar la conectividad.

**Paso 4: Guardar la configuración en ejecución.**

# Parte 2: Configurar un túnel GRE

En la parte 2, configurará un túnel GRE entre los routers EAST y WEST.

**Paso 1: Configurar la interfaz de túnel GRE.**

a.     Configure la interfaz de túnel en el router WEST. Utilice S0/0/0 en el router WEST como interfaz de origen del túnel y 10.2.2.1 como destino del túnel en el router EAST.

WEST(config)# **interface tunnel 0**

WEST(config-if)# **ip address 172.16.12.1 255.255.255.252**

WEST(config-if)# **tunnel source s0/0/0**

WEST(config-if)# **tunnel destination 10.2.2.1**

WEST
```bash
int t 0
ip a 172.16.12.1 255.255.255.252
t s s0/0/0
t d 10.2.2.1
```

b.     Configure la interfaz de túnel en el router EAST. Utilice S0/0/1 en el router EAST como interfaz de origen del túnel y 10.1.1.1 como destino del túnel en el router WEST.

EAST(config)# **interface tunnel 0**

EAST(config-if)# **ip address 172.16.12.2 255.255.255.252**

EAST(config-if)# **tunnel source s0/0/1**

EAST(config-if)# **tunnel destination 10.1.1.1**
EAST
```bash
int t 0
ip a 172.16.12.2 255.255.255.252
t s s0/0/1
t d 10.1.1.1
```


**Paso 2: Verificar que el túnel GRE funcione.**

a. Verifique el estado de la interfaz de túnel en los routers EAST y WEST.

WEST# **show ip interface brief**

Interface IP-Address OK? Method Status Protocol

Embedded-Service-Engine0/0 unassigned YES unset administratively down down

GigabitEthernet0/0 unassigned YES unset administratively down down

GigabitEthernet0/1 172.16.1.1 YES manual up up

Serial0/0/0 10.1.1.1 YES manual up up

Serial0/0/1 unassigned YES unset administratively down down Tunnel0 172.16.12.1 YES manual up up

EAST# **show ip interface brief**

Interface IP-Address OK? Method Status Protocol

Embedded-Service-Engine0/0 unassigned YES unset administratively down down

GigabitEthernet0/0 unassigned YES unset administratively down down

GigabitEthernet0/1 172.16.2.1 YES manual up up

Serial0/0/0 unassigned YES unset administratively down down

Serial0/0/1 10.2.2.1 YES manual up up

Tunnel0 172.16.12.2 YES manual up up

b.     Emita el comando **show interfaces tunnel 0** para verificar el protocolo de tunneling, el origen y el destino de túnel que se utilizan en este túnel.

¿Qué protocolo de tunneling se utiliza? ¿Cuáles son las direcciones IP de origen y destino de túnel asociadas al túnel GRE en cada router?

____________________________________________________________________________________ ____________________________________________________________________________________

____________________________________________________________________________________

c.     Haga ping a través del túnel desde el router WEST hasta el router EAST con la dirección IP de la interfaz de túnel.

WEST# **ping 172.16.12.2**

Type escape sequence to abort. Sending 5, 100-byte ICMP Echos to 172.16.12.2, timeout is 2 seconds: !!!!! Success rate is 100 percent (5/5), round-trip min/avg/max = 32/34/36 ms

d.     Utilice el comando **traceroute** en el router WEST para determinar la ruta a la interfaz de túnel en el router EAST. ¿Cuál es la ruta al router EAST?

____________________________________________________________________________________

e.     Haga ping a la ruta a través del túnel y rastréela desde el router EAST hasta el router WEST con la dirección IP de la interfaz de túnel.

¿Cuál es la ruta al router WEST desde el router EAST? _______________________________________

¿A qué interfaces se asocian estas direcciones IP? ¿Por qué?

____________________________________________________________________________________

____________________________________________________________________________________

f.      Los comandos **ping** y **traceroute** deberían realizarse correctamente. De lo contrario, lleve a cabo la resolución de problemas antes de continuar con la parte siguiente.

# Parte 3: Habilitar el routing por el túnel GRE

En la parte 3, configurará el routing OSPF de modo que las LAN en los routers EAST y WEST se puedan comunicar mediante el túnel GRE.

Después de configurar el túnel GRE, se puede implementar el protocolo de routing. Para los túneles GRE, una instrucción network incluye la red IP del túnel, en lugar de la red asociada a la interfaz serial, tal como sucedería con otras interfaces, como Serial y Ethernet. Recuerde que el router ISP no participa en este proceso de routing.

**Paso 1: Configurar el routing OSPF para el área 0 a través del túnel.**

a.     Configure la ID de proceso OSPF 1 con el área 0 en el router WEST para las redes 172.16.1.0/24 y 172.16.12.0/24.

WEST(config)# **router ospf 1**

WEST(config-router)# **network 172.16.1.0 0.0.0.255 area 0**

WEST(config-router)# **network 172.16.12.0 0.0.0.3 area 0**
```bash
int lo 1
ip add 1.1.1.1 255.255.255.255
no sh 
ex
rou o 1
ro 1.1.1.1
net 172.16.12.0 0.0.0.3 a 0
net 172.16.1.0 0.0.0.255 a 0
p g0/1
```

b.     Configure la ID de proceso OSPF 1 con el área 0 en el router EAST para las redes 172.16.2.0/24 y 172.16.12.0/24.

EAST(config)# **router ospf 1**

EAST(config-router)# **network 172.16.2.0 0.0.0.255 area 0**

EAST(config-router)# **network 172.16.12.0 0.0.0.3 area 0**

```bash
int lo 1
ip add 2.2.2.2 255.255.255.255
no sh 
ex
rou o 1
ro 2.2.2.2
net 172.16.12.0 0.0.0.3 a 0
net 172.16.2.0 0.0.0.255 a 0
p g0/1
```

**Paso 2: Verificar el enrutamiento OSPF.**

a.     Desde el router WEST, emita el comando **show ip route** para verificar la ruta a la LAN 172.16.2.0/24 en el router EAST.

WEST# **show ip route**

Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP

D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area

N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2 E1 - OSPF external type 1, E2 - OSPF external type 2 i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2 ia - IS-IS inter area, * - candidate default, U - per-user static route o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP + - replicated route, % - next hop override

Gateway of last resort is 10.1.1.2 to network 0.0.0.0

S* 0.0.0.0/0 [1/0] via 10.1.1.2

10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks

C 10.1.1.0/30 is directly connected, Serial0/0/0

L 10.1.1.1/32 is directly connected, Serial0/0/0

172.16.0.0/16 is variably subnetted, 5 subnets, 3 masks

C 172.16.1.0/24 is directly connected, GigabitEthernet0/1

L 172.16.1.1/32 is directly connected, GigabitEthernet0/1

O 172.16.2.0/24 [110/1001] via 172.16.12.2, 00:00:07, Tunnel0

C 172.16.12.0/30 is directly connected, Tunnel0

L 172.16.12.1/32 is directly connected, Tunnel0

¿Cuál es la interfaz de salida y la dirección IP para llegar a la red 172.16.2.0/24?

____________________________________________________________________________________

b.     Desde el router EAST, emita el comando para verificar la ruta a la LAN 172.16.1.0/24 en el router WEST. ¿Cuál es la interfaz de salida y la dirección IP para llegar a la red 172.16.1.0/24?

____________________________________________________________________________________

**Paso 3: Verificar la conectividad de extremo a extremo.**

a.     Haga ping de la PC-A a la PC-C. Esto debe tener éxito. De lo contrario, lleve a cabo la resolución de problemas hasta que haya conectividad de extremo a extremo.

b.     Utilice traceroute desde la PC-A hasta la PC-C. ¿Cuál es la ruta desde la PC-A hasta la PC-C?

____________________________________________________________________________________