# Pythonicness & packaging

### Recomendaciones de estilo

Nombres:

- Los modulos con nombres cortos en minuscula.
- Las clases con la primera letra en mayuscula.
- Variables y funciones en minusculas y guion bajo.
- Constantes en mayusculas y guion bajo.
- Si se usan palabras reservadas se añade un guion bajo al final.
- _ como nombre de variables temporal e insignificante.

Comas: Despues de comas y entre operadores.

Tamaño lineas: Máximo 80 caracteres.

Evitar `from module import *`.

## Funciones

Se puede elegir el número de argumentos que se pasan a una funcion. Deben ir al final de los argumentos. Los que se pasan de mas se juntan en una tupla.

```python
def function(named_arg, *args):
    print(named_arg)
    print(args)

function(1, 2, 3, 4, 5)

#>> 1
#>> (2, 3, 4, 5)
```

Algunos argumentos pueden hacerse opcionales dandoles un valor por defecto. Deben ir despues de los que no tienen valor por defecto. Al usar la funcion se puede elegir si pasarle un valor.

```python
def function(x = "X", y = "Y", z = "Z"):
    print(x, y, z)

function(1, 2)

#>> 1 2 Z
```

Añadir argumentos no definidos con **\*\*kwargs**. Al añadirlos se debe definir el nombre también. Se crea con todos un diccionario.

```python
def my_func(x, y=7, *args, **kwargs):
    print(args)
    print(kwargs)

my_func(2, 3, 4, 5, 6, a=7, b=8)

#>> (4, 5, 6)
#>> {'a': 7, 'b': 8}
```

### Unpacking of an iterable

Allows to assign each value of a tuple to a variable.

```python
numbers = (1, 2, 3)
a, b, c = numbers
print(a)
print(b)
print(c)

#>> 1
#>> 2
#>> 3
```

If there are less variables tan the length of the tuple, one must take a list of the lefovers.

```python
numbers = (1, 2, 3, 4)
a, *b, c = numbers
print(a)
print(b)
print(c)
```

```
1
[2, 3]
4
```

### Ternary operator

Allows to use an if statement with less code.

```python
status  = 1
msg = "Logout" if status == 1 else "Login"
print(msg)
```

```
Logout
```

### Else in for or while statements

Is it called once the loops end if a **break** statement hasn't caused it.

```python
for char in "ABC":
    print(char)
else:
    print("X")
```

```
A
B
C
X
```

Is a **break** statement causes the end of the loop, the else statement is not executed.

```python
for char in "ABC":
    print(char)
    if char == 'B':
        break

else:
    print("X")
```

```
A
B
```

### Else in try/except

The code in **else** is only executed if there are no errors in the **try** statement.

```python
  if i > 5:
      print(i)
      break
else:
   print("7")
```

```
1
3
```

```python
try:
    print(1/0)
except ZeroDivisionError:
    print(4)
else:
    print(5)
```

```
4
```

### Python files for both importing and be an script

Most Python code is to be imported or a script that does something. And for a file to be able to do both, we can make an if statement that won't run if the file is imported as a module.

```python
if __name__=="__main__":
    print("This is a script")
```

```
This is a script
```

### Importing file

We can import other Python files we have. For instance, we save as **parallel.py** the following code:

```python
def function():
    print("This is a module function")

if __name__=="__main__":
    print("This is a script") 
```

Then in our main file, we can import the other file and use its functions.

```python
import parallel

parallel.function()
```

```
This is a module function
```

### 3rd parties libraries

- **Django**, **CherryPy** and **Flask**: Web frameforks.

- **BeautifulSoup**: Scraping websites.

- **matplotlib**: Create graphs.

- **NumPy**: For math operations and use of multidimensional arrays which are way faster than default nested lists.

- **SciPy**: Extension to Numpy.

### Packages

Packaging is putting modules you coded in an standard format so others can use them. First the files must be is order them correctly: In the directory must be the README and LICENSE files, a **setup.py** file and a directory in which there is a file called **\_\_init\_\_.py** and all the modules with code.

Folder/ 
   LICENSE.txt 
   README.txt 
   **setup.py**
   solol/ 
      **\_\_init\_\_.py**
      file1.py 
      file2.py

In **setup.py** we put the info needed to assemble the package in order to upload it to PyPI (Package repository) and then install it with pip. An example of the setup.py file:

```python
from distutils.core import setup

setup(
   name='<package_Name>', 
   version='0.1dev',
   packages=['<package_Name>',],
   license='MIT', 
   long_description=open('README.txt').read(),
)
```

Then, a source distribution (Simplest one) of the package can be made with the command `python setup.py sdist` after navigating to the directory of the file. Instead, `python setup.py bdist` or `python setup.py bdist_wininst` for Windows in order to build a binary distribution.

To upload a package, use `python setup.py register` and then `python setup.py sdist upload` to upload the package.

Then, the command `python setup.py install` allows to install the package.

### Package modules to run anywhere

Python scripts can be packaged as executable scripts in order to run them anywhere and without anything installed or configured. For that purpose, the libraries **py2exe**, **PyInstaller** or **cx_Freeze** can be used. For Mac, **py2app** instead of **py2exe**.