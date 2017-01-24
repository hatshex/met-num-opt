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


## Otro ejemplo, inicializando variables y haciendo operaciones`
```
#include<stdio.h>
main(){

//Declarando de variables
int variable_int1, variable_int2;
double variable_double1, variable_double2, variable_double3;

// Inicializando variables
variable_int1=3;
variable_int2=-1;
variable_double1=5.0;
variable_double2=-3.0;
variable_double3= 0.6789281736281947;

//Operaciones
variable_int1=variable_int1/variable_int2;

printf("Variable entera dividida por -1: %d\n", variable_int1);
variable_double1 = variable_double1 / variable_double2;
printf("Variable double1 entre variable double2: %1.9f\n", variable_double1);

//Notación exponencial
printf("Variable double 1 entre variable double2 notación exponencial: %1.9e\n", variable_double1);
printf("Variable double 3: %e\n", variable_double3);
printf("Variable double 3 todos los decimales %1.5e\n", variable_double3);
}
```
