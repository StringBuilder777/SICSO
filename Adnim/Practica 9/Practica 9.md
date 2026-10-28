![[Prac9_1.png]]

ACL

N.1 

Para la red 201.10.10.0 todos los host pueden salir del dominio exepto el .64.128.192
El nombre para la ACL será NO-SERVERS (solo se permiten dos entradas en la lista de acceso ultilizar las máscaras para filtrarlo)
Red
	210.10.10.0
1100 1001. 0000 1010. 0000 1010. 0000 0000 

Host

	201.10.10.64
1100 1001. 0000 1010. 0000 1010. 0100 0000 

	201.10.10.128
1100 1001. 0000 1010. 0000 1010. 1000 0000 

	 201.10.10.192
1100 1001. 0000 1010. 0000 1010. 1100 0000 


0000 0000. 0000 0000. 0000 0000. 0000 0000
1100 1001. 0000 1010. 0000 1010. 0100 0000
1100 1001. 0000 1010. 0000 1010. 1000 0000
1100 1001. 0000 1010. 0000 1010. 1100 0000
0000 0000. 0000 0000. 0000 0000. 1100 0000 = 0.0.0.3
mask
0.0.0.192

Router4
```bash
en
conf t
ip a e NO-SERVERS
20 d ip 201.10.10.0 0.0.0.192 any
90 p ip any any
ex
int f0/0
ip ac NO-SERVERS in
```

N.2
Las redes 201.10.10.0, 201.10.20.0, 201.10.30.0 no permiten tráfico de la red 201.10.50.0, t'rafico de otras redes es permitido.
El nombre del ACL es BLOCK_LAB

Red
	1100 1001. 0000 1010. 0011 0010. 0000 0000 -> 201.10.50.0 Host
	0000 000. 0000 0000. 0000 0000. 1111 1111 -> 0.0.0.255 Wc
 
Router4
```bash
en
conf t
ip a s BLOCK_LAB
10 d 201.10.50.0 0.0.0.255
90 p a
ex

int s0/0/0
ip ac BLOCK_LAB in
```

Router5
```bash
en
conf t
ip a s BLOCK_LAB
10 d 201.10.50.0 0.0.0.255
90 p a
ex

int s0/0/1
ip ac BLOCK_LAB in
```

N.3
La red 201.10.30.0 no permite tráfico entrante de Internet, eso significa que cualesquier dirección que no sea igual a las del dominio 201.10.0.0 será bloqueada.
Los Host.31,.63, .63, 127, 159, 191 y 223 de la red 201.10.40.0 no pueden salir a Internet
El host .95 y el resto de los host dentro del dominio son permitidos en Internet.
El nombre de la ACL es NO_INTERNET.



Red

```bash
en
conf t
ip a e NO-SERVERS
20 d ip 201.10.10.0 0.0.0.192 any
90 p ip any any
ex
int f0/0
ip ac NO-SERVERS in
```