# Regular expressions

Para manipular cadenas. Se usa el módulo re, ya incluido. 

### Comprobar si una cadena comienza por cierta subcadena

```python
import re
cadena = r"Hola"

if re.match(cadena, "HolaXX"):
    print("Match") # 'Match'
else:
    print("No match")

if re.match(cadena, "XXHola"):
    print("Match")
else:
    print("No match") # 'No match'
```

### Comprobar si en una cadena hay una subcadena

Da igual si aparece más de una vez

```python
import re
pattern = r"Hola"

if re.search(pattern, "QueNoHolaTio"):
    print("Match") # 'Match'
else:
    print("No match")
```

Al resultado podemos aplicarle varios métodos, **group()** devuelve la string del match, **start()** y **end()** las posiciones iniciales y finales del match y span() devuelve las dos posiciones en forma de tupla.

```python
import re
pattern = r"Hola"

match = re.search(pattern, "QueNoHolaTio")
if match:
    print(match.group()) # 'Hola'
    print(match.start()) # 5
    print(match.end()) # 9
    print(match.span()) # (5, 9)
```

### Buscar todas las subcadenas que siguen un patrón

Se puede usar re.findall() que devuelve el resultado en forma de lista, o re.finditer() que lo devuelve en forma de iterador.

```python
import re
pattern = r"spam"

re.findall(pattern, "eggspamsausagespam") # ['spam', 'spam']
```

### Buscar y reemplazar una subcadena

Cambia todas las subcadenas de una cadena por otra subcadena elegida.

```python
import re

cadena = "Hola que tal. Hola tio."
pattern = r"Hola"
cadena2 = re.sub(pattern, "Buenas", cadena) 
cadena2 # 'Buenas que tal. Buenas tio.'
```

Se puede especificar que haga el cambio cierto número de veces

```python
import re

cadena = "Hola que tal. Hola tio."
pattern = r"Hola"
cadena2 = re.sub(pattern, "Buenas", cadena, count = 1) 
cadena2 # 'Buenas que tal. Hola tio.'
```

### Metacarácteres

Permite crear varias combinaciones de texto para usarlas como patrones en las funciones. Para evitar problemas, al escribir las combinaciones usar cadenas raw:

`.` representa cualquier carácter menos una nueva línea.

```python
import re
pattern = r"X..X"

re.findall(pattern, "xxX1XxxX22Xxx") # ['XxxX']
```

`^` y `$` representan el comienzo y final de una cadena, respectivamente.

En el ejemplo siguiente, estamos definiendo que la cadena a buscar debe comenzar por XX y acabar por YY. Y entre medias, un único carácter que puede ser cualquiera.

```python
import re

pattern = r"XX.YY$"
if re.search(pattern, "xxxxxxxXX1YY"):
    print("Match 1")

pattern = r"^XX.YY"
if re.search(pattern, "xxxxxxxXX1YY"):
    print("Match 2")

pattern = r"^XX.YY$"
if re.search(pattern, "XX1YY"):
    print("Match 3")
```

`[]` representa una clase, un conjunto de caracteres definidos dentro de los corchetes, valiendo cualquiera de ellos como carácter.

```python
import re
pattern = r"[AEIOU]"

re.findall(pattern, "xxAxxExxOAx") # ['A', 'E', 'O', 'A']
```

También se puede definir dentro de los corchetes rangos de caracteres, por ejemplo:

- `[a-z]` cualquier letra minúscula
- `[G-P]` cualquier letra mayúscula de la G a la P
- `[0-9]` cualquier número
- `[\x41-\x4F]` caracteres de la tabla ASCII desde el 0x41 (A) al 0x4F (O)

Se puede también definir varios rangos, como:

- `[A-Za-z]` cualquier letra minúscula o mayúscula
- `[A-Za-z0-9]` cualquier letra minúscula o mayúscula o cualquier número

Para permitir todo lo demás menos ese rango, poner `^` delante, por ejemplo:

- `[^A-Z]` todo menos cualquier letra minúscula o mayúscula
- `[^A-Za-z]` todo menos cualquier letra minúscula o mayúscula

`*`, `+`, `?` y `{}` sirven para especificar el número de repeticiones. 

`*` significa 0 o más repeticiones de lo que hay antes, que puede ser un carácter, una clase, o un grupo de caracteres (entre paréntesis).

```python
import re
pattern = r"X(YZ)*"

if re.match(pattern, "X"):
    print("Match 1") # 'Match 1'

if re.match(pattern, "XYZ"):
    print("Match 2") # 'Match 2'

if re.match(pattern, "YZ"):
    print("Match 3")
```

`+` significa 1 o más repeticiones de lo que hay antes, que puede ser un carácter, una clase, o un grupo de caracteres (entre paréntesis).

```python
import re
pattern = r"X(YZ)+"

if re.match(pattern, "X"):
    print("Match 1")

if re.match(pattern, "XYZ"):
    print("Match 2") # 'Match 2'

if re.match(pattern, "XZ"):
    print("Match 3")
```

`?` significa 0 o 1 repeticiones de lo que hay antes, que puede ser un carácter, una clase, o un grupo de caracteres (entre paréntesis).

```python
import re

pattern = r"X(Y)?Z"

if re.match(pattern, "XZ"):
    print("Match 1") # Match 1

if re.match(pattern, "XYZ"):
    print("Match 2") # Match 2

if re.match(pattern, "XYYZ"):
    print("Match 3")
```

`{}` permiten elegir el número de repeticiones de lo que hay antes, que puede ser un carácter, una clase, o un grupo de caracteres (entre paréntesis). Este número puede ser fijo o un rango. Si en el rango se omite alguno de los dos números se toma por 0 o por infinito.

```python
import re

pattern = r"X{1,3}$"

if re.match(pattern, "X"):
    print("Match 1") # Match 1

if re.match(pattern, "XXX"):
    print("Match 2") # Match 2

if re.match(pattern, "XXXX"):
    print("Match 3")
```

Por tanto: `{0,}` es `*`, `{1,}` es `+` y `{0,1}` es `?`.

Otro metacarácter es `|` que funciona como or.

```python
import re
pattern = r"gr(a|e)y"

match = re.match(pattern, "gray")
if match:
    print ("Match 1") # Match 1

match = re.match(pattern, "grey")
if match:
    print ("Match 2") # Match 2

match = re.match(pattern, "griy")
if match:
     print ("Match 3")
```

### Grupos

Como se ha visto antes, un grupo es un conjunto de caracteres entre paréntesis simples. Al hacer un match, la función **group()** nos permite ver los diferentes grupos en el match, los cuales se han definido en el patrón: **group(1)** o más para ver grupo a grupo y **groups()** para ver una lista de todos.

```python
import re

pattern = r"a(bc)(de)(f(g)h)i"

match = re.match(pattern, "abcdefghijklmnop")
if match:
match.group() # 'abcdefghi'
match.group(0) # 'abcdefghi'
match.group(1) # 'bc'
match.group(3) # 'fgh'
match.group(4) # 'g'
match.groups() # ('bc', 'de', 'fgh', 'g')
```

Hay varios tipos de grupos, entre ellos los named groups y los non-capturing groups. 

Los named groups tienen el formato **(?P\<name\>...)**. \<name\> es el nombre que le queramos poner y ... el contenido. Funciona igual que un grupo normal pero podemos usar el nombre para indexar el grupo al usar **group(name)**.

Los non-capturing groups tienen el formato **(?:...)**, y no son accesibles con el método group, no ocupando un puesto en el índice del método.

```python
import re
pattern = r"(?P<first>abc)(?:def)(ghi)(jkl(?:mno))"

match = re.match(pattern, "abcdefghijklmno")
if match:
match.group("first") # 'abc'
print(match.groups()) # ('abc', 'ghi', 'jklmno')
```

### Secuencias especiales

Se escriben con un backslash (\\) seguido de otro carácter: Puede ser por ejemplo un número del 1 al 99, que copia el contenido del grupo dentro de esa expresión cuya posición es la del numero elegido. Por ejemplo, `\2` se copiará lo que haya en el segundo grupo de la regex en la que se encuentre.

```python
import re
pattern = r"(XX)(YY)\1\2"

match = re.match(pattern, "XXYYXXYY")
if match:
    print ("Match 1") # Match 1
```

Otros casos:

- `\d` se refiere a cualquier dígito (0 a 9)
- `\s` se refiere a un espacio en blanco
- `\w` se refiere a cualquier carácter de palabra (a-Z, 0-9 y _)

Si se ponen en mayúscula, se refieren a todo menos al significado original. Por ejemplo, `\S` se refiere a todo menos un espacio en blanco.

```python
import re
pattern = r"(\D+\d$)"

match = re.match(pattern, "xxx1")
if match:
    print("Match 1") # Match 1

match = re.match(pattern, "1xxx1")
if match:
    print("Match 2")
```

Otros casos:

- `\A` para señalar que debe ser el principio de la cadena
- `\Z` para señalar que debe ser el final de la cadena
- `\b` para referirse a que al otro lado no hay una carácter de los de `\w`
- `\B` para referirse a que al otro lado haya una carácter de los de `\w`

En el caso anterior buscamos la subcadena cat pero que no tengo pegada a ningún lado una letra, número o _.

```python
import re

pattern = r"\b(cat)\b"

match = re.search(pattern, "The cat sat!")
if match:
    print ("Match 1") # Match 1

match = re.search(pattern, "We s=cat<tered?")
if match:
    print ("Match 2") # Match 2

match = re.search(pattern, "We scattered.")
if match:
    print ("Match 3")
```

### Leer una cadena en modo multilínea

Podemos usar el modificador `re.MULTILINE` para que los metacharacters `^` y `$` pueden hacer match no solo al principio y final de la cadena si no también al principio y final de cada línea. `\A` y `\Z` seguirán funcionando solo en el principio y final de la cadena.

```python
re.findall('$#', cadena, re.MULTILINE)
```

### Extracción de un correo

Con `[\w\.-]` estamos admitiendo: Letra, número, punto (Con \ delante para que no se interprete como un metacharacter) y guiones alto y bajo.

Con `([\w\.-]+)` buscamos una cadena de mínimo un carácter de longitud que solo tengo los caracteres antes vistos (USERNAME).

Con `@` buscamos que justo después de esa cadena hay un símbolo arroba (ARROBA).

Con `([\w\.-]+)` estamos buscando después del arroba otra vez una cadena como la del principio (NOMBRE DEL DOMINIO).

Por último, con `(\.[\w\.]+)` buscamos una última cadena como las otras dos, pero que comience por un punto (SUFIJO DEL DOMINIO).

Si un texto tuviese varios correos, se usaría **re.findall()**, si no con **re.search()** es suficiente.

```python
import re
cadena = 'Contacte con sergisastre22@outlook.com para pedir una cita'
pattern = r'([\w\.-]+)@([\w\.-]+)(\.[\w\.]+)'

match = re.search(pattern, cadena)
if match:
    print(match.group()) # 'sergisastre22@outlook.com'
```

