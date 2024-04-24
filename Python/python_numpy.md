# NumPy

Contains array structures:

- Called ndarrays.
- Faster than lists.
- Can only have one data type.

### Create an array

```python
array1 = np.array([3, 4, 3])

array2 = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
```

### Create an array with a range

```python
array1 = np.arange(1, 9, 2) # [1 3 5 7]
```

### Index an array

```python
array1 = np.array([3, 4, 3])
array2 = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])

first_element = array1[0] # 3
first_element = array2[0][1] # 6
```

### Obtain properties of the array

- `ndim`: Returns number of dimensions.
- `size`: Returns number of elements in the array
- `shape`: Returns tuple with number of elements in each dimension of the array.

```python
array1 = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])

array1.ndim # 2
array1.size # 9
array1.shape # (3, 3)
```

### Modify array elements

- `np.append`: Adds an element to an array
- `np.delete`: Delete the elements on the index of an array
- `np.sort`: Sort elements of the array

```python
array1 = np.array([1, 3, 2])

array1 = np.append(array1, 4) # [1 3 2 4]
array1 = np.delete(array1, 0) # [3 2 4]
array1 = np.sort(array1) # [2 3 4]
array1 = array1[::-1] # [4, 3, 2]
```