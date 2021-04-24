# Practica 3: Simulador Arduino

**Trabajo realizado por Alejandro Ruíz Rodríguez **

*IMPORTANTE: El funcionamiento de los 3 modelos, se muestran en los 3 videos incorporados en la carpeta actual.*

### TINKERCAD

Para esta práctica, utilizaré el simulador "Tinkercad".

### 1º LEDS ROJO, VERDE Y AMARILLO CON ENCENDIDO ALTERNO DE 1,5 SEGUNDOS

El código utilizado es el siguiente:

~~~~
void setup()
{
	pinMode(13, OUTPUT);
    pinMode(12, OUTPUT);
    pinMode(11, OUTPUT);
}

void loop()
{
    digitalWrite(13, HIGH);
    digitalWrite(12, LOW);
    digitalWrite(11, LOW);
    delay(1500);
    digitalWrite(13, LOW);
    digitalWrite(12, HIGH);
    digitalWrite(11, LOW);
    delay(1500);
    digitalWrite(13, LOW);
    digitalWrite(12, LOW);
    digitalWrite(11, HIGH);
    delay(1500);
}
~~~~

Y el programa quedaría de la siguiente manera:

![Imagen Ejercicio 1](./Capturas/parte1Arduino.jpg)

En este modelo simplemente hemos añadido 3 leds de 3 colores diferentes, las cuales se activan de manera alterna, con esperas de 1,5 segundos entre cada una. Las resistencias son de 220 ohmios.

### 2º LEDS ROJO, VERDE Y AMARILLO CON ENCENDIDO ALTERNO DE 1,5 SEGUNDOS Y BOTON PARA ENCENDER ROJO

El código utilizado es el siguiente:

~~~~
int val;

void setup()
{
	pinMode(13, OUTPUT);
    pinMode(12, OUTPUT);
    pinMode(11, OUTPUT);
    pinMode(7, INPUT);
}

void loop()
{
  val=digitalRead(7);
  while(!val){ 
  	val=digitalRead(7);
  	if(val) break;
    digitalWrite(13, HIGH);
    digitalWrite(12, LOW);
    digitalWrite(11, LOW);
  	val=digitalRead(7);
  	if(val) break;
    delay(1500);
  	val=digitalRead(7);
  	if(val) break;
    digitalWrite(13, LOW);
    digitalWrite(12, HIGH);
    digitalWrite(11, LOW);
  	val=digitalRead(7);
  	if(val) break;
    delay(1500);
  }
  
  digitalWrite(11, HIGH);
  digitalWrite(12, LOW);
  digitalWrite(13, LOW);
  delay(1500);
}
~~~~

Y el programa quedaría de la siguiente manera:

![Imagen ejercicio 2](./Capturas/parte2Arduino.jpg)

En este modelo, el botón deja pasar corriente si es pulsado, suministrándole en tal caso energía a la entrada 7,  y por lo tanto se activaría la variable val. En el código utilizo esta variable para entrar o no en un bucle while. Si está desactivada, me quedo en un bucle que solo enciende alternativamente las luces verde y amarilla. Si se activa la variable val, se ejecuta un break (para dar más rapidez al proceso) y me salgo del while. Fuera del while, y sin poder entrar, estaríamos encendiendo la luz led roja, hasta que soltemos el botón, desactivándose la variable val. Las resistencias son de 220 ohmios.

### 3º EJERCICIO EXTRA LEDS DEL COCHE FANTÁSTICO

El código utilizado es el siguiente:

~~~~
int val;
int val2;
int lento=160;
int rapido=50;

void setup()
{
  pinMode(13, OUTPUT);
  pinMode(12, OUTPUT);
  pinMode(11, OUTPUT);
  pinMode(10, OUTPUT);
  pinMode(7, INPUT);
}

void loop()
{
  val2=digitalRead(7);
  if(val2)
    val=rapido;
  else
    val=lento;
  
  for(int i=10 ; i<14 ; i++){
    digitalWrite(13, LOW);
    digitalWrite(12, LOW);
    digitalWrite(11, LOW);
    digitalWrite(10, LOW);
    digitalWrite(i, HIGH);
    delay(val);
  }
  delay(val*2);
  
  val2=digitalRead(7);
  if(val2)val=rapido;
  else val=lento;
  
  for(int i=13 ; i>9 ; i--){
    digitalWrite(13, LOW);
    digitalWrite(12, LOW);
    digitalWrite(11, LOW);
    digitalWrite(10, LOW);
    digitalWrite(i, HIGH);
    delay(val);
  }
  delay(val*2);
  
}
~~~~

Y el programa quedaría de la siguiente manera:

![Imagen ejercicio 2](./Capturas/EJERCICIOeXTRA.jpg)

En este modelo he implementado 4 luces leds que se van activando una después de otra, yendo siempre de la mano. Además, le he añadido un interruptor deslizante, el cual varía la velocidad de movimiento de las luces led. Para ello simplemente he hecho que la variable de velocidad global se actualice cada vez que se lea de la entrada número 7. Si está activa, se actualiza el valor del delay a un número bajo, por lo que se acelera el movimiento de las leds, y si la entrada número 7 está apagada, se actualiza el valor del delay a un número alto, por lo que el movimiento de las luces leds es más lento.  Las resistencias son de 220 ohmios.