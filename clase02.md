# Ubuntu

## Pre requisitos
``` shell
$ sudo apt-get install build-essential
$ sudo apt-get install libblas-dev
$ sudo apt-get install nano
```
Abrimos un editor de texto
``` shell
nano ejemplo_hello_world.c
```
Y tecleamos el siguiente código
``` shell
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
``` shell
gcc ejemplo_hello_world.c -o ejemplo_hello_world.out
```

Para ejecutar el programa
``` shell
./ejemplo_hello_world.out
```


## Otro ejemplo, inicializando variables y haciendo operaciones`
``` shell
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

## Máximo número
``` shell
#include<stdio.h>
#include<float.h>
main()
{
printf("Número más grande positivo: %e\n", DBL_MAX);

}
```
## UnderFlow, OverFlow
``` shell
#include<stdio.h>
#include<float.h>
main()
{

//UnderFlow, OverFlow

long double variable1=2.22e-326;
long double variable2 = 1e309;

printf("Valor de variable 1: %Le\n", variable1);
printf("Valor de variable 2: %Le\n", variable2);

}

```

## Epsilon de la máquina`
``` shell
#include<stdio.h>
main()
{

double variable =1.0;
while(1.0+variable !=1.0){
variable = variable/2.0;
}

printf("Valor de epsilon de la máquina %e\n", variable);
}
```
