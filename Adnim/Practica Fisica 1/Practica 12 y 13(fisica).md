Para la siguiente topología
![[Screenshot 2026-10-18 at 10.51.17 p.m..png]]

----
Configure la seguridad de puerto básica tal y como lo realizó en la practica 11. Solo que esta vez utilizando los switches del laboratorio.

## Parte 1:
Configura las direcciones IP en cada una de las PC's de acuerdo con la IP mostrada. Comprueba la conectividad entre todas las PC's.

PC-A
	IP: 192.168.10.10 Mask:255.255.255.0
PC-B
	IP: 192.168.10.11 Mask:255.255.255.0
PC-C
	IP: 192.168.10.12 Mask:255.255.255.0
PC-X
	IP: 192.168.10.20 Mask:255.255.255.0

## Parte 2:
Configura la seguridad de puerto en los puertos f0/2 y f0/3

### Paso 1:
Establece los puertos como modo de acceso
SW1
```bash
en
conf t
hos SW1
int ran f0/2-3
sw m a
```

SW2
```bash
en
conf t
hos SW2
```

### Paso 2:
Habilita la seguridad de puerto

SW1 
```bash
sw po
```

### Paso 3:
Establece el máximo de direcciones MAC permitidas en 1

SW1 
```bash
sw po max 1
```

### Paso 4:
Para el puerto fa0/2 la dirección MAC será aprendida manualmente, observa la dirección MAC de la PC-A y da de alta esa dirección en la seguridad de puerto de esa interface
SW1
```bash
ex
int f0/2
sw po mac [la mac]
ex
```

### Paso 5:
Para el puerto fa0/3 la dirección MAC será aprendida por de formadinámica (mac-address sticky)
SW1 Y SW2
```bash
int f0/3
sw po mac s
ex
```

### Paso 6:
Para ambas interfaces en caso de violación deberán apagarse
```bash
int ran f0/2-3
sw po v s
ex
```

### Paso 6: 
Para ambas interfaces el tiempo de inactividad permitido antes de dar de baja la dirección MAC es de 60 minutos
```bash
int ran f0/2-3
sw po a ti 60
sw po a ty ina
ex
```

### Paso 7: 
Apagar los puertos fastethernet no utilizados Fa0/4-24
```bash
int ran f0/4-24
sh
ex
```


VERIFICAR 
```bash
show port-security interface fa0/2
show port-security interface fa0/3
```
