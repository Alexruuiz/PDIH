# Seminario 5: Manejo del sonido con R

**Trabajo realizado por Alejandro Ruíz Rodríguez **

### REALIZACIÓN DEL SCRIPT

Para este seminario utilizaré Windows 10 e instalaré RStudio y el programa HdX para analizar las cabeceras de los archivos de audio .wav.

Una vez estoy dentro de RStudio, creo un nuevo archivo y comienzo con la realización del script.

#### Instalamos librerías

~~~~
library(tuneR)
library(seewave)
library(audio)
~~~~

#### Leemos archivos

~~~~
perro <- readWave("C:/Users/alexs/Desktop/Universidad/PDIH/S-sonido/Sonidos/perro.wav")
oveja <- readWave("C:/Users/alexs/Desktop/Universidad/PDIH/S-sonido/Sonidos/oveja.wav")
~~~~

Los dos archivos de audio que voy a leer son el de `perro.wav` y `oveja.wav`. Le indicaré la dirección absoluta de cada archivo de sonido a la función.

#### Obtenemos información de cabeceras

Si queremos representar las ondas, primero necesitamos saber ciertas características de ellas, como la frecuencia de muestreo de ellas o el número de muestras:

~~~~
perro
oveja
str(perro)
str(oveja)
~~~~

![Imagen inspección cabeceras](./Capturas/inspeccion.png)

Gracias a estos comandos, podemos obtener información sobre los audios tales como el número de muestras, la duración del audio, la frecuencia de muestreo, si es un sonido mono o estéreo, etc. El comando `str()` visualiza los datos como una cadena de caracteres aunque con la llamada directa al nombre de la variable donde guardamos el audio es suficiente.

#### Dibujamos forma de onda de los sonidos

~~~~
plot( extractWave(perro, from = 1, to = 159732) )
plot( extractWave(oveja, from = 1, to = 19764) )
~~~~

Con este comando puedo dibujar la onda que produce el sonido, indicándole desde que muestra hasta que muestra mostrar. Como del paso anterior obtuvimos el número de muestras totales de cada sonido, simplemente le indicamos desde la muestra 1 al nº total de muestras. Las gráficas serían las siguientes:



**ONDA PERRO.WAV**

<img src="./Capturas/ondaPerro.png" alt="Imagen onda perro" style="zoom: 33%;" />

**ONDA OVEJA.WAV**

<img src="./Capturas/ondaOveja.png" alt="Imagen onda oveja" style="zoom:33%;" />

En las gráficas podemos ver como el audio perro.wav es de canal mono y el audio oveja.wav es estéreo, ya que tiene dos canales.

#### Unión de los dos audios

Para unir los dos audios necesitamos que ambos tengan la misma frecuencia de muestreo, sin embargo, vemos que el audio `perro.wav` tiene una frecuencia de 44100Hz mientras que el audio `oveja.wav` tiene una frecuencia de 8000Hz. Por lo tanto voy a ajustar la frecuencia del audio del perro, reduciéndola de 44100Hz a 8000Hz.

~~~~
perro8000<-resamp(perro,f=44100,g=8000, output="Wave")
perro8000
~~~~

Ahora si visualizamos las características de la variable **perro8000**, podemos ver como su frecuencia se ha reducido a 8000Hz.

<img src="./Capturas/perro8000.png" alt="Imagen perro8000" style="zoom: 80%;" />

Y a continuación podemos unir ambos audios de manera correcta, utilizando el siguiente comando:

~~~~
union <- pastew(perro8000, oveja, output = "Wave")
~~~~

#### Reproducir señal generada y almacenarla en un nuevo fichero WAV

Finamente utilizaremos el comando `listen(union, f=8000)` para escuchar el audio final. Al ejecutarlo nos saldrá el reproductor de Windows y podremos escuchar el sonido de una oveja continuado con el ladrido de un perro. 

Para almacenar el audio resultante en un nuevo archivo, utilizaré el siguiente comando:

~~~~
writeWave(union, file.path("C:/Users/alexs/Desktop/Universidad/PDIH/S-sonido/Sonidos/mezcla.wav") )
~~~~

El archivo se denominará `mezcla.wav` y se guardará en la dirección especificada.



### EXTRAER VALORES DE LOS CAMPOS DE CABECERAS DEL FICHERO `desagradable.wav`

Para esta parte utilizaré el programa recomendado **HdX Hex Editor**. Una vez descargado, lo abro y arrastro el archivo de audio dentro de este, obteniendo lo siguiente:

![Imagen inspección cabeceras](./Capturas/cabeceras2.png)

En la 1ª fila, el primer cuadro corresponde con el **RIFF** (1179011410), el segundo cuadro con el **ChunckSize**=3534596, el tercero con el **WAVE** (1163280727) y el cuarto con el **fmt** (544501094). 

En la 2ª fila, el primer cuadro corresponde con el **ChunckSize**=18, el segundo con el **formato de audio** (1), el tercero con el **número de canales** (2 = estéreo), el cuarto cuadro corresponde con el **SampleRate**(Frecuencia = 44100) y el quinto cuadro corresponde con el **ByteRate** (176400). 

En la 3ª fila, el primer cuadro corresponde con el **BlockAlign** (4) y el segundo cuadro con el **BitsPaerSample** (16).

En la 5ª fila, el primer cuadro corresponde con el **data** (1635017060), el segundo recuadro con el **ChunckSize**=3534524 y el resto con los **bytes de muestras**.

