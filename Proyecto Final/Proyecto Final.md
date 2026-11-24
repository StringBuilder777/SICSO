## Actualizar los routers 
**version vieja** 
```
c1900-universalk9-mz.SPA.151-1.M4.bin
```

**version nueva**
```
c1900-universalk9-mz.SPA.155-3.M4a.bin
```

![Router para actualizar la version](Evidencia/RV.png)

### version
**Comando**
```bash
en
sh ver | inc System img
```
**Salida**
```text
System image file is "flash0:c1900-universalk9-mz.SPA.151-1.M4.bin"
```

### Actualización
**Prender la int**
Router
```bash
en
conf t
int g0/0
ip add 67.67.67.1 255.255.255.0
no sh
ex
ex
```
PC
```bash
ip -> 67.67.67.1
Mask -> 255.255.255.0
Gateway -> 67.67.67.1
```

![Router para actualizar la version](Evidencia/RVINT.png)

**Eliminar la version vieja**
```bash
del c1900-universalk9-mz.SPA.151-1.M4.bin
```
O
```bash
del flash:c1900-universalk9-mz.SPA.151-4.M4.bin
```
**Cargar la nueva version**
**Comando**
```bash
en
copy tftp fl
```
**Salida**
```text
Address or name of remote host []? 67.67.67.2

Source filename []? c1900-universalk9-mz.SPA.155-3.M4a.bin

Destination filename [c1900-universalk9-mz.SPA.155-3.M4a.bin]?

Accessing tftp://67.67.67.2/c1900-universalk9-mz.SPA.155-3.M4a.bin...

Loading c1900-universalk9-mz.SPA.155-3.M4a.bin from 67.67.67.2: !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

[OK - 33591768 bytes]

33591768 bytes copied in 0.277 secs (12732840 bytes/sec)
```

**Configurar el Systema**
```bash
en
conf t
bo s f c1900-universalk9-mz.SPA.155-3.M4a.bin
ex
wri
rel
```

**Verificar**
```bash
sh ver | inc System im
```

```text
System image file is "flash0:c1900-universalk9-mz.SPA.155-3.M4a.bin"
```


### Configuracion 
**basico routers**
R8
```bash
en
conf t
hos R8
int g0/0
ip add 192.168.40.1 255.255.255.0
no sh
ex
int s0/0/0
ip add 10.10.6.1 255.255.255.252
no sh
ex

```

R7
```bash
en
conf t
hos R7
int g0/0
ip add 192.168.30.1 255.255.255.0
no sh
int s0/0/0
ip add 10.10.6.2 255.255.255.252 
cl r 2000000
no sh
int s0/0/1
ip add 10.10.5.1 255.255.255.252
cl r 2000000
no sh
ex

```
R6
```bash
en
conf t
Hos R6
int s0/0/1
ip add 10.10.5.2 255.255.255.252
no sh
int g0/0
ip add 209.180.10.1 255.255.255.0
no sh
int s0/0/0
ip add 10.10.4.1 255.255.255.0
no sh
ex

```
R5
```bash
en
conf t
hos R6
int s0/1/0
ip add 10.10.4.2 255.255.255.252
cl r 2000000
no sh 
int s0/0/1
ip add 10.10.3.1 255.255.255.252
cl r 2000000
no sh
int s0/0/0
ip add 10.10.2.0 255.255.255.252
cl r 2000000
no sh
ex

```
R4
```bash
en 
conf t
hos R4
int s0/0/1
ip add 10.10.3.2 255.255.255.252
no sh
int g0/0
ip add 192.168.15.1 255.255.255.0
no sh
ex
```
R2
```bash
en
conf t
hos R2
int s0/0/0
ip add 10.10.2.2 255.255.255.252
no sh
int s0/0/1
ip add 10.10.1.1 255.255.255.252
no sh
int g0/0
ip add 192.168.0.1 255.255.255.0
no sh
int g0/1
ip add 192.168.10.1 255.255.255.0
no sh
ex

```
R3
```bash
en
conf t
hos R3
int s0/0/1
ip add 10.10.1.2 255.255.255.252
cl r 2000000
no sh
int s0/0/0
ip add 10.10.0.1 255.255.255.252
cl r 2000000
no sh
ex
```
R1
```bash
en
conf t
hos R1
int s0/0/0
ip add 10.10.0.2 255.255.255.252
no sh
int g0/0
ip add 192.168.5.1 255.255.255.0
no sh 
ex
```

**Routeo**




```bash
```