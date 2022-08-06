# Git

## Introducción

Git es un software de control de versiones. Este guarda toda la información de todos los cambios que se hacen en un proyecto: Quién, qué, dónde, cuándo y por qué. Se trata de una versión mejorada del CVS, donde hay un único repositorio del proyecto.

En Git, aparte del repositorio central hay uno para cada integrante. De cada uno de los repositorios de los usuarios se crea una copia física en el disco local sobre la que se trabaja. En caso de que se rompiese el disco duro se perdería la copia local pero no el repositorio propio.

## Ramas

En Git se trabaja por ramas, siendo master la principal, a la que se añaden ramas adicionales para cada una de las tareas. En la rama master no se debe hacer commit. Las ramas se crean en nuestro repositorio, y al cambiar de rama se actualiza el contenido de la copia local para poder trabajar localmente con la rama actual. Se pueden también hacer ramas a partir de otras ramas y se pueden hacer también a partir de versiones antiguas del proyecto.

Las ramas se pueden ver como líneas temporales, siendo master la principal. En el momento de crear una rama, se crea paralelamente otra línea temporal. En el caso de que queramos hacer definitivos los cambios podemos combinar la rama con la master, haciendo que la línea temporal de la rama vuelva a integrarse en la principal.

## Acciones

- **Commit**: Cuando al trabajar en nuestra copia hemos hecho algún progreso y queremos actualizarlo en nuestro repositorio. Es recomendable hacer un commit por cada archivo, documentando los cambios.
- **Push**: Para actualizar en el repositorio central los archivos modificados de nuestro repositorio. Es recomendable hacer un único push con todos los archivos modificados. Dará error si antes otra persona ha hecho una modificación de la misma parte del proyecto y no hemos trabajado con el proyecto actualizado con esta modificación.
- **Pull**: Para actualizar el repositorio local a partir del central y trabajar con la última versión del proyecto. Se debe hacer para cuando alguien ha actualizado el contenido del repositorio central.
- **Revert**: Para obtener una nueva copia de nuestro repositorio local en caso de que nos hayamos equivocado trabajando con nuestra copia.
- **Update**: Para comprobar si alguien ha actualizado el repositorio central.
- **Merge**: Para volver a unir dos ramas.

## GitHub

GitHub es una página para almacenar proyectos usando Git. Para tener un repositorio en GitHub y la copia local en nuestro ordenador necesitamos el programa Git. En el terminal de Windows, el comando `git --version` nos permite comprobar que el programa está correctamente instalado.

Una vez creada la cuenta en GitHub, en el icono de usuario de arriba a la derecha le damos a *Settings* -> *SSH and GPG keys* -> *New SSH Key*. Aquí añadimos la clave pública que creamos en el primer día con PuttyGen.

Para crear un repositorio, en el panel de la izquierda le damos a New. Aquí especificamos el nombre, añadimos un archivo README.md y elegimos la licencia Apache License 2.0.

El archivo README.md es el que se muestra por defecto al abrir el repositorio, y también se abrirá por defecto si ponemos uno dentro de una carpeta. Este tipo de archivo se llama Markdown, es un archivo de texto plano con [algunas mejoras](https://www.markdownguide.org/cheat-sheet/) y puede ser editado con algún programa como Typora.

Al entrar en un repositorio, el icono de lápiz permite editar el archivo README.md creando para ello un commit. En el apartado de insights se muestra un historial de todos los commit.

## GitKraken

GitKraken es un programa que ofrece una interfaz gráfica para utilizar Git. Primero nos registramos con la cuenta de GitHub y en *File* -> *Preferences* -> *SSH* añadimos los archivos de claves privada y pública que creamos con PuttyGen. Luego le damos a Clone a repo: Elegimos la opción de GitHub.com, la carpeta de nuestro PC donde queremos hacer la copia física del repositorio y el repositorio que queremos clonar.

En la pantalla principal se muestra en vertical las distintas líneas temporales. En *File* -> *Open in File Manager* se abre la carpeta donde está la copia local. Cuando en esta hagamos modificaciones, saldrá al final de la línea temporal una línea que pone WIP que indica que hay cambios. Al darle, en el panel de la derecha se muestra los archivos que se han creado, borrado o modificado. Para cada archivo del que queramos confirmar el cambio le damos a *Stage file* o si lo pulsamos muestra los cambios que se han hecho. Añadimos el mensaje del commit y ya lo podemos crear.

En los iconos superiores, creamos una rama con *Branch* y con *Push* hacemos un push en la rama nueva creada. Al entrar en GitHub, aparecerá una pull request y al aceptarla permitirá unir la rama en al que se ha hecho push con la rama principal. Al crear la rama en GitKraken se verán las dos líneas temporales.

