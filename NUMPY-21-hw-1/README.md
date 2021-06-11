# Домашнее задание «Библиотека numpy. Вычислительные задачи»

<a id='contents'></a>
## Оглавление:
### [Задание 1](#task1)
Создайте numpy array с элементами от числа N до 0 (например, для N = 10 это будет array([9, 8, 7, 6, 5, 4, 3, 2, 1, 0])).
### [Задание 2](#task2)
Создайте диагональную матрицу с элементами от N до 0. Посчитайте сумму ее значений на диагонали.
### [Задание 3](#task3)
Решите систему уравнений:  
4x + 2y + z = 4  
x + 3y = 12  
5y + 4z = -3  
### [Задание 4](#task4)
Имеется матрица покупок в интернет-магазине. Столбец А - ID пользователя. Остальные столбцы - количество покупок категорий товаров этим пользователем:  
```
users_stats = np.array(
    [
        [2, 1, 0, 0, 0, 0],
        [1, 1, 2, 1, 0, 0],
        [2, 0, 1, 0, 0, 0],
        [1, 1, 2, 1, 0, 1],
        [0, 0, 1, 2, 0, 0],
        [0, 0, 0, 0, 0, 5],
        [1, 0, 0, 0, 0, 0],
        [0, 1, 1, 0, 0, 0],
        [0, 0, 0, 1, 1, 3],
        [1, 0, 0, 2, 1, 4]
    ], 
    np.int32
)
```
На сайт заходит очередной посетитель, о покупках которого известно следующее:  
`next_user_stats = np.array([0, 1, 2, 0, 0, 0])`   
Найдите самого похожего пользователя. Т. е. посчитайте косинусное сходство между этим пользователем и всеми пользователями из массива user_stats

<a id='task1'></a>
## Задание 1


```python
import numpy as np
```


```python
def array_gen(in_number):
    return np.arange(in_number-1, -1, -1)
```


```python
array_gen(15)
```




    array([14, 13, 12, 11, 10,  9,  8,  7,  6,  5,  4,  3,  2,  1,  0])



[к оглавлению](#contents)

<a id='task2'></a>
## Задание 2


```python
def array_gen(in_number):
    return np.diag(np.arange(in_number-1, -1, -1), k=0)
```


```python
print(f'Диагональная матрица:\n{array_gen(5)}')
print(f'Сумма элементов диагонали матрицы: {np.sum(array_gen(5))}')
```

    Диагональная матрица:
    [[4 0 0 0 0]
     [0 3 0 0 0]
     [0 0 2 0 0]
     [0 0 0 1 0]
     [0 0 0 0 0]]
    Сумма элементов диагонали матрицы: 10
    

Второй вариант:


```python
def diag_sum(in_number):
    diag_array = array_gen(in_number)
    return sum(diag_array[i][i] for i in range(in_number))
```


```python
diag_sum(5)
```




    10



[к оглавлению](#contents)

<a id='task3'></a>
## Задание 3

Решите систему уравнений:  
4x + 2y + z = 4  
x + 3y = 12  
5y + 4z = -3  


```python
coef_value = np.array([[4, 2, 1], [1, 3, 0], [5, 4, 0]])
resul_value = np.array([4, 12, -3])
```


```python
from numpy import linalg
```


```python
linalg.solve(coef_value, resul_value)
```




    array([-5.18181818,  5.72727273, 13.27272727])




```python
np.allclose(np.dot(coef_value, linalg.solve(coef_value, resul_value)), resul_value)
```




    True



[к оглавлению](#contents)

<a id='task4'></a>
## Задание 4


```python
users_stats = np.array(
    [
        [2, 1, 0, 0, 0, 0],
        [1, 1, 2, 1, 0, 0],
        [2, 0, 1, 0, 0, 0],
        [1, 1, 2, 1, 0, 1],
        [0, 0, 1, 2, 0, 0],
        [0, 0, 0, 0, 0, 5],
        [1, 0, 0, 0, 0, 0],
        [0, 1, 1, 0, 0, 0],
        [0, 0, 0, 1, 1, 3],
        [1, 0, 0, 2, 1, 4]
    ], 
    np.int32
)
```


```python
next_user_stats = np.array([0, 1, 2, 0, 0, 0])
```


```python
from numpy import linalg
```


```python
similarity = 0
sim_pos = None

# рассчитываем норму матрицы покупок для нового посетителя
next_user_length = np.linalg.norm( next_user_stats )

for count, current_user in enumerate(users_stats):
    # рассчитываем норму матрицы покупок для нового посетителя
    current_user_length = np.linalg.norm( current_user )
    # рассчитываем cos угла между векторами (массивами покупок) нового и каждого действующего посетителя
    cosin_users = np.dot( next_user_stats, current_user ) / ( next_user_length * current_user_length )
    # ищем и запоминаем максимально близкого посетителя
    if cosin_users > similarity:
        similarity = cosin_users
        sim_pos = count
print(f'Наиболее близким новому посетителю является посетитель № {sim_pos+1},\nсо следующей статистикой покупок: {list(users_stats[sim_pos])}')
```

    Наиболее близким новому посетителю является посетитель № 8,
    со следующей статистикой покупок: [0, 1, 1, 0, 0, 0]
    

[к оглавлению](#contents)
