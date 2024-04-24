# Data structures

## Strings

Las cadenas son inmutables, el modificarlas lo que hará es crear una cadena nueva.

### Access parts of a string

Sequence of characters. Each one has his index, starting from 0

```python
string = 'How are you'
string[0] # 'H'
string[0:2] # 'Ho'
```

There are also negatives index, to access from the end, starting from -1.

```python
string = 'How are you'
string[-1] # 'u'
string[1:-1] # 'w are yo'
string[-5:-2] # 'e y'
```

### Iterar una cadena

```python
string = 'How are you'
for c in string:
    print(c)
```

### Check if a string is part of other

`in` and `not in` to check if a string is part of other string or not.

```python
string = 'How are you'
'How' in string # True
```

### Concatenation

```python
"Hola" + "Tio" # "HolaTio"
```

Can also concatenate the string with itself.

```python
"Hola" * 3 # "HolaHolaHola"
```

### Count how many times there is a substring

```python
string = "Hola Que Tal"
string.count("a") # 3
```

### Convierte la cadena a mayúsculas o minúsculas

```python
string = "Hola Que Tal"
string.upper() # "HOLA QUE TAL"
string.lower() # "hola que tal"
```

### Sustituye una substring por otra

```python
string = "Hola Que Tal"
string.replace("Tal", "Pasa") # "Hola Que Pasa"
```

### Devuelve el número de carácteres

```python
string = "Hola Que Tal"
len(string) # 12
```

### Special characters in strings:

`\n`: New line

`\t`: Tab

`\`: Poner antes de una comilla si la queremos mostrar

## Listas

Para guardar varios elementos, cada uno con un índice, empezando por 0.

```python
lista = ["Casa", "Coche", "Gato"]
lista[1] # "Coche"
```

### Matrices

Si en los elementos de una lista ponemos listas, tenemos una matriz.

```python
matriz = [
    [1,2,3],
    [4,5,6]
    ]
    
matriz[1][2] # 6
```

### Crear una lista a partir de un iterable

La función list permite crear una lista a partir de otro iterable. Si se pasa un diccionario, creará la lista con las keys.

```python
cadena = 'HolaTio'
list(cadena) # ['H', 'o', 'l', 'a', 'T', 'i', 'o']
```

### Comprobar si un elemento está en la lista

```python
lista = ["Casa", "Coche", "Gato"]
"Gato" in lista # True
```

### Mostrar el índice de un elemento de la lista

Devuelve solo la posición del primero elemento que coincida. Si no lo encuentra, devuelve un ValueError.

```python
lista = ["Casa", "Coche", "Gato", "Casa"]
lista.index("Casa") # 0
```

Podemos específicar a partir de que posición de la lista comienza a buscar el elemento.

```python
lista = ["Casa", "Coche", "Gato", "Casa"]
lista.index("Casa", 2) # 3
```

Podemos también indicar en que posición dejar de buscar.

```python
lista = ["Casa", "Coche", "Gato", "Casa"]
lista.index("Gato", 0,3) # 2
```

### Contar los elementos de una lista

```python
lista = ["Casa", "Coche", "Gato"]
len(lista) # 3
```

### Iterar una lista

```python
lista = ["Casa", "Coche", "Gato"]
for xx in lista:
    print(xx)
```

### Adjuntar un elemento al final de la lista

Modifica la lista original.

```python
lista = ["Casa", "Coche", "Gato"]
lista.append("Perro") # lista = ["Casa", "Coche", "Gato", "Perro"]
```

### Añadir los elementos de un iterable al final de la lista

Modifica la lista original.

```python
lista = ["Casa", "Coche", "Gato"]
base = ["Perro", "Gafas"]
lista.extend(base) # lista = ['Casa', 'Coche', 'Gato', 'Perro', 'Gafas']
```

### Insertar un elemento en un índice de la lista

Modifica la lista original.

```python
lista = ["Casa", "Coche", "Gato"]
lista.insert(1, "Perro") # lista = ["Casa", "Perro", "Coche", "Gato"]
```

### Eliminar un elemento de la lista

Modifica la lista original. Elimina solo el primero que encuentra.

```python
lista = ["Casa", "Coche", "Gato", "Casa"]
lista.remove("Casa") # lista = ["Coche", "Gato", "Casa"]
```

### Eliminar el elemento de un índice de la lista

Modifica la lista original.

```python
lista = ["Casa", "Coche", "Gato"]
lista.pop(1) # lista = ["Casa", Gato"]
```

### Eliminar todos los elementos de una lista

```python
lista = ["Casa", "Coche", "Gato"]
lista.clear() # lista = []
```

### Cuenta cuantos veces hay un elemento en la lista

```python
lista = ["Casa", "Coche", "Gato", "Casa"]
lista.count("Casa") # 2
```

### Dar la vuelta los elementos de una lista

Modifica la lista original.

```python
lista = ["Casa", "Coche", "Gato"]
lista.reverse() # lista = ["Gato", "Coche", "Casa"]
```

### Ordenar los elementos de una lista

De menor a mayor o en orden álfabetico por defecto. Modifica la lista original.

```python
lista = ["Casa", "Coche", "Gato"]
lista.sort() # lista = ["Casa", "Coche", "Gato"]

lista = [2, 4, 7, 3]
lista.sort(reverse = True) # lista = [2, 3, 4, 7]
```

### Buscar el valor máximo o mínimo de una lista

```python
lista = ["Casa", "Coche", "Gato"]
min(lista) # "Casa"

lista = [2, 4, 7, 3]
max(lista) # 7
```

### Copiar una lista

```python
base = ["Casa", "Coche", "Gato"]
lista = base.copy() # lista = ["Casa", "Coche", "Gato"]
```

### Crear lista de a partir de algo iterable (Lista, rango...)

Podemos iterar sobre algo y para cada iteración podemos añadir un elemento a la lista mediante una expresión. Podemos añadir una condición para añadir el elemento a la lista.

```python
lista = [i**2 for i in range(5)] # [0, 1, 8, 27, 64]
lista = [i + 1 for i in lista] # [1, 2, 5, 10, 17]
```

Podemos crear una o varias condiciones para añadir el elemento a la lista.

```python
lista = [i for i in range(10) if i % 2 == 1] # [1, 3, 5, 7, 9]
lista = [i for i in range(10) if i % 2 == 1 if i <= 5] # [1, 3, 5]
```

Podemos crear una lista a partir de un if else.

```python
base = ['a', 'h', 'g', 'e', 'w']
lista = ["V" if c in 'aeiou' else "C" for c in base] # ['V', 'C', 'C', 'V', 'C']
```

### Separar una cadena en elementos de una lista

```python
string = "Saludos"
lista = [c for c in string] # ['S', 'a', 'l', 'u', 'd', 'o', 's']
```

### Crear una lista a partir de separar una cadena

El separador por defecto es un espacio, lo que permite separar por palabras.

```python
cadena = "Hola que tal"
lista = cadena.split() # ['Hola', 'que', 'tal']
```

Se puede elegir el separador que queramos.

```python
cadena = "Hola_que_tal"
lista = cadena.split('_') # ['Hola', 'que tal']
```

Podemos definir el máximo de separaciones. Al llegar al límite, lo demás no se separa y va en el mismo elemento.

```python
cadena = "Hola que tal"
lista = cadena.split(' ', 1) # ['Hola', 'que tal']
```

## Diccionarios

A diferencia de la una lista, a cada elemento se le puede poner el índice que queramos (keys). Las keys puede ser cualquier elemento inmutable, como cadenas, números, booleanos o tuplas, pero no listas o diccionarios.

No puede haber la misma key dos veces, de intentarlo, se sobrescribiría el valor nuevo sobre el previo.

```python
diccionario = {
    'Casa': 6,
    'Coche': 2,
    'Gato': 4
}

diccionario # {'Casa': 6, 'Coche': 2, 'Gato': 4}
diccionario["Coche"] # 2
```

### Comprobar si una key está en un diccionario

```python
diccionario = {'Casa': 6, 'Coche': 2, 'Gato': 4}
'Gato' not in diccionario # False
'Abeja' in diccionario # False
```

### Borrar un elemento de un diccionario

Permite además especificar un valor a devolver si la key no está en el diccionario.

```python
diccionario = {'Casa': 6, 'Coche': 2, 'Gato': 4}
diccionario.pop('Casa', 'NoKey') # {'Coche': 2, 'Gato': 4}
diccionario.pop('Abeja', 'NoKey') # 'NoKey'
```

También se puede hacer de otra manera que es más rápido pero que devuelve error si la key no existe.

```python
diccionario = {'Casa': 6, 'Coche': 2, 'Gato': 4}
del diccionario['Casa']
diccionario # {'Coche': 2, 'Gato': 4}
```

### Borrar varios elementos de un diccionario

```python
diccionario = {'Casa': 6, 'Coche': 2, 'Gato': 4}
[diccionario.pop(key, 'X') for key in ['Casa', 'Abeja']] # [6, 'X']
diccionario # {'Coche': 2, 'Gato': 4}
```

### Comprobar tamaño de un diccionario

```python
diccionario = {'Casa': 6, 'Coche': 2, 'Gato': 4}
len(diccionario) # 3
```

### Devolver valores de un diccionario sin errores

Al indexar un diccionario con un key que no tiene, esto devuelve un KeyError. Para ello, se usa una función que en cambio devuelve lo que queramos en ese caso.

```python
diccionario = {'Casa': 6, 'Coche': 2, 'Gato': 4}
diccionario.get('Gato') # 4
diccionario.get('Abeja') # None
diccionario.get('Abeja', 'No hay') # 'No hay'
```

### Mostrar todos los values o keys de un diccionario

Se crea un iterable de todas las keys o values que se actualiza automáticamente si el contenido del diccionario se modifica.

```python
diccionario = {'Casa': 6, 'Coche': 2, 'Gato': 4}
dic_keys = diccionario.keys() # dict_keys(['Casa', 'Coche', 'Gato'])
del diccionario['Casa']
dic_keys # dict_keys(['Coche', 'Gato'])
```

```python
diccionario = {'Casa': 6, 'Coche': 2, 'Gato': 4}
dic_values = diccionario.values() # dict_values([6, 2, 4])
del diccionario['Casa']
dic_values # dict_values([2, 4])
```

## Tuplas

Como una lista, pero inmutable. Intentar cambiar el contenido de alguno de los elementos causa error.

```python
tupla = ('Casa', 'Coche', 'Gato')
tupla[1] # 'Coche'
```

### Crear una tupla a partir de un iterable

La función list permite crear una lista a partir de otro iterable. Si se pasa un diccionario, creará la lista con las keys.

```python
lista = ['Casa', 'Coche', 'Gato']
tuple(lista) # ('Casa', 'Coche', 'Gato')
```

## Sets

Colección de elementos sin orden (Sin index) y no repetidos. Más rápido acceder a ellos que una lista.

```python
set1 = {'Casa', 'Coche', 'Gato', 'Casa'} # {'Casa', 'Gato', 'Coche'}
```

### Comprobar que un elemento está en el set

```python
set1 = {'Casa', 'Coche', 'Gato'}
"Casa" in set1 # True
```

### Crear un set a partir de un iterable

La función list permite crear una lista a partir de otro iterable. Si se pasa un diccionario, creará la lista con las keys.

```python
lista = ['Casa', 'Coche', 'Gato']
set(lista) # {'Casa', 'Gato', 'Coche'}
```

### Añadir un elemento al set

```python
set1 = {'Casa', 'Coche', 'Gato'}
set1.add('Perro') # {'Casa', 'Gato', 'Coche', 'Perro'}
```

### Borrar un elemento del set

```python
set1 = {'Casa', 'Coche', 'Gato'}
set1.remove('Casa') # {'Gato', 'Coche'}
```

### Comprobar el tamaño del set

```python
set1 = {'Casa', 'Coche', 'Gato'}
len(set1) # 3
```

### Sacar elementos de dos sets

A partir de dos sets, se pueden hacer operaciones para sacar elementos de ellos:

- **|**: Elementos que hay en alguno de los sets.
- **&**: Elementos que hay en ambos sets.
- **-**: Elementos que hay en el primer set pero no en el segundo. 
- **^**: Elementos que hay en alguno de los sets pero no en ambos.

```python
set1 = {1, 2, 3, 4, 5, 6}
set2 = {4, 5, 6, 7, 8, 9}

print(set1 | set2) # {1, 2, 3, 4, 5, 6, 7, 8, 9}
print(set1 & set2) # {4, 5, 6}
print(set1 - set2) # {1, 2, 3}
print(set1 ^ set2) # {1, 2, 3, 7, 8, 9}
```

## Estrucuras de datos propios

Estructuras de datos creadas a partir de las propias con ciertas funcionalidades. Se crean mediante una clase a la que se le añaden métodos que permiten modificar las estructuas.

## Stack

Estructura de datos propia. Conjunto de elementos ordenados como si estuviesen apilados. Se crea a partir de una lista. Usado para funcionalidades de deshacer-rehacer.

- Solo se puede quitar el último elemento añadido (pop).
- Solo se puede añadir un elemento encima del último añadido (push).

### Crear la clase stack

Crear las métodos para trabajar con el stack: Comprobar si está vacío, hacer un push, un pull y devolver su contenido (Lo devolverá en forma de lista).

```python
class Stack:
    def __init__(self):
        self.items = []  
  
    def empty(self):
        return self.items == []
  
    def push(self, item):
        self.items.insert(0, item)
    
    def pop(self):
        return self.items.pop(0)
    
    def content(self):
        return self.items
```

### Crear un stack y modificarlo

A partir de la clase creada arriba se pueden crear stacks.

```python
stack1 = Stack()
stack1.empty() # True
stack1.push('a')
stack1.push('b')
stack1.content() # ['b', 'a']
stack1.pop()
stack1.content() # ['a']
```

## Queues

Estructura de datos propia. Similar a un stack, pero cuando se añade un elemento se hace en un extremo (Enqueue en el back) y cuando se elimina un elemento se hace en el otro extremo (Dequeue en el front).

### Crear la clase queue

Crear las métodos para trabajar con el queue: Comprobar si está vacío, hacer un enqueue, un dequeue y devolver su contenido (Lo devolverá en forma de lista).

```python
class Queue:
    def __init__(self):
        self.items = []

    def empty(self):
        return self.items == []

    def enqueue(self, item):
        self.items.insert(0, item)

    def dequeue(self):
        return self.items.pop()

    def content(self):
        return self.items
```

### Crear un queue y modificarlo

A partir de la clase creada arriba se pueden crear stacks.

```python
queue1 = Queue()
queue1.empty() # True
queue1.enqueue('a')
queue1.enqueue('42')
queue1.content() # ['42', 'a']
queue1.dequeue()
queue1.content() # ['42']
```

## Linked lists

Estructura de datos propia. Secuencia de nodos donde cada uno tiene su información y un link al próximo nodo. El primer nodo es la cabeza y el último nodo debe tener un None en el link. Se pueden añadir nodos en cualquier posición.

[Linked List vs Array - GeeksforGeeks](https://www.geeksforgeeks.org/linked-list-vs-array/)

### Crear la clase LinkedList

Primero se debe crear la estructura de los nodos.

```python
class Node:
  def __init__(self, data, next):
    self.data = data
    self.next = next
```

Ahora podemos crear la clase LinkedList, que usa la clase Node ya creada, con los siguientes métodos: add_at_front para añadir un nodo al principio, add_at_end para añadir un nodo al final, get_last_node para obtener el contenido del último nodo, is_empty para comprobar que la LinkedList esta vacía y print_list para imprimir la LinkedList.

```python
class LinkedList:
    def __init__(self):
        self.head = None
    
    def add_at_front(self, data):
        self.head = Node(data, self.head)      

    def add_at_end(self, data):
        if not self.head:
            self.head = Node(data, None)
            return
        curr = self.head
        while curr.next:
            curr = curr.next
        curr.next = Node(data, None)

    def get_last_node(self):
        n = self.head
        while(n.next != None):
            n = n.next
        return n.data

    def is_empty(self):
        return self.head == None

    def print_list(self):
        n = self.head
        while n != None:
            print(n.data, end = " => ")
            n = n.next
        print()
```

### Crear una ListedLink y modificarla

```python
s = LinkedList()
s.add_at_front(5)
s.add_at_end(8)
s.add_at_front(9)

s.print_list() # 9 => 5 => 8 => 
print(s.get_last_node()) # 8
```

## Graphs

Estructura de datos propia. Conjunto de nodos (Vertex) done cada uno está conectado (Edge) a otros nodos. Se podría representar como una matriz con los nodos como filas y columnas donde un 1 indica que los nodos están conectados y un 0 que no.

### Crear la clase graphs

Crear las métodos para trabajar con el graph: **add_edge**() para añadir una conexión entre dos nodos, y **remove_edge**() para eliminarla.

```python
class Graph(): 
    def __init__(self, size): 
        self.adj = [ [0] * size for i in range(size)]
        self.size = size 
    
    def add_edge(self, orig, dest): 
        if orig > self.size or dest > self.size or orig < 0 or dest < 0: 
            print("Invalid Edge") 
        else: 
            self.adj[orig-1][dest-1] = 1 
            self.adj[dest-1][orig-1] = 1 
        
    def remove_edge(self, orig, dest): 
        if orig > self.size or dest > self.size or orig < 0 or dest < 0: 
            print("Invalid Edge") 
        else: 
            self.adj[orig-1][dest-1] = 0 
            self.adj[dest-1][orig-1] = 0 
            
    def display(self): 
        for row in self.adj: 
            print() 
            for val in row: 
                print('{:4}'.format(val),end="") 
```

Crear una graph y modificarla. Se indica al principio cuantos nodes tiene.

```python
G = Graph(4) 
G.add_edge(1, 3) 
G.add_edge(3, 4)
G.add_edge(2, 4)
G.display() # Devuelve la matriz con un 1 donde hay nodos conectados
```

## Asignar los elementos de un iterable a variables

Se deben especificar el mismo número de variables que elementos tiene el iterable. Llamado unpacking. Esto también se puede

```python
tupla = ('Casa', 'Coche', 'Gato')
var1, var2, var3 = tupla
var2 # 'Coche'
```

En caso de especificar menos variables, se define una que guardará en forma de lista todos los elementos del iterable que no se asignen a las otras variables.

```python
lista = [1, 2, 3, 4, 5, 6]
var1, *var2, var3 = lista
var1 # 1
var2 # [2, 3, 4, 5]
var3 # 6
```

### Intercambiar el valor de dos variables

```python
var1, var2 = 5, 8
var1, var2 = var2, var1
var2 # 5
```

