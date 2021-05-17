# Práctica 5: Experimentación con el sistema de salida de sonido

**Trabajo realizado por Alejandro Ruíz Rodríguez **

### REALIZACIÓN DEL SCRIPT

Para esta práctica utilizaré Windows 10 junto con RStudio. Una vez dentro de RStudio, crearé un nuevo archivo y comenzaré con la creación del script.

#### Instalamos librerías

~~~~
library(tuneR)
library(seewave)
library(audio)
~~~~

#### Leemos archivos

~~~~
perro <- readWave("C:/Users/alexs/Desktop/Universidad/PDIH/P5/Sonidos/perro.wav")
gato <- readMP3("C:/Users/alexs/Desktop/Universidad/PDIH/P5/Sonidos/gato.mp3")
~~~~

Los dos archivos de audio que voy a leer son el de `perro.wav` y `gato.wav`. Le indicaré la dirección absoluta de cada archivo de sonido a la función.

#### Obtenemos información de cabeceras

Si queremos representar las ondas, primero necesitamos saber ciertas características de ellas, como la frecuencia de muestreo de ellas o el número de muestras:

~~~~
perro
gato
str(perro)
str(gato)
~~~~

<img src="./Capturas/inspeccionCab.png" alt="Imagen inspección cabeceras" style="zoom:60%;" />

Gracias a estos comandos, podemos obtener información sobre los audios tales como el número de muestras, la duración del audio, la frecuencia de muestreo, si el canal es mono o estéreo, etc. El comando `str()` visualiza los datos como una cadena de caracteres aunque con la llamada directa al nombre de la variable donde guardamos el audio es suficiente.

#### Dibujamos forma de onda de los sonidos

~~~~
plot( extractWave(perro, from = 1, to = 159732) )
plot( extractWave(gato, from = 1, to = 393984) )
~~~~

Con este comando puedo dibujar la onda que produce el sonido, indicándole desde que muestra hasta que muestra mostrar. Como del paso anterior obtuvimos el número de muestras totales de cada sonido, simplemente le indicamos desde la muestra 1 al nº total de muestras. Las gráficas serían las siguientes:



**ONDA PERRO.WAV**

<img src="./Capturas/ondaPerro.png" alt="Imagen onda perro" style="zoom: 33%;" />

**ONDA GATO.WAV**

<img src="./Capturas/ondaGato.png" alt="Imagen onda oveja" style="zoom:33%;" />

En las gráficas podemos ver como el audio perro.wav es de canal mono y el audio gato.wav es estéreo, ya que tiene dos canales.

#### Unión de los dos audios

Para unir los dos audios necesitamos que ambos tengan la misma frecuencia de muestreo. Podemos comprobar que el audio `perro.wav` tiene una frecuencia de 44100Hz y que el audio `oveja.wav` tiene una frecuencia de 44100Hz, por lo tanto no hará falta llevar a cabo ningún tipo de ajuste. Por lo tanto, unimos ambos audios utilizando el siguiente comando:

~~~~
union <- pastew(perro, gato, output = "Wave")
~~~~

#### Dibujamos la onda resultante

Primero obtengo el número de muestras del audio **union**:

<img src="./Capturas/union.png" alt="Imagen onda perro" style="zoom: 67%;" />

Ahora voy a dibujar la onda resultante de la unión anterior. Para ello simplemente utilizo el siguiente comando, indicándole que quiero mostrar todas las muestras:

~~~~
plot( extractWave(union, from = 1, to = 553716) )
~~~~

<img src="./Capturas/ondaUnion.png" alt="Imagen inspección cabeceras" style="zoom: 50%;" />

#### Eliminamos las frecuencias entre 10.000 y 20.000 Hz

Para esta parte utilizo el siguiente comando:

~~~~
union2 <- bwfilter(union,f=44100, channel=1, n=1, from=10000, to=20000, bandpass=TRUE, listen = FALSE, output = "Wave")
~~~~

Con este comando puedo pasarle un filtro al audio y eliminar las frecuencias contenidas entre un cierto rango indicado. El archivo de audio quedaría tal que así:

<img src="./Capturas/eliminarFrec.png" alt="Imagen inspección cabeceras" style="zoom: 50%;" />

#### Almacenar la señal obtenida en mezcla.wav

Para almacenar el audio resultante en un nuevo archivo, utilizaré el siguiente comando:

~~~~
writeWave(union2,file.path("C:/Users/alexs/Desktop/Universidad/PDIH/P5/Sonidos/mezcla.wav") )
~~~~

El archivo se denominará `mezcla.wav` y se guardará en la dirección absoluta especificada.

#### Cargar nuevo archivo de sonido, aplicarle eco, invertirlo y almacenarlo

Para esta última parte voy a cargar el archivo de audio denominado `hola.wav`:

~~~~
hola <- readWave("C:/Users/alexs/Desktop/Universidad/PDIH/P5/Sonidos/hola.wav")
~~~~

A este audio le vamos a aplicar un eco utilizando el comando `echo()`:

~~~~
holaEco <- echo(hola,f=22050,amp=c(0.8,0.4,0.2),delay=c(1,2,3), output="Wave")
holaEco@left <- 10000 * holaEco@left
~~~~

A continuación lo invertimos utilizando el comando `revw()`:

~~~~
alreves <- revw(holaEco, output="Wave")
~~~~

Y finalmente los almacenamos en un nuevo archivo denominado **alreves.wav**:

~~~~
writeWave(alreves,file.path("C:/Users/alexs/Desktop/Universidad/PDIH/P5/Sonidos/alreves.wav") )
~~~~

#### Representación gráficas de hola, holaEco y alreves.wav respectivamente

**GRÁFICA HOLA.WAV**

<img src="./Capturas/hola.png" alt="Imagen inspección cabeceras" style="zoom: 50%;" />

**GRÁFICA HOLAECO**

<img src="./Capturas/holaEco.png" alt="Imagen inspección cabeceras" style="zoom: 50%;" />

**GRÁFICA ALREVES.WAV**

<img src="./Capturas/holaalreves.png" alt="Imagen inspección cabeceras" style="zoom: 50%;" />

En esta tres gráficas podemos ver la transformación de la señal hola, pasando primero por la adición de eco y posteriormente sometiéndola a una inversión.