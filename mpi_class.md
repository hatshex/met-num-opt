Crear los contenedores en la misma ruta
```
sudo docker run -dit -v $(pwd):/results -p 22 -h master --name master_container openmpi_mno_2017/openmpi:v1 /bin/bash
sudo docker run -dit -v $(pwd):/results -p 22 -h nodo1 --name nodo1_container openmpi_mno_2017/openmpi:v1 /bin/bash

sudo docker inspect "iddocker" | grep IPA #Obtener IP del docker
sudo docker exec -it "iddocker" /bin/bash #Entrar al docker
```
