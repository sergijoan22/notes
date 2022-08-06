## POO

Una clase describe como va a ser un objeto y estos se crean a partir de ellas. Al intentar acceder a un atributo de un objeto que no existe, devuelve un AtributeError.

### Crear una clase y objetos a partir de ella

En este caso, creamos una clase llamado Gato. Le añadimos dos atributos, el color y la edad, que se completan al crear el objeto.

Creamos también un atributo de clase, con la raza.

Creamos también dos métodos, uno para aumentarle la edad y otro para que maúlle.

A partir de ella, podemos crear los objetos que queramos.

```python
class Gato:
    raza = "Ninguna"	
    def __init__():
        self.color = color
        self.edad = legs	
    
    def maullar(self):
        return 'Miauuu!'
        
    def cumpleaños(self):
        self.edad = self.edad + 1

        
boleta = Cat("Blanco", 2)
boloncho = Cat("Gris", 7)
boleta.edad # 2
boloncho.raza # 'Ninguna'
Gato.raza # 'Ninguna'
boloncho.maullar() # 'Miauuu!'
boleta.cumpleaños # boleta.edad = 3

```

### Herencia

Permite a una clase (subclase) heredar de otra clase (superclase) sus atributos y métodos. Si en la subclase se define algo con el mismo nombre de los hereda, se superpone. Una clase puede heredar de una subclase y así.

```python
class Animal:
    def __init__(self, edad, color):
        self.edad = edad
        self.color = color

class Gato(Animal):
    def maullar():
        return 'Miauuu!'

boleta = Gato(5, 'Blanco')
```

```python
class A:
    def method(self):
        return "A method"
    
class B(A):
    def another_method(self):
        return "B method"
    
class C(B):
    def third_method(self):
        return "C method"
    
c = C()
c.method() # A method
c.another_method() # B method
c.third_method() # C method
```

La función super() permite llamar a la superclase para usar sus métodos.

```python
class A:
    def spam(self):
        print(1)

class B(A):
    def spam(self):
        print(2)
        super().spam()

B().spam() # 2\n1
```

### Métodos mágicos

Estos métodos (Dunders) permiten definir como actuar en caso de usar sobre un objeto algún operador o función, como una suma o str(). Un ejemplo es \_\_add\_\_, que permite actuar en caso de usar el operador de suma sobre una instancia de la clase. Al usar x + y seria equivalente a hacer x.\_\_add\_\_(y). Si x no tuviese ese dunder definido y fuese de otra clase, se llamaría a y.\_\_radd\_\_(x), que es el método r de add, teniendo todos los dunders su método r propio.

```python
class Vector2D:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def mostrar(self):
        return '(' + str(self.x) + ', ' + str(self.y) + ')'
    
    def __add__(self, other):
        return Vector2D(self.x + other.x, self.y + other.y)

first = Vector2D(5, 7)
second = Vector2D(3, 9)
result = first + second
result.mostrar() # (8, 16)
```

Hay muchos otros dunders: 

- **str** para elegir que devuelve al usar str() sobre el objeto. Si no existe, llama a **repr**

- **repr** es [parecido a str](https://www.journaldev.com/22460/python-str-repr-functions) pero para devolverlo de una manera que lo lea la máquina.
- **__sub__** para -
-  **__mul__** para *
- **__truediv__** para /
- **__floordiv__** para //
- **__mod__** para %
- **__pow__** para 
- __and__** para &
- **__xor__** para ^
- **__or__** para |
- **__lt__** para <
- **__le__** para <=
- **__eq__** para ==
- **__ne__** para !=. Si no está implementado, devuelve lo contario a **eq**
- **__gt__** para >
- **__ge__** para >=
- **__len__** para len()
- **__getitem__** para indexar
- **__setitem__** para asignar a valores indexados. Ej: x[y] = z llamaría a x.\_\_setitem\_\_(y, z)
- **__delitem__** para eliminar valores indexados
- **__iter__** para iterar sobre el objeto
- **__contains__** para usar in
- **__int__** para convertir a entero

En el ejemplo siguinte, definimos que al usar la función len() sobre nuestro objeto, devuelva el número 5.

### Ciclo de vida del objeto

Creación, manipulación y destrucción. Primero se define a que clase pertenece, luego se llama a \_\_init\_\_ para crear la instancia. Una variable se destruye, dejando el hueco de memoria libre, cuando su cuenta de referencias, el número de elementos que se refieren al objeto, llega a 0.

```python
a = 42  # Create object <42> 
b = a  # Increase ref. count  of <42> 
c = [a]  # Increase ref. count  of <42>  

del a  # Decrease ref. count  of <42> 
b = 100  # Decrease ref. count  of <42> 
c[0] = -1  # Decrease ref. count  of <42>
```

### Esconder partes de una clase

Hay ciertas partes de una clase que no se recomienda usar en código de terceros, pero no hay ninguna manera de hacerlas privadas. Para ello, la convención recomienda poner un _ en el principio del nombre.

También se suele poner dos _ antes de crear el nombre. Esto hace que para llamar a esa parte desde fuera de la clase, en vez de poner `nombreObjeto.nombreParte` se deba hacer como `nombreObjeto._nombreClase__nombreParte`

```python
class Spam:
    __egg = 7
    def print_egg(self):
        print(self.__egg)

s = Spam()
s._Spam__egg # 7
```

### Propiedades

Permiten personalizar como acceder a el atributo de un objeto. Se crean poniendo `@property` encima de un método, y cuando se llame a un atributo de mismo nombre que el método, es a este último el que se llamará. En el ejemplo, crea un atributo llamado pineapple_allowed de solo lectura.

```python
class Person:
    def __init__(self, age):
        self.age = int(age)
    
    @property
    def isAdult(self):
        if self.age >= 18:
            return True
        else:
            return False

persona = Person(15)
print(persona.isAdult) # False
persona.isAdult = True # AttributeError
```

Estos atributos también se pueden definir mediante las funciones setter() y getter(), que definen y devuelven el valor del atributo.

