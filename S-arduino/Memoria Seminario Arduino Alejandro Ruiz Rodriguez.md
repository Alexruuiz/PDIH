# Seminario 3: Simulador Arduino

**Trabajo realizado por Alejandro Ruíz Rodríguez **

*IMPORTANTE: El funcionamiento de los 4 modelos, se muestran en los 4 videos incorporados en la carpeta actual.*

### 1º SIMULADOR UnoArduSim

#### LEDS ROJO Y VERDE CON ENCENDIDO ALTERNO DE 1,5 SEGUNDOS

El código utilizado es el siguiente:

~~~~
void setup()
{
	pinMode(13, OUTPUT);
	pinMode(12, OUTPUT);
}

void loop()
{
	digitalWrite(13,HIGH);
	digitalWrite(12,LOW);
	delay(1500);
	digitalWrite(13,LOW);
	digitalWrite(12,HIGH);
	delay(1500);
}
~~~~

Y el programa quedaría de la siguiente manera:

![Imagen ejercicio 1](./Capturas/2eje1.jpg)

Los leds que aparecen arriba a la derecha, están conectados con las entradas 12 y 13 y con el color puesto en rojo (R) y verde (G) respectivamente.

#### LED CON PULSADOR

El código utilizado es el siguiente:

~~~~
void setup()
{
	pinMode(13, OUTPUT);
	pinMode(7, INPUT);
}

void loop()
{
	val=digitalRead(7);
	digitalWrite(13,val);
}
~~~~

Y el programa quedaría de la siguiente manera:

![Imagen ejercicio 2](./Capturas/2eje2.jpg)

El led rojo está conectado a la entrada 13 y el pulsador "push" está conectado a la entrada 7.



### 2º SIMULADOR TINKERCAD

#### LEDS ROJO Y VERDE CON ENCENDIDO ALTERNO DE 1,5 SEGUNDOS

El código utilizado es el siguiente:

~~~~
void setup()
{
	pinMode(13, OUTPUT);
	pinMode(12, OUTPUT);
}
void loop()
{
	digitalWrite(13,HIGH);
	digitalWrite(12,LOW);
	delay(1500);
	digitalWrite(13,LOW);
	digitalWrite(12,HIGH);
	delay(1500);
}
~~~~

Y el programa quedaría de la siguiente manera:

![Imagen ejercicio 1](./Capturas/ejer1.jpg)

En la fila 2 de la placa he situado la toma a tierra, las resistencias son de 220 Ohmios, y el led rojo esta conectado en la entrada 13 y el led verde en la entrada 12.

#### LED CON PULSADOR

El código utilizado es el siguiente:

~~~~
void setup()
{
	pinMode(13, OUTPUT);
	pinMode(7, INPUT);
}

void loop()
{
	val=digitalRead(7);
	digitalWrite(13,val);
}
~~~~

Y el programa quedaría de la siguiente manera:

![Imagen ejercicio 2](./Capturas/ejer2.jpg)

En este modelo, el botón deja pasar corriente si es pulsado, suministrándole en tal caso energía al led rojo, conectado en la entrada 13.