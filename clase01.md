# Ubuntu

## Pre requisitos
```
$ sudo apt-get install build-essential
$ sudo apt-get install libblas-dev
$ sudo apt-get install nano
```
Abrimos un editor de texto y tecleamos el siguiente código
``` 
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
