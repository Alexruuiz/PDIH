# Práctica 4: El lenguaje Postscript

**Trabajo realizado por Alejandro Ruíz Rodríguez **

### INTRODUCCIÓN

Para esta practica utilizaré el programa **Notepad ++** para desarrollar el código en lenguaje Postscript. Una vez realizado el código, transformaré los archivos .ps en .pdf utilizando el siguiente conversor online: https://online2pdf.com/es/. Con esta página podré visualizar el resultado del código. A continuación experimentaré con los ejemplos propuestos en el guion y una vez estudiada la sintaxis y funcionamiento del código pasaré al desarrollo de los ejercicios propuestos.

### 1º PRIMER EJERCICIO: Casa

Para este ejercicio he desarrollado el siguiente código en lenguaje Postscript:

~~~
%!
newpath

%%RELLENOS--------------
0.7 0.7 0.7 setrgbcolor
%%Muros
200 550 moveto
240 0 rlineto
0 180 rlineto
-240 0 rlineto
closepath
fill

%%Puerta
300 550 moveto
40 0 rlineto
0 75 rlineto
-40 0 rlineto
closepath
fill

%%Ventana izq
220 660 moveto
60 0 rlineto
0 40 rlineto
-60 0 rlineto
closepath
fill

%%Ventana drcha
360 660 moveto
60 0 rlineto
0 40 rlineto
-60 0 rlineto
closepath
fill

%%Tejado
170 730 moveto
300 0 rlineto
-150 100 rlineto
closepath
fill
stroke

%%CONTORNOS--------------
1 setlinewidth

%%Muros
200 550 moveto
240 0 rlineto
0 180 rlineto
-240 0 rlineto
closepath

%%Puerta
300 550 moveto
40 0 rlineto
0 75 rlineto
-40 0 rlineto
closepath

%%Ventana izq
220 660 moveto
60 0 rlineto
0 40 rlineto
-60 0 rlineto
closepath

%%Ventana drcha
360 660 moveto
60 0 rlineto
0 40 rlineto
-60 0 rlineto
closepath

%%Tejado
170 730 moveto
300 0 rlineto
-150 100 rlineto
closepath

0.2 0.5 0.7 setrgbcolor
stroke

%%TEXTO----------------

/Times-Roman findfont
0 0 0 setrgbcolor
20 scalefont 
setfont 
170 550 translate
90 rotate
60 0 moveto
(mi casita) show

stroke
showpage
~~~

Lo primero que hago es dibujar cada parte de la casa rellena de color gris y luego represento los contornos de cada parte de la casa, con un color de lápiz azul claro y un grosor de línea fino `1 setlinewidth`. 

Para dibujar el texto "mi casita" de manera inclinada, he modificado el punto central del archivo moviéndolo a otra posición `170 550 translate`.  A continuación escribo el texto y luego utilizo la función *rotate* para rotarlo 90  grados `90 rotate`.

La imagen visualizada quedaría así:

![Imagen ejercicio1](./Capturas/ejer1.png)

### 2º SEGUNDO EJERCICIO: Tarjeta

Para este segundo ejercicio he desarrollado el siguiente código:

~~~
%!
0.9 0.9 0.94 setrgbcolor
newpath

%%Fondo
100 400 moveto
480 0 rlineto
0 300 rlineto
-480 0 rlineto
closepath
fill
stroke

%%Circulo
200 550 60 0 360 arc % dibujar un círculo gris
0.7 setgray % poner el color a gris claro
fill % rellenamos la figura recién terminada
0 setgray
1 setlinewidth
200 550 60 0 360 arc % dibujar una circunferencia negra
stroke

%%Texto tarjeta
/Helvetica-Bold findfont
0 0 0 setrgbcolor
15 scalefont 
setfont
430 560 moveto
(Alejandro Ruiz) show
stroke

/Helvetica findfont
0.7 0.7 0.7 setrgbcolor
15 scalefont 
setfont

396 540 moveto
(Ingeniero informatico) show

392 500 moveto
(P de la castellana, 25) show

361 480 moveto
(Local 2 - Edificio Cuzco IV) show

414 460 moveto
(CP: 28046 Madrid) show

426 440 moveto
(Tf.: 123 456 789) show

437 420 moveto
(info@alexsl.es) show

stroke
showpage
~~~

Primero creo el rectángulo principal, de color gris claro con un pequeño tono de azul, a continuación represento un circulo, con centro en la mitad izquierda de la tarjeta. Luego lo relleno de color gris claro y le dibujo un contorno negro. El radio del círculo será de **60** unidades. En el siguiente paso, añado el texto de la tarjeta. Para el nombre utilizo la fuente **Helvetica-Bold** y para lo demás utilizo la fuente **Helvetica**. Cada línea de texto está ajustada para que tenga alineación a la derecha. La visualización del archivo sería la siguiente:

![Imagen ejercicio2](./Capturas/ejer2.png)