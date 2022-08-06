# Unix

## Introducción

Los sistemas operativos Unix, así como los SO Linux que están basados en los primeros, se caracterizan por:

- Sistema de ficheros jerárquico
- Ficheros de texto para guardar incluso imágenes, audio, etc.
- Tratar memorias y periféricos como ficheros de texto

En un SO basado en Unix se usan comandos para moverse por el sistema de ficheros. / es el directorio raíz y dentro de este hay siempre unas carpetas características, entre ellas:

- **bin**: Se guardan todos los comandos que se pueden ejecutar. Al usar un comando, se ejecuta el fichero de dicho comando.
- **lib**: Donde se guardan las librerías.
- **home**: Localización al arrancar.
- **root**: Carpeta de usuario

Un comando está formado por tres partes: Primero el nombre del comando (La instrucción a realizar), luego los parámetros (Instrucciones de como se debe realizar la instrucción y precedidos por un signo -) y finalmente los argumentos (Sobre que se debe realizar la instrucción y que se pueden poner más de uno). 

Si no se indica un argumento, el comando se ejecutará sobre la ruta actual. Si no queremos eso, se debe indicar la ruta absoluta o relativa de la ruta o archivo sobre el que se quiere actuar.

Poner dos puntos (..) al escribir una ruta sirve para subir al directorio superior del que nos encontramos. Con estos se **pueden** comenzar las rutas relativas.

Las flechas superior e inferior del teclado sirven para copiar los últimos comandos escritos.

Si un comando se queda colgado, CTRL + C cancela la ejecución.

Al escribir / en una ruta, el tabulador va autorellenando las opciones posibles dentro de esa ruta.

En https://bellard.org/jslinux/ se puede usar un terminal para hacer pruebas. Utilizamos la versión de consola de Alpine, que es una distribución.

A la izquierda de donde se escriben los comandos se encuentra el prompt. En esta distribución muestra quien es el host, y al aparecer *localhost* significa que el host es el ordenador local. No obstante, aquí pueden salir otra información.

## Comandos

### Conocer ruta actual

El comando **pwd** devuelve en que lugar del directorio nos encontramos.

`pwd`

### Conocer quien somos

El comando **whoami** imprime el nombre del usuario de quien ejecuta el comando.

`whoami`

### Ver contenido de un directorio

El comando **ls** muestra el contenido de un directorio, es decir, las carpetas y archivos que se encuentran en ella (En verde los ficheros y en azul los directorios). Permite varios parámetros, entre ellos:

- **l**: Muestra una vista detalla del contenido. El primer carácter de cada fila muestra el tipo de fichero: - para fichero regular o d para directorio. También muestra el propietario(3ª fila), el tamaño en bits (5ª fila) y la fecha de la última modificación (6ª fila). 
- **t**: Ordena los archivos por última modificación.
- **a**: Incluye los archivos ocultos.

Para poner a la vez varios parámetros, se puede también poner un único signo - y todas las letras de los parámetros que queramos.

`ls`

`ls -l`

`ls -lt`

`ls -l -t`

Sobre todo en caso de haber muchos ficheros, podemos usar el * que sustituye cualquier cadena de caracteres.

`ls pa`

`ls *.txt`

También se pued ejecutar el comando sobre un directorio que esté dentro del que nos encontremos actualmente. Por ejemplo, dentro del directorio raíz podemos hacer:

`ls bin`

`ls root`

### Obtener ayuda de un comando

Para obtener ayuda acerca del uso de un comando, basta con escribir **--help** luego de este para que se imprima la ayuda necesaria.

`ls --help`

`whoami --help`

### Historial de comandos usados

El comando **history** muestra por orden los comandos que se han usado.

`history`

### Moverse entre los directorios

El comando **cd** sirve para subir, bajar o saltar de directorio.

`cd ..`

`cd /root`

`cd root`

`cd ../bin`

### Crear un fichero

El comando **touch** crea un fichero. Se puede crear donde queramos independientemente de donde estemos.

`touch /root/prueba.txt`

`touch ../home/archprueba.py`

`touch pr3.txt`

### Borrar un fichero

El comando rm borra un fichero. Usando * se pueden borrar varios a la vez. No hay comando de deshacer así que hay que estar seguros.

`rm /root/prueba.txt`

`rm ../home/*.py`

`rm \*prueba*`

### Crear una carpeta

El comando **mkdir** crea una carpeta.

`mkdir carpeta2`

`mkdir ../home/carpeta3`

### Borrar una carpeta

El comando **rmdir** permite eliminar directorios solo si están vacíos. El comando **rm** permite eliminar el directorio y todo el contenido que haya dentro. Para ello, hay que añadir los parámetro **rf**.

`rmdir carpeta2`

`rm -rf ../carpeta1`

`rm -rf ../carpeta*`

### Copiar archivo o carpeta

El comando **cp** permite copiar un archivo o carpeta. Para ello, se especifica la ruta de lo que queremos copiar y la ruta de donde lo queremos copiar. Se puede elegir si cambiar el nombre del archivo, y si ya existe se sobrescribe.

`cp carpeta1/hola.txt carpeta2`

`cp carpeta1/hola.txt carpeta2/quepasa.txt`

### Mover archivo o carpeta

El comando **mv** funciona igual que el comando de copiar, pero en este caso el archivo de origen se elimina. Usando el dos casos el mismo directorio, se puede usar el comando para cambiar de nombre el archivo.

`mv carpeta1/hola.txt carpeta2`

`mv hola.txt adios.txt`

### Escribir en un archivo

El comando **echo** permite escribir en un archivo. Si no se especifica ningún archivo, devuelve lo escrito en la pantalla.

`echo Hola que tal > carpeta/hola.txt` 

`echo hola.txt adios.txt`

Otra opción más completa es el comando **vi** que abre el archivo en una especie de editor de textos. Una vez hemos editado el archivo, pulsamos ESC y escribimos **:q** para salir, **:w** para guardar y **:wq** para ambas. Otro editor similar se obtiene con el comando **nano**.

`vi hola.txt`

`nano hola.txt`

### Mostrar el contenido de un archivo

El comando **cat** muestra el contenido de un archivo pero sin ejecutarlo. En caso de que el archivo tengo muchas líneas, el comando **tail** permite mostrar solo las últimas 10 líneas.

`cat carpeta3/hola.txt`

`tail hola.txt`

### Permisos de archivos

La primera columna que devuelve el comando `ls -l` muestra los permisos que se tiene sobre el fichero, que pueden ser de lectura (r), escritura (w) y ejecución (x). El primer carácter muestra si el fichero es o no un directorio, los tres siguientes los permisos del propietario, los tres siguientes los de grupo y los tres últimos los de los demás.

En el caso de `-rw-r--r--` el propietario tiene permiso de escritura y todo el mundo de lectura. En el caso de `-rwxrwxrwx` todo el mundo tiene todos los derechos.

El comando **chmod** permite cambiar los permisos de un fichero. El primer argumento es un número que define que permisos se conceden (Se puede calcular en [Chmod Calculator](https://chmod-calculator.com/)) y el segundo argumento los ficheros afectados.

`chmod 777 hola.txt`    `chmod 644 ../carpeta2/readme.txt`

### Obtener permisos de administrador

En los casos que no seamos el administrador usando un terminal, habrá ciertas ordenes que no podremos ejecutar. El comando **sudo** permite obtener estos permisos hasta que se anule con el comando **exit**.

`sudo su`

`exit`

Otra opción es escribir **sudo** al principio de un comando, que ejecutará como administrador ese único comando.

`sudo chmod 777 hola.txt`

### Conocer las unidades de disco

El comando **df** muestra la ocupación de las distintas unidades de disco conectadas, siendo /dev/sda1 en la que nos encontramos. Cada unidad de disco o periférico es una de las columnas mostradas. El parámetro **h** muestra la capacidad en MB.

`df -h`

Ver procesos activos

El comando **ps** muestra una lista de los procesos activos.

`ps`

### Instalar programas

Primero debemos ejecutar un comando específico para mostrar la última versión de los paquetes disponibles para instalar.

`sudo apt update`

Para instalar un programa fuera de la lista, luego de ejecutar el comando anterior, hay que conocer el nombre del paquete en internet. Por ejemplo, para descargar java el nombre es **default-jdk**. Con ello, se ejecuta el siguiente comando.

`sudo apt install default-jdk`

### Conocer ubicación de los programas

El comando **which** permite conocer la ubicación de un programa instalado.

`which java`

### Conocer versión de un programa

Para conocer la versión de un programa, se debe escribir **--version** después del nombre del programa

`java --version`

## Bash

Bash es un lenguaje que permite programar en la terminal y que se puede practicar con un [compilador online](https://www.onlinegdb.com/online_bash_shell).
