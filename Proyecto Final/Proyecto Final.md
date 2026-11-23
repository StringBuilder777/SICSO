## Actualizar los routers 
**version vieja** 
```
c1900-universalk9-mz.SPA.151-1.M4.bin
```

**version nueva**
```
c1900-universalk9-mz.SPA.155-3.M4a.bin
```

![Router para actualizar la version](Evidencia/RV.PNG)

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

![Router para actualizar la version](Evidencia/RVINT.PNG)

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

```bash
```