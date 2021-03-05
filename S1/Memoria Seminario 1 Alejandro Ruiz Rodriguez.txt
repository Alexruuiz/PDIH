# Seminario 1: Programación de dispositivos a bajo nivel

**Trabajo realizado por Alejandro Ruíz Rodríguez **

### 1º Instalamos DOSBox y ejecutamos aplicaciones MS-DOS

Una vez descargado e instalado DOSBox, tendremos que acceder al archivo de configuración, en mi caso localizado en C:\Users\alexs\AppData\Local\DOSBox\dosbox-0.74-3. Una vez dentro del archivo de configuración, nos abajo del todo, debajo de la etiqueta [autexec], y debajo de los comentarios, y añadimos las dos siguientes líneas:

~~~~
keyb sp
MOUNT C C:\Users\alexs\Desktop\Universidad\PDIH\S1
~~~~
La primera sentencia sirve para configurar el teclado español y la segunda para montar en C: el contenido de la carpeta  S1, de manera que esta sea la carpeta raíz en DOSBox. Dentro de esta carpeta vamos a encontrar la carpeta denominada OLDGames, que contendrá los juegos que vamos a probar. Accedo a esta carpeta hasta dirigirme al ejecutable`vball`. Ahora simplemente lo ejecuto escribiendo en DOSBox `VBALL.EXE` y el juego se abrirá en la terminal.

![Imagen ejecucion juego vball.exe](./Capturas/juego.jpg)

### 2º Configuración de DOSBox montando el directorio con el entorno de Borland c

El ejercicio ahora nos pide que cambiemos el path de montaje, de manera que ahora esté situada en la carpeta bc, la cual ya teníamos descargada y colocada dentro de la carpeta PDIH/S1. Por lo tanto debemos modificar la sentencia de montaje anterior, quedado como sigue:
~~~
MOUNT C C:\Users\alexs\Desktop\Universidad\PDIH\S1\bc\bin
~~~
De esta manera ya tendríamos montada la unidad C: en dicha carpeta.

### 3º Crear ejemplo "hola mundo" en ensamblador, comprobar su funcionamiento y modificarlo para que aplique un bucle que muestre el mensaje 7 veces

Dentro de BIN, creamos una carpeta denominada S1, donde colocaremos los archivos ASM. Dentro compilamos el archivo hola.asm y vemos su funcionamiento. 
~~~~
pila segment stack 'stack'
	dw 100h dup (?)
pila ends
datos segment 'data'
	msg db 'hola mundo$'  <---- AQUI MODIFICAMOS EL MENSAJE A MOSTRAR
datos ends
codigo segment 'code'
	assume cs:codigo, ds:datos, ss:pila
	main PROC
		mov ax,datos
		mov ds,ax

		mov dx,OFFSET msg
		mov ah,9
		int 21h

		mov ax,4C00h
		int 21h
	main ENDP
codigo ends

END main
~~~~

Para compilarlo y obtener el ejecutable, usamos un script denominado `c.bat` y cuyo código no pongo ya que está en el guion, se usa de la siguiente manera:
~~~~
c.bat hola
~~~~
Así obtenemos el ejecutable y para ejecutarlo simplemente ponemos su nombre:
~~~~
hola.exe
~~~~
![Imagen ejecucion ejecutable hola.exe](./Capturas/holanormal.jpg)

##### Modificamos archivo "hola" añadiendo el bucle

Para esta modificación utilizaremos el archivo anterior el cual quedará de la siguiente manera **(holaBucle.asm)**:
~~~~
pila segment stack 'stack'
	dw 100h dup (?)
pila ends
datos segment 'data'
	msg db 'hola mundo$'
datos ends
codigo segment 'code'
	assume cs:codigo, ds:datos, ss:pila
	main PROC
		mov ax,datos
		mov ds,ax

        ;imprimir N veces una cadena
        mov cx,0
        bucle:
            mov dx,OFFSET msg
            mov ah,9
            int 21h
            ;actualizar contador y comprobar condicion
            inc cx
            cmp cx,7   <------ Aquí modificamos el número de repeticiones del bucle
            jne bucle

		mov ax,4C00h
		int 21h
	main ENDP
codigo ends

END main
~~~~
Ahora lo compilamos con `c.bat holaBucle` y lo ejecutamos con `holaBucle.exe` y vemos como aparece "hola mundo" repetido 7 veces.

![Imagen ejecucion hola mudo modificado con el bucle](./Capturas/ejecucionBucle.jpg)

---