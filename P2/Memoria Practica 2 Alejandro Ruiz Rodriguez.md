# Práctica 2: Uso de ncurses para crear interfaces de usuario en modo texto bajo Linux

**Trabajo realizado por Alejandro Ruíz Rodríguez **

### 1º Instalar la librería ncurses, crear los programas de ejemplo ofrecidos más arriba y comprobar su funcionamiento. 

El comando utilizado para la instalación de la librería **ncurses** dentro del sistema operativo de Linux es el siguiente:

~~~~
sudo apt-get install libncurses5-dev libncursesw5-dev
~~~~
<img src="./Capturas/InstalacionLibreriasNcurses.jpg" alt="Imagen instalación libreria ncurses" style="zoom: 50%;" />

Una vez realizada la instalación, podremos proseguir con la creación y ejecución de los programas ejemplo.

Primero creamos un archivo .c y una vez terminado el código, lo compilamos utilizando el siguiente comando:

~~~
gcc -o archivo archivo.c -lncurses
~~~

#### Hello.c

Para este ejemplo vamos a crear un archivo **hello.c** con el código del guion, cambiando el texto a mostrar. Una vez creado el archivo lo compilamos de la siguiente manera:

~~~
gcc -o hello hello.c -lncurses
~~~

![Imagen hello.c](./Capturas/1.jpg)

Funcionamiento:

![Imagen Ejecucion hello.c](./Capturas/1eje.jpg)

#### Ventana.c

Para el ejemplo de **ventana.c** repetimos el mismo proceso:

![Imagen ventana.c](./Capturas/ventana.jpg)

![Imagen ejecución ventana.c](./Capturas/ventanaEje.jpg)

#### Pelotita.c

Y seguimos repitiendo el mismo proceso para **pelotita.c**, cuyo código encontramos de nuevo en el guion:

![Imagen pelotita.c](./Capturas/pelotita.jpg)

![Imagen ejecución pelotita.c](./Capturas/pelotitaEje.jpg)





### 2º Crear un juego sencillo tipo "pong"

#### Resumen

Para esta parte primero voy a resumir las funciones que he implementado y luego las voy a ir mostrando parte por parte, profundizando mas en cada una de ellas.

Mi juego comienza con un `MENÚ` en el que aparecen los controles para los dos jugadores que podrán jugar al juego. Para comenzar simplemente hay que tocar cualquier tecla. En la siguiente pantalla se muestra el juego junto con un **marcador** arriba izquierda que contará los puntos de cada jugador. 

La pelota rebotará por la pantalla y también con la barra del jugador, igual que el clásico juego de pong.

El código se ha implementado de manera que si se redimensiona la pantalla, **el área de juego se ajusta a la nueva medida, en tiempo real y de manera proporcional**, así, si la pelota esta en el medio, al aumentar o disminuir la pantalla, esta seguirá estando en el medio. Y de la misma manera se le aplica a la posición del jugador.

Además, para evitar que al disminuir se "pille" a la pelota o al jugador, quedándose este fuera de los límites de la pantalla, se ha implementado otra función que detecta el problema y reajusta su posición, para evitar bugs.

Finalmente, para terminar el juego hay que pulsar `Ctrl+C`.

![Imagen Juego empezado](./Capturas/2pongJuego.jpg)

#### Menu

Para mostrar el **menu**, utilizo un array de *char* donde guardo el texto a mostrar y a continuación utilizo la función `box(...)`, y coloco  esta variable menú, para mostrar dicho  menú con una interfaz más amena:

~~~
char menu[] = "            -------------PING PONG 2D - THE GAME------------- \
	               \n\n                        Controles:\
	              \n\n                          JUGADOR 1\
	                \n                          - Mover izq/drcha ---> A/D\
	              \n\n                          JUGADOR 2\
	                \n                          - Mover izq/drcha ---> J/L\
	              \n\n                          - Salir del juego ---> Ctrl+C\
	               \n\n                        Pulse cualquier tecla para empezar a jugar!!";
~~~

~~~
//Primera ventana menu
	WINDOW *window = newwin(rows,cols,0,0);
	wbkgd(window, COLOR_PAIR(3));
	box(window, '|', '-');             //Se crea el cuadro en pantalla
	
	mvwprintw(window, 10, 10, menu);   //Muestro el menú
	wrefresh(window);
	getch();
~~~

A continuación empezaría el juego y entraríamos en el **while**, del cual solo se sale cuando finalice el juego.

![Imagen menú](./Capturas/1pongMenu.jpg)

#### Redimensión pantalla y posición jugador

El siguiente código estaría situado dentro del bucle **while** y se encarga de la posición del jugador y de la redimensión de la pantalla:

~~~~
        //ACTUALIZAMOS LIMITES SEGUN TAMANIO PANTALLA, EN TIEMPO REAL
		//Si al minimizar la pantalla, la bola se queda fuera, para evitar
		//que se produzca error actualizamos su posicion a X=1, y=1
		//Tambien se actualiza de manera proporcional la posicion
		//de la barra del jugador
		
		getmaxyx(stdscr, rows, cols);
		
		if(max_x != cols || max_y != rows){    //medidas de pantalla diferentes?...
			jugador1x=jugador1x*cols/max_x;		
			jugador2x=jugador2x*cols/max_x;
			jugador2y=rows-2;
			
			max_y=rows;
			max_x=cols;
			if(x>=max_x || y>=max_y){
				x = y = 1;
				direction_x = 1;
				direction_y = 1;
			}
			//Posicion barra del jugador
			if(jugador1x>=max_x-11){
				jugador1x=max_x-12;
			}
			else if(jugador2x>=max_x-11){
				jugador2x=max_x-12;
			}

		}
~~~~

-**Funcionamiento:** Primero se actualizan los valores de las medidas de la pantalla. Dentro del primer if, se mira si siguen siendo las mismas. Si no lo son, la posición de los dos jugadores se actualiza de manera proporcional (siguiendo una simple regla de tres), y se actualiza el valor de las medidas de la pantalla. Luego se revisa si la pelota se ha quedado fuera de los limites y en tal caso se le asigna de valor `x=1` e `y=1` y `direccion=1`. De igual manera, si el nuevo limite a "cortado" o dejado fuera al jugador, se recoloca a este.

![Imagen cambio medidas pantalla 1](./Capturas/redimension1.jpg)

![Imagen cambio medidas pantalla 2](./Capturas/redimension2.jpg)

#### Movimiento barras Jugador 1 y 2

~~~
		//Movimiento de la barra del jugador
		mov = getch();
		if(mov == 'd' && jugador1x<max_x-13)
			jugador1x += 3;
		else if(mov == 'a' && jugador1x>2) 
			jugador1x -= 3;
		else if(mov == 'l' && jugador2x<max_x-13) 
			jugador2x += 3;
		else if(mov == 'j' && jugador2x>2) 
			jugador2x -= 3;
~~~

Con `getch()` obtenemos los valores insertados por teclado, para que los jugadores puedan controlar sus barras. En cada if se mira que tecla a tocado para mover al jugador de derecha a izquierda, sin salirse nunca de la pantalla.

`CONTROLES: Jug1 A - D // Jug2 J - L`

Para que el programa no se quede congelado con cada `getch()`, se utiliza la función `nodelay(stdscr,TRUE)`, para que no se espere a los inputs.

#### Dibujado barras y pelota y marcador

~~~
		//Dibujado de las barras y la pelota
		clear();
		mvprintw(y, x, "o");
		mvprintw(jugador1y, jugador1x, "[LLLL1LLLL]");
		mvprintw(jugador2y, jugador2x, "[TTTT2TTTT]");
		snprintf(marcador,50,"Jug1: %i \nJug2: %i",score1,score2);
		mvprintw(0, 0, marcador);
		refresh();
		
		usleep(DELAY);

~~~

En esta función simplemente dibujo las **barras, la pelota y el marcador** en cada iteración del bucle while, con las variables actualizadas anteriormente.

![Imagen jugador 1](./Capturas/barraJug1.jpg)

![Imagen jugador 2](./Capturas/barraJug2.jpg)

![Imagen marcador](./Capturas/marcador.jpg)

#### Función  toca

~~~
bool toca(int j1x, int j2x, int j2y, int nx, int ny){
	bool toca = false;
	
	if(nx>=j1x && nx<=j1x+10 && ny == 1)
		toca=true;
	else if(nx>=j2x && nx<=j2x+10 && ny == j2y)
		toca=true;
	
	return toca;
}
~~~

Esta función se utiliza para, pasándole la posición del J1 y J2 y la posición siguiente de la pelota, detectar si se hace o no contacto con la barra del jugador.

#### Movimiento pelota y Marcador

~~~
		//Si hay colision, la "y" se multiplicara por "-1"
		colision=toca(jugador1x,jugador2x,jugador2y,next_x,next_y,x,y);
		
		if (next_x >= max_x || next_x < 0) {
			direction_x*= -1;
		} else {
			x+= direction_x;
		}
		
		if (next_y >= max_y || next_y < 0 || colision) {
			direction_y*= -1;
			//Puntos score
			if(next_y >= max_y)
				score1+=1;
			else if(next_y < 0)
				score2+=1;
		} else {
			y+= direction_y;
		}
~~~

En esta parte del código se utiliza la función `toca`, para detectar si la pelota colisiona con algún jugador. En la primera parte se controla el rebote en el **eje X** y en el segundo if, el rebote en el **eje Y** y además se actualiza el **marcador** si se produce dicho rebote.

#### Código completo programa

~~~~
#include <stdlib.h>
#include <ncurses.h>

#define DELAY 30000

//########################################
//### Esta funcion devuelve true si la pelota va a tocar la barra de un jugador
//### j1x = coordenada x jugador 1
//### j2x = coordenada x jugador 2
//### j2y = coordenada y jugador 2
//### nx  = next_x de pelota
//### ny  = next_y de pelota
//### px  = coordenada x pelota
//### py  = coordenada x pelota
//########################################
bool toca(int j1x, int j2x, int j2y, int nx, int ny, int px, int py){
	bool toca = false;
	
	if(nx>=j1x && nx<=j1x+10 && ny == 1)
		toca=true;
	else if(nx>=j2x && nx<=j2x+10 && ny == j2y)
		toca=true;
	
	return toca;
}

int main(void) {
	int score1=0,score2=0;
	char marcador[50];
	int x = 2, y = 2;
	int max_y = 24, max_x = 95;
	int next_x = 0, next_y = 0;
	int jugador1x = max_x/2-6, jugador1y = 1;
	int jugador2x = max_x/2-6, jugador2y = max_y-3;
	int direction_x = 1, direction_y = 1;
	char mov; //Representa la tecla de movimiento pulsada
	bool colision = false;
	int rows, cols;
	char menu[] = "             -------------PING PONG 2D - THE GAME------------- \
	               \n\n                        Controles:\
	              \n\n                          JUGADOR 1\
	                \n                          - Mover izq/drcha ---> A/D\
	              \n\n                          JUGADOR 2\
	                \n                          - Mover izq/drcha ---> J/L\
	              \n\n                          - Salir del juego ---> Ctrl+C\
	               \n\n                        Pulse cualquier tecla para empezar a jugar!!";
	
	
	initscr();
	
	if (has_colors() == FALSE) {
		endwin();
		printf("El terminal no tiene soporte de color \n");
		exit(1);
	}
	
	start_color();
	init_pair(1, COLOR_YELLOW, COLOR_GREEN);
	init_pair(2, COLOR_BLACK, COLOR_WHITE);
	init_pair(3,COLOR_WHITE,COLOR_BLUE);
	clear();
	refresh();
	
	getmaxyx(stdscr, rows, cols);
	
	//Primera ventana
	WINDOW *window = newwin(rows,cols,0,0);
	wbkgd(window, COLOR_PAIR(3));
	box(window, '|', '-');
	
	mvwprintw(window, 10, 10, menu);
	wrefresh(window);
	getch();

	//INICIO DEL JUEGO
	noecho();
	curs_set(FALSE);
	nodelay(stdscr,TRUE);
	
	while(1) {
		//ACTUALIZAMOS LIMITES SEGUN TAMANIO PANTALLA, EN TIEMPO REAL
		//Si al minimizar la pantalla, la bola se queda fuera, para evitar
		//que se produzca error actualizamos su posicion a X=1, y=1
		//Tambien se actualiza de manera proporcional la posicion
		//de la barra del jugador
		getmaxyx(stdscr, rows, cols);
		if(max_x != cols || max_y != rows){
			jugador1x=jugador1x*cols/max_x;		
			jugador2x=jugador2x*cols/max_x;
			jugador2y=rows-2;
			
			max_y=rows;
			max_x=cols;
			if(x>=max_x || y>=max_y){
				x = y = 1;
				direction_x = 1;
				direction_y = 1;
			}
			//Posicion barra del jugador
			if(jugador1x>=max_x-11){
				jugador1x=max_x-12;
			}
			else if(jugador2x>=max_x-11){
				jugador2x=max_x-12;
			}

		}
		
		//Movimiento de la barra del jugador
		mov = getch();
		if(mov == 'd' && jugador1x<max_x-13)
			jugador1x += 3;
		else if(mov == 'a' && jugador1x>2) 
			jugador1x -= 3;
		else if(mov == 'l' && jugador2x<max_x-13) 
			jugador2x += 3;
		else if(mov == 'j' && jugador2x>2) 
			jugador2x -= 3;
			
		//Dibujado de las barras y la pelota
		clear();
		mvprintw(y, x, "o");
		mvprintw(jugador1y, jugador1x, "[LLLL1LLLL]");
		mvprintw(jugador2y, jugador2x, "[TTTT2TTTT]");
		snprintf(marcador,50,"Jug1: %i \nJug2: %i",score1,score2);
		mvprintw(0, 0, marcador);
		refresh();
		
		usleep(DELAY);
		
		next_x = x + direction_x;
		next_y = y + direction_y;
		
		//Si hay colision, la "y" se multiplicara por "-1"
		colision=toca(jugador1x,jugador2x,jugador2y,next_x,next_y,x,y);
		
		if (next_x >= max_x || next_x < 0) {
			direction_x*= -1;
		} else {
			x+= direction_x;
		}
		
		if (next_y >= max_y || next_y < 0 || colision) {
			direction_y*= -1;
			//Puntos score
			if(next_y >= max_y)
				score1+=1;
			else if(next_y < 0)
				score2+=1;
		} else {
			y+= direction_y;
		}
	}
	endwin();
	
	return 0;
}
~~~~

