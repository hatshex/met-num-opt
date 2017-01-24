# Ubuntu

## Pre requisitos
```
$ sudo apt-get install build-essential
$ sudo apt-get install libblas-dev
$ sudo apt-get install nano
```
Abrimos un editor de texto
```
nano ejemplo_hello_world.c
```
Y tecleamos el siguiente código
``` 
/*ejemplo_hello_world.c*/
#include<stdio.h>
main(){
//comentario
/*
Otros comentarios, siempre ayudan a tu yo posterior
*/
        printf("Hello world!\n");
}
```

Para compilar
```
gcc ejemplo_hello_world.c -o ejemplo_hello_world.out
```

Para ejecutar el programa
```
./ejemplo_hello_world.out
```
