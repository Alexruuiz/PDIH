# Práctica 1: Entrada/Salida utilizando interrupciones con lenguaje C

**Trabajo realizado por Alejandro Ruíz Rodríguez **

### 1º Preparación del material y zona de trabajo

Para esta práctica trabajaremos dentro de la carpeta P1, por lo que habrá que modificar la dirección de montaje de C, como ya hicimos anteriormente:

~~~~
MOUNT C C:\Users\alexs\Desktop\Universidad\PDIH\P1
~~~~
Esta sentencia sirve para montar en C: el contenido de la carpeta  P1, de manera que esta sea la carpeta raíz en DOSBox. Dentro de P1 tendremos la carpeta bc y dentro de esta zona tendremos otra carpeta denominada P1 donde trabajaremos y donde estarán los archivos necesarios para la compilación de archivos .asm y .c

### 2º Script para compilación de archivos en lenguaje C

Para poder compilar archivos en lenguaje c, utilizaré el script cedido en clase. Dicho script tiene como nombre C2 y se utiliza de la siguiente manera:
~~~
C:\BC\P1>  C2.BAT <archivo.c>
~~~
De esta manera podríamos ver los resultados de la compilación del archivo .c y obtener el ejecutable.

### 3º REQUISITOS MÍNIMOS: Creación de PROGRAM.c

Una vez que ya tenemos la zona de trabajo preparada, creamos un archivo .c al cual hemos denominado `program.c` y será donde se desarrollarán las 9 funciones requeridas.


##### Primera función: gotoxy(x,y)

Esta función se encarga de mover el cursor a la posición indicada. Le pasaremos como parámetro dos enteros indicando la coordenada X e Y donde se posicionará el cursor.
~~~
void gotoxy(int x, int y){
	union REGS inregs, outregs;
	inregs.h.dl = x;
	inregs.h.dh = y;
	inregs.h.bh = 0x00;
	inregs.h.ah = 0x02;
	int86(0x10, &inregs, &outregs);
}
~~~

En `ah` le estamos indicando la subfunción `0x02`. Y en `dl` y `dh` le estamos indicando la fila y la columna.

##### Segunda función: setcursortype(enum tipo)

Con esta función podemos elegir el modo de visualización del cursor, pudiendo aceptar 3 tipos: el tipo 0 `INVISIBLE`, el tipo 1 `NORMAL` y el tipo 2 `GRUESO`.

~~~
void setcursortype(enum tipoC tipo_cursor){
	union REGS inregs, outregs;
	inregs.h.ah = 0x01;
	switch(tipo_cursor){
		case INVISIBLE: //invisible
			inregs.h.ch = 010;
			inregs.h.cl = 000;
			break;
		case NORMAL: //normal
			inregs.h.ch = 010;
			inregs.h.cl = 010;
			break;
		case GRUESO: //grueso
			inregs.h.ch = 000;
			inregs.h.cl = 010;
			break;
	}
	int86(0x10, &inregs, &outregs);
}
~~~

Los valores que acepta dicha función están guardados en una variable de tipo enum:

~~~
enum tipoC{
	INVISIBLE, NORMAL, GRUESO
};
~~~



##### Tercera/Cuarta función: setvideomode(unsigned char modo) / getvideomode()

Con estas funciones podemos elegir el modo de video y ver cual es el modo de video activo.

~~~
void setvideomode(unsigned char modo){
   union REGS inregs, outregs;
   inregs.h.al = modo;
   inregs.h.ah = 0x00;
   int86(0x10, &inregs, &outregs);
}
~~~

~~~
int getvideomode(){
   union REGS inregs, outregs;
   inregs.h.ah = 0xF;
   int86(0x10, &inregs, &outregs);
   return outregs.h.al;
}
~~~

##### Quinta/Sexta función: textColor(int c) / textbackground(int c)

Estas funciones simplemente se utilizan para modificar las variables globales de color de fuente y color de fondo y su implementación es bastante simple:

~~~
void textcolor(int c){
	color_texto=c;
}
~~~

~~~
void textbackground(int c){
	color_fondo=c;
}
~~~

Estas funciones reciben como parámetro un entero, indicando el número del color deseado.

##### Séptima función: clrscr()

Esta función se encargar de borrar toda la pantalla y colocar el cursor al inicio de esta, utilizando la función `gotoxy(0,0)`.

~~~
void clrscr(){
	scroll(EJE_Y+1);
	gotoxy(0,0);
}
~~~

Sin embargo, esta función es un poco más compleja y ha requerido de la creación de otras nuevas funciones: `scroll` y `coordenadas_cursor`:

~~~
void scroll(int lin_avance){
	union REGS inregs, outregs;
	inregs.h.ah=0x06;
	inregs.h.al=lin_avance;
	//Actualizamos el color de fondo y texto
	inregs.h.bh=color_fondo << 4 | color_texto;
	
	//Indicamos las esquinas del recuadro a ser desplazado
	inregs.h.ch=0;
	inregs.h.cl=0;
	inregs.h.dh=EJE_Y;
	inregs.h.dl=EJE_X;
	
	int86(0x10,&inregs,&outregs);
	
	coordenadas_cursor(); //Actualizamos coordenadas cursor
	if(CURSOR_Y-lin_avance >= 0)
		gotoxy(CURSOR_X,CURSOR_Y-lin_avance);
	else
		gotoxy(CURSOR_X,0);
}
~~~

Esta función hace un scroll hacia arriba, el numero de líneas indicado como parámetro. Así, si por ejemplo queremos hacer un scroll de 10 líneas, ponemos `scroll(10)`, y la pantalla bajará hacia abajo, 10 líneas. Pero además necesitamos también modificar la posición del cursor, por lo que modificaremos esta mediante el uso de `gotoxy()` subiendo el cursor el mismo número de veces que de líneas de avance. Para que el cursor no se salga de la pantalla he puesto un limite de manera que si se sale, la coordenada Y de cursor se pone a 0. Además, esta función utilizará las variables globales del color, para rellenar los nuevos espacios en blanco `inregs.h.bh=color_fondo << 4 | color_texto;`. La segunda nueva función que utilizaremos es la siguiente:

~~~
void coordenadas_cursor(){
	union REGS inregs, outregs;
	inregs.h.ah=0x03;
	inregs.h.bh=0x00;
	int86(0x10,&inregs,&outregs);
	CURSOR_X=outregs.h.dl;
	CURSOR_Y=outregs.h.dh;
}
~~~

Esta función simplemente se encargar de actualizar las variable globales `CURSOR_X` Y `CURSOR_Y`. Estas mantienen las coordenadas del cursor en un momento dado. En la función `scroll()`, la utilizamos para acceder a dichas coordenadas del cursor en el momento justo antes de modificarlas.

##### Octava función: cputchar(char,int)

Esta función escribirá un carácter en pantalla, en la posición en la que se encuentre el cursor, con el color de fuente y de fondo indicado en las variables globales y repitiendo dicho carácter el número de veces indicado.

~~~
void cputchar(char ch, int repeticiones){
	union REGS inregs, outregs;
	int color = color_fondo << 4 | color_texto;

	inregs.h.ah=0x09;
	inregs.h.bh=0x00;
	inregs.h.al=ch;
	inregs.h.bl=color;
	inregs.x.cx=repeticiones;
	int86(0x10,&inregs,&outregs);
}
~~~

Como ya he comentado, a esta función se le pasa un carácter y el numero de veces que será escrito.

##### Novena función: getche()

Esta función simplemente obtiene el carácter introducido por teclado y lo muestra por pantalla. Para ello he utilizado dos funciones ya creadas.

~~~
void getche(){
	int tmp;
	printf("\nPulsa una tecla...  ");
	tmp = mi_getchar();
	printf("\nHas pulsado: ");
    mi_putchar( (char)tmp );
}
~~~

Como se puede ver, he utilizado la función ejemplo `mi_getchar()` para obtener por teclado el carácter y a continuación lo muestro por pantalla mediante la función ejemplo `mi_putchar((char)tmp)`.

### 4º REQUISITOS AMPLIADOS: Función de dibujado

##### Primera función: recuadro(...)

Esta función dibuja un recuadro pasándole como parámetro la coordenada de la esquina superior izquierda e inferior derecha del rectángulo. Para ello utilizaremos la función `gotoxy()` para ir posicionándose en las coordenadas donde iremos dibujando el marco y utilizaremos `cputchar()` para escribir los caracteres en dichas coordenadas. Y anteriormente a todo esto, actualizaremos el color de texto y de fondo:

~~~
void recuadro(int x1, int y1, int x2, int y2, int colorTexto, int colorFondo){
	int i;
	//Modificamos el color de texto y background
	textcolor(colorTexto);
	textbackground(colorFondo);

	//Dibujamos la primera línea horizontal
	gotoxy(x1,y1);
	cputchar('o',x2-x1+1);
	
	//Vamos dibujando las paredes del marco
	for(i=1 ; i<y2-y1+1 ; i++){
		gotoxy(x1,y1+i);
		cputchar('o',1); //Pared Izquierda
		gotoxy(x2,y1+i);
		cputchar('o',1); //Pared Derecha
	}
	
	//Dibujamos la última línea horizontal
	gotoxy(x1,y2);
	cputchar('o',x2-x1+1);
}
~~~



### 5º CREACIÓN DEL MAIN Y EXPLICACIÓN

~~~
int main(){
	char tmp[9];
	char tmp2;
	enum tipoC tipo_cursor=GRUESO;
	int modovideo;
	int iq=20;
	int aux=1;

	//******************************************************************
	//Funcion pedir un caracter y mostrarlo por pantalla
	getche();

	//******************************************************************
	//Pedimos al usuario que elija el aspecto del cursor
	while(aux){
		printf("\nElige el aspecto del cursor (INVISIBLE,NORMAL o GRUESO): ");
		scanf("%s",&tmp);
		if(strcmp(tmp,"INVISIBLE")==0 || strcmp(tmp,"invisible")==0){
			setcursortype(INVISIBLE);
			aux=0;
		}else if(strcmp(tmp,"NORMAL")==0 || strcmp(tmp,"normal")==0){
			setcursortype(NORMAL);
			aux=0;
		}else if(strcmp(tmp,"GRUESO")==0 || strcmp(tmp,"grueso")==0){
			setcursortype(GRUESO);
			aux=0;
		}	
	}
	aux=1;
		
	mi_pausa();
   	memset(tmp,'\0',9);
   	
   	//******************************************************************
   	//Pedimos al usuario que elija el modo de video
   	while(aux){
	   	printf("\nElige el modo de video (texto o grafico): ");
		scanf("%s",&tmp);
		
		if(strcmp(tmp,"texto")==0){
			setvideomode(MODOTEXTO);
			aux=0;
		}else if(strcmp(tmp,"grafico")==0){
			setvideomode(MODOGRAFICO);
			aux=0;
		}
	}
	aux=1;
	mi_pausa();
	
	//******************************************************************
	//Obtener y mostrar el modo de video actual
	modovideo=getvideomode();
	if(modovideo == 3)
		printf("\nEl modo de video actual es texto");
	else if(modovideo == 4)
		printf("\nEl modo de video actual es grafico");
	
	//******************************************************************	
	//Mover el cursor a una coordenada especifica
	gotoxy(20,10);
	mi_pausa();	
	coordenadas_cursor();
	printf("\nCoordenada x es %i y la coordenada y es %i",CURSOR_X,CURSOR_Y);
	
	//******************************************************************
	//Limpieza de pantalla
	//(aniadimos lineas de texto para visualizar la limpieza de pantalla)
	
	while(iq>0){
		printf("\n*****");
		iq--;
	}
		
	mi_pausa();
	
	//Cambiamos el color global de fondo y texto...
	textcolor(14);
	textbackground(4);

	clrscr();
	mi_pausa();
	
	//******************************************************************
	//Escribir caracter en pantalla un numero de veces indicado, y con el color actual
	//Volvemos a cambiar el color global de fondo y texto
	
	textcolor(13); //rosa
	textbackground(3); //azul cielo
	
	tmp2='o';
	cputchar(tmp2,5);  //Caracter "c" repetido 5 veces
	mi_pausa();
	
	//******************************************************************
	//funcion para hacer recuadro en pantalla
	recuadro(10,5,EJE_X-5,EJE_Y-9,10,5);
	mi_pausa();
		
	return 0;
}
~~~

En el main probamos cada una de la funciones anteriormente descritas, y comprobamos su correcto funcionamiento. Cada parte del main está separada por asteriscos, de manera que en cada zona pruebo una función distinta. En la primera parte utilizo la función `getche()`:

![Imagen getche](./Capturas/f1.jpg)

A continuación le pido al usuario que elija el aspecto del cursor:

![Imagen tipo cursor](./Capturas/f2.jpg)

Como vemos, el cursor se pone en modo grueso. Luego, le pido al usuario que elija el modo de video:

![Imagen modo video](./Capturas/f3.jpg)

Una vez escrito, se pondrá en modo texto o gráfico, borrándose la pantalla y localizándose en el inicio de la terminal. A continuación utilizo la función `getvideomode()` para que se muestre el modo de video en el que estamos y también utilizo la función `gotoxy()` para mover el cursor a la coordenada (20,10).

![gotoxy y ver el modo de video](./Capturas/f4.jpg)

Ahora vamos a utilizar la función de limpieza de pantalla `clrscr()`. Para ello, antes voy a añadir varias líneas de caracteres para poder visualizar bien si desaparecen todas.

![Imagen clear terminal](./Capturas/f5.jpg)

Y pulsamos enter...

![Imagen clear terminal](./Capturas/f6.jpg)

Como vemos a desaparecido todo el texto anterior, el ratón se ha posicionado al inicio de la terminal y los colores del texto y del fondo se han puesto como estaban definidos en la variables globales (Texto amarillo 14 y fondo rojo 4). A continuación, utilizaremos la función `cputchar()`, la cual escribirá el carácter "o" repetido 5 veces y utilizando los colores globales definidos anteriormente (Texto rosa 13 y fondo azul cielo 3).



<img src="./Capturas/f7.jpg" alt="Imagen CPUTCHAR" style="zoom:200%;" />

Por último, vamos a probar la función `recuadro()`, la cual nos va a dibujar un recuadro en las coordenadas especificadas y con el color de texto y de fondo pasado como parámetro (texto 10 y fondo 5):

![Imagen recuadro](./Capturas/f8.jpg)

Como hemos podido comprobar, todas las funciones funcionan correctamente.



