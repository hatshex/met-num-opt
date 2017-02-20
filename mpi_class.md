Para esta clase vamos a crear un directorio que se llame MPI, y dentro se debe colocar los archivos Dockerfile, openmpi-2.0.2.tar.gz [Descargar](https://www.open-mpi.org/software/ompi/v2.0/openmpi-2.0.2.tar.gz) y el programa hello_clase.out


```shell
$ sudo mkdir mpi
$ cd mpi
$ sudo nano Dockerfile
```
El archivo Dockerfile contiene lo siguiente:
```shell
FROM ubuntu:14.04

RUN apt-get update -y && apt-get install -y build-essential \
	nano \
	man \
	openssh-server

RUN groupadd mpi_user

RUN useradd mpi_user -g mpi_user -m -s /bin/bash

ADD openmpi-2.0.2.tar.gz /opt/

RUN cd /opt && chown -hR mpi_user:mpi_user openmpi-2.0.2

RUN mkdir -p /var/run/sshd

RUN echo "mpi_user ALL=(ALL:ALL) NOPASSWD:ALL" | (EDITOR="tee -a" visudo)

RUN echo "mpi_user:mpi" | chpasswd

USER mpi_user

RUN cd /opt/openmpi-2.0.2 && ./configure --prefix=/opt/openmpi-2.0.2 -with-sge && make all install

ENV PATH="/opt/openmpi-2.0.2/bin:$PATH"

ENV LD_LIBRARY_PATH="/opt/openmpi-2.0.2/lib:LD_LIBRARY_PATH"
```
Dentro de la carpeta de mpi, tenemos los 2 archivos.
```shell
$ ls
$ Dockerfile  openmpi-2.0.2.tar.gz
```




Ahora tenemos que construir la imagen del contenedor, este proceso va a tardar varios minutos
```shell
$ sudo docker build -t openmpi_mno_2017/openmpi:v1 .
```

Una vez que termina el proceso, verificamos que la imagen esté disponible con el comando `docker images`
```shell
$sudo docker images
[sudo] password for hatshex: 
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
openmpi_mno_2017/openmpi   v1                  a89db00a6867        9 days ago          649 MB
```

Una vez que ya tenemos la imagen, debemos crear 2 contenedores, uno llamado master y otro nodo1. 
Nota: Debemos estar dentro de la carpeta mpi cuando se ejecuten las instrucciones.
``` shell
$ pwd
mpi

$ sudo docker run -dit -v $(pwd):/results -p 22 -h master --name master_container openmpi_mno_2017/openmpi:v1 /bin/bash
$ sudo docker run -dit -v $(pwd):/results -p 22 -h nodo1 --name nodo1_container openmpi_mno_2017/openmpi:v1 /bin/bash
```
Para verificar que se hayan creado usamos `docker ps -a`
```shell
$ sudo docker ps -a

CONTAINER ID        IMAGE                         COMMAND                  CREATED             STATUS                    PORTS                   NAMES
6a3a2547d315        openmpi_mno_2017/openmpi:v1   "/bin/bash"              46 minutes ago      Up 46 minutes             0.0.0.0:32770->22/tcp   nodo1_container
c4b8ee392a2a        openmpi_mno_2017/openmpi:v1   "/bin/bash"              46 minutes ago      Up 46 minutes             0.0.0.0:32769->22/tcp   master_container
```

Ya que están creados, necesitamos saber las IPs que se le asignó a cada contenedor(master, nodo1), por lo que usamos el comando `docker inspect`
``` shell
➜  mpi git:(master) ✗ sudo docker inspect 6a3a2547d315 | grep IPA #Obtener IP del docker
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.3",
                    "IPAMConfig": null,
                    "IPAddress": "172.17.0.3",
➜  mpi git:(master) ✗ sudo docker inspect c4b8ee392a2a | grep IPA #Obtener IP del docker
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAMConfig": null,
                    "IPAddress": "172.17.0.2",

```
En seguida debemos entrar a cada uno de los contenedores usando el comando `docker exec`
```shell
sudo docker exec -it c4b8ee392a2a /bin/bash #Entrar al docker
```

Una vez que estamos dentro de los contenedores hay que agregar en el archivo /etc/hosts las ips respectivas para el nodo1 y el master
```shell
sudo nano \etc\hosts
```
Debe quedar algo así
```shell
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

```shell
sudo service ssh restart
```
En el nodo master, nos movemos a la carpeta `results/` y creamos y compilamos el programa `hello_clase.c`

Creamos el programa
```
$ sudo nano hello_clase.c
```
El programa debe contener el siguiente código fuente
```
#include<stdio.h>
#include<string.h>
#include<mpi.h>
int main(){
    int max_string=100;
    char greeting[max_string];
    int comm_sz;
    int my_rank;
    int q;
    MPI_Init(NULL,NULL);
    MPI_Comm_size(MPI_COMM_WORLD, &comm_sz);
    MPI_Comm_rank(MPI_COMM_WORLD, &my_rank);
    if(my_rank!=0){
     sprintf(greeting, "Hola del procesador %d de %d!", my_rank, comm_sz);
     MPI_Send(greeting, strlen(greeting)+1,MPI_CHAR,0,0,MPI_COMM_WORLD);    
    }else{
     printf("Hola del procesador %d de %d!\n", my_rank, comm_sz);
     for(q=1;q<comm_sz;q++){
      MPI_Recv(greeting, max_string, MPI_CHAR,q,0,MPI_COMM_WORLD,MPI_STATUS_IGNORE);
      printf("%s\n",greeting);
     }
    }
    MPI_Finalize();
}

```
Para compilar ejecutamos las siguientes instrucciones:
```
$ mpicc hello_clase.c -o hello_clase.out
```
Para ejecutar el programa
```
mpi_user@master:/results$ mpiexec -n 3 hello_clase.out
Hola del procesador 0 de 3!
Hola del procesador 1 de 3!
Hola del procesador 2 de 3!
```

Ahora si ejecutamos el programa
```shell
mpi_user@master:/results$ mpirun --prefix /opt/openmpi-2.0.2/ -n 2 -H master,nodo1 hello_clase.out 
```

El sistema se conecta por ssh al nodo1 y ejecuta el programa
```shell
The authenticity of host 'nodo1 (172.17.0.3)' can't be established.
ECDSA key fingerprint is 80:b4:09:18:6e:00:fd:49:7d:94:a6:5d:4e:d9:10:0c.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nodo1,172.17.0.3' (ECDSA) to the list of known hosts.
mpi_user@nodo1's password: 
Hola del procesador 0 de 2!
Hola del procesador 1 de 2!
```
Hacemos lo mismo para el nodo1
```shell
mpi_user@nodo1:/results$ mpirun --prefix /opt/openmpi-2.0.2/ -n 2 -H master,nodo1 hello_clase.out 
The authenticity of host 'master (172.17.0.2)' can't be established.
ECDSA key fingerprint is 80:b4:09:18:6e:00:fd:49:7d:94:a6:5d:4e:d9:10:0c.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'master,172.17.0.2' (ECDSA) to the list of known hosts.
mpi_user@master's password: 
Hola del procesador 0 de 2!
Hola del procesador 1 de 2!
```
