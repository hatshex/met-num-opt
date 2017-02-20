Crear los contenedores en la misma ruta
```
sudo docker run -dit -v $(pwd):/results -p 22 -h master --name master_container openmpi_mno_2017/openmpi:v1 /bin/bash
sudo docker run -dit -v $(pwd):/results -p 22 -h nodo1 --name nodo1_container openmpi_mno_2017/openmpi:v1 /bin/bash

sudo docker inspect "iddocker" | grep IPA #Obtener IP del docker
sudo docker exec -it "iddocker" /bin/bash #Entrar al docker
```

Una vez que estamos dentro de los contenedores hay que agregar en el archivo /etc/hosts las ips respectivas para el nodo1 y el master
```
sudo nano \etc\hosts
```
Debe quedar algo así
```
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2 master
172.17.0.3 nodo1

```

Y reiniciamos el servicio ssh en ambos contenedores ( master y nodo1)

```
sudo service ssh restart
```

En el nodo master hay que ir a la carpeta /results  y verificamos que el archivo hello_clase.out exista
```
$ cd results/
$ ls -al
hello_clase.c  hello_clase.out
```
Ahora si ejecutamos el programa
```
mpirun --prefix /opt/openmpi-2.0.2/ -n 2 -H master,nodo1 hello_clase.out 
```

El sistema se conecta por ssh al nodo1 y ejecuta el programa
```
The authenticity of host 'nodo1 (172.17.0.3)' can't be established.
ECDSA key fingerprint is 80:b4:09:18:6e:00:fd:49:7d:94:a6:5d:4e:d9:10:0c.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nodo1,172.17.0.3' (ECDSA) to the list of known hosts.
mpi_user@nodo1's password: 
Hola del procesador 0 de 2!
Hola del procesador 1 de 2!

```
