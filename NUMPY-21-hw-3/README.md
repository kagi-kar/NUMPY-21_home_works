# Домашнее задание «Библиотека Pandas»

<a id='contents'></a>
## Оглавление:
### [Задание 1](#task1)
Напишите функцию, которая классифицирует фильмы из материалов занятия по следующим правилам:
- оценка 2 и меньше - низкий рейтинг
- оценка 4 и меньше - средний рейтинг
- оценка 4.5 и 5 - высокий рейтинг

Результат классификации запишите в столбец class
### [Задание 2](#task2)
Используем файл keywords.csv.

Необходимо написать гео-классификатор, который каждой строке сможет выставить географическую принадлежность определенному региону. Т. е. если поисковый запрос содержит название города региона, то в столбце ‘region’ пишется название этого региона. Если поисковый запрос не содержит названия города, то ставим ‘undefined’.

Правила распределения по регионам Центр, Северо-Запад и Дальний Восток:

    geo_data = {
    'Центр': ['москва', 'тула', 'ярославль'],
    'Северо-Запад': ['петербург', 'псков', 'мурманск'],
    'Дальний Восток': ['владивосток', 'сахалин', 'хабаровск']
    }
Результат классификации запишите в отдельный столбец region.
### [Задание 3](#task3)
Есть мнение, что “раньше снимали настоящее кино, не то что сейчас”. Ваша задача проверить это утверждение, используя файлы с рейтингами фильмов из прошлого домашнего занятия (файл movies.csv и ratings.csv из базы grouplens.org.../movielens). Т. е. проверить верно ли, что с ростом года выпуска фильма его средний рейтинг становится ниже.

При этом мы не будем затрагивать субьективные факторы выставления этих рейтингов, а пройдемся по следующему алгоритму:

В переменную years запишите список из всех годов с 1950 по 2010.

Напишите функцию production_year, которая каждой строке из названия фильма выставляет год выпуска. Не все названия фильмов содержат год выпуска в одинаковом формате, поэтому используйте следующий алгоритм:

для каждой строки пройдите по всем годам списка years
если номер года присутствует в названии фильма, то функция возвращает этот год как год выпуска
если ни один из номеров года списка years не встретился в названии фильма, то возвращается 1900 год
Запишите год выпуска фильма по алгоритму пункта 2 в новый столбец ‘year’

Посчитайте средний рейтинг всех фильмов для каждого значения столбца ‘year’ и отсортируйте результат по убыванию рейтинга

P/S/ все файлы находятся в архиве «дополнительные файлы к лекции и дз»

<a id='task1'></a>
## Задание 1


```python
import pandas as pd

path = r'D:\Google Disk\Учеба\Data Scientist с нуля до middle\Python\Материалы курса\NUMPY-21\Python_11_доп_файлы\ratings.csv'
# загружаем датафрейм с данными по рейтингам фильмов от пользователей
df_rate = pd.read_csv(path, encoding = 'utf-8')
df_rate.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>userId</th>
      <th>movieId</th>
      <th>rating</th>
      <th>timestamp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>31</td>
      <td>2.5</td>
      <td>1260759144</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1029</td>
      <td>3.0</td>
      <td>1260759179</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>1061</td>
      <td>3.0</td>
      <td>1260759182</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>1129</td>
      <td>2.0</td>
      <td>1260759185</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>1172</td>
      <td>4.0</td>
      <td>1260759205</td>
    </tr>
  </tbody>
</table>
</div>




```python
path = r'D:\Google Disk\Учеба\Data Scientist с нуля до middle\Python\Материалы курса\NUMPY-21\Python_11_доп_файлы\movies.csv'
# загружаем датафрем с данными о фильмах
df_film = pd.read_csv(path, encoding = 'utf-8')
df_film.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movieId</th>
      <th>title</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Jumanji (1995)</td>
      <td>Adventure|Children|Fantasy</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Grumpier Old Men (1995)</td>
      <td>Comedy|Romance</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Waiting to Exhale (1995)</td>
      <td>Comedy|Drama|Romance</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Father of the Bride Part II (1995)</td>
      <td>Comedy</td>
    </tr>
  </tbody>
</table>
</div>




```python
# объединяем рейтинги с фильмами
df_cons = pd.merge(df_film, df_rate, left_on='movieId', right_on='movieId')
```


```python
# удаляем лишние поля применяя операцию в датафрейму через inplace
df_cons.drop(columns=['genres', 'userId', 'timestamp'], inplace = True)
```


```python
# считаем средний среди пользователей рейтинг у каждого фильма для последующей классификации
df_cons = df_cons.groupby(['movieId', 'title']).mean().reset_index() # т.к. при группировке столбец, по которому
                                                                     # группировали стал индексом, из-за чего будет размещен
                                                                     # на уровень ниже остальных - применяем reset_index() для
                                                                     # переиндексации датафрейма
df_cons.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movieId</th>
      <th>title</th>
      <th>rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>3.872470</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Jumanji (1995)</td>
      <td>3.401869</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Grumpier Old Men (1995)</td>
      <td>3.161017</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Waiting to Exhale (1995)</td>
      <td>2.384615</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Father of the Bride Part II (1995)</td>
      <td>3.267857</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_cons['rating'] = df_cons['rating'].round(1)
df_cons.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movieId</th>
      <th>title</th>
      <th>rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>3.9</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Jumanji (1995)</td>
      <td>3.4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Grumpier Old Men (1995)</td>
      <td>3.2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Waiting to Exhale (1995)</td>
      <td>2.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Father of the Bride Part II (1995)</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>




```python
def film_class (in_rate):
    '''
    функция оценивает средний рейтинг фильма и
    передает его словесное описание
    '''
    if in_rate <= 2:
        return 'low ratign'
    elif in_rate <= 4:
        return 'medium rating'
    else:
        return 'high rating'
```


```python
df_cons['class'] = df_cons['rating'].apply(film_class)
df_cons = df_cons.sort_values(by = 'rating', ascending = False)
df_cons.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movieId</th>
      <th>title</th>
      <th>rating</th>
      <th>class</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9065</th>
      <td>163949</td>
      <td>The Beatles: Eight Days a Week - The Touring Y...</td>
      <td>5.0</td>
      <td>high rating</td>
    </tr>
    <tr>
      <th>4787</th>
      <td>6769</td>
      <td>Mambo Italiano (2003)</td>
      <td>5.0</td>
      <td>high rating</td>
    </tr>
    <tr>
      <th>4464</th>
      <td>6107</td>
      <td>Night of the Shooting Stars (Notte di San Lore...</td>
      <td>5.0</td>
      <td>high rating</td>
    </tr>
    <tr>
      <th>4487</th>
      <td>6163</td>
      <td>He Loves Me... He Loves Me Not (À la folie... ...</td>
      <td>5.0</td>
      <td>high rating</td>
    </tr>
    <tr>
      <th>4580</th>
      <td>6332</td>
      <td>Lizzie McGuire Movie, The (2003)</td>
      <td>5.0</td>
      <td>high rating</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_cons.groupby('class').agg({'movieId' : ['count'], 'rating' : ['min', 'max']}).reset_index()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>class</th>
      <th>movieId</th>
      <th colspan="2" halign="left">rating</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th>count</th>
      <th>min</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>high rating</td>
      <td>1212</td>
      <td>4.1</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>low ratign</td>
      <td>979</td>
      <td>0.5</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>medium rating</td>
      <td>6875</td>
      <td>2.1</td>
      <td>4.0</td>
    </tr>
  </tbody>
</table>
</div>



[к оглавлению](#contents)

<a id='task2'></a>
## Задание 2


```python
import pandas as pd

# загружаем датафрейм с данными по регионам
path = r'D:\Google Disk\Учеба\Data Scientist с нуля до middle\Python\Материалы курса\NUMPY-21\Python_11_доп_файлы\keywords.csv'

df_rate = pd.read_csv(path, encoding = 'utf-8')
df_rate.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>keyword</th>
      <th>shows</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>вк</td>
      <td>64292779</td>
    </tr>
    <tr>
      <th>1</th>
      <td>одноклассники</td>
      <td>63810309</td>
    </tr>
    <tr>
      <th>2</th>
      <td>порно</td>
      <td>41747114</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ютуб</td>
      <td>39995567</td>
    </tr>
    <tr>
      <th>4</th>
      <td>вконтакте</td>
      <td>21014195</td>
    </tr>
  </tbody>
</table>
</div>




```python
geo_data = {
    'Центр': ['москва', 'тула', 'ярославль'],
    'Северо-Запад': ['петербург', 'псков', 'мурманск'],
    'Дальний Восток': ['владивосток', 'сахалин', 'хабаровск']
}
```


```python
geo_data
```




    {'Центр': ['москва', 'тула', 'ярославль'],
     'Северо-Запад': ['петербург', 'псков', 'мурманск'],
     'Дальний Восток': ['владивосток', 'сахалин', 'хабаровск']}




```python
def check_regs (request):
    for reg, cities in geo_data.items():
        for city in cities:
            pattern = r'.*' + city + '.*'
            if re.search(pattern, request.lower()): # применяем .lower(), чтобы все буквы сделать в нижнем регистре
                return reg
    return 'undefined'
```


```python
import re

df_rate['region'] = df_rate['keyword'].apply(check_regs)
df_rate.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>keyword</th>
      <th>shows</th>
      <th>region</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>вк</td>
      <td>64292779</td>
      <td>undefined</td>
    </tr>
    <tr>
      <th>1</th>
      <td>одноклассники</td>
      <td>63810309</td>
      <td>undefined</td>
    </tr>
    <tr>
      <th>2</th>
      <td>порно</td>
      <td>41747114</td>
      <td>undefined</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ютуб</td>
      <td>39995567</td>
      <td>undefined</td>
    </tr>
    <tr>
      <th>4</th>
      <td>вконтакте</td>
      <td>21014195</td>
      <td>undefined</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_rate.groupby(['region']).count().reset_index()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>region</th>
      <th>keyword</th>
      <th>shows</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>undefined</td>
      <td>99253</td>
      <td>99253</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Дальний Восток</td>
      <td>94</td>
      <td>94</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Северо-Запад</td>
      <td>266</td>
      <td>266</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Центр</td>
      <td>387</td>
      <td>387</td>
    </tr>
  </tbody>
</table>
</div>



[к оглавлению](#contents)

<a id='task3'></a>
## Задание 3


```python
import pandas as pd
import numpy as np

path = r'D:\Google Disk\Учеба\Data Scientist с нуля до middle\Python\Материалы курса\NUMPY-21\Python_11_доп_файлы\ratings.csv'
# загружаем датафрейм с данными по рейтингам фильмов от пользователей
df_rate = pd.read_csv(path, encoding = 'utf-8')
```


```python
path = r'D:\Google Disk\Учеба\Data Scientist с нуля до middle\Python\Материалы курса\NUMPY-21\Python_11_доп_файлы\movies.csv'
# загружаем датафрем с данными о фильмах
df_film = pd.read_csv(path, encoding = 'utf-8')
```


```python
df_cons = pd.merge(df_film, df_rate, left_on = 'movieId', right_on = 'movieId')
df_cons.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movieId</th>
      <th>title</th>
      <th>genres</th>
      <th>userId</th>
      <th>rating</th>
      <th>timestamp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
      <td>7</td>
      <td>3.0</td>
      <td>851866703</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
      <td>9</td>
      <td>4.0</td>
      <td>938629179</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
      <td>13</td>
      <td>5.0</td>
      <td>1331380058</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
      <td>15</td>
      <td>2.0</td>
      <td>997938310</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
      <td>19</td>
      <td>3.0</td>
      <td>855190091</td>
    </tr>
  </tbody>
</table>
</div>




```python
years = list(np.arange(1950, 2010 + 1)) # генерируем список годов
```


```python
def production_year(in_film):
    for year in years:
        pattern = r'.*' + str(year) + '.*'
        if re.search(pattern, in_film):
            return year
    return '1900'    
```


```python
df_cons['year'] = df_cons['title'].apply(production_year)
```


```python
df_cons.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movieId</th>
      <th>title</th>
      <th>genres</th>
      <th>userId</th>
      <th>rating</th>
      <th>timestamp</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
      <td>7</td>
      <td>3.0</td>
      <td>851866703</td>
      <td>1995</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
      <td>9</td>
      <td>4.0</td>
      <td>938629179</td>
      <td>1995</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
      <td>13</td>
      <td>5.0</td>
      <td>1331380058</td>
      <td>1995</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
      <td>15</td>
      <td>2.0</td>
      <td>997938310</td>
      <td>1995</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Adventure|Animation|Children|Comedy|Fantasy</td>
      <td>19</td>
      <td>3.0</td>
      <td>855190091</td>
      <td>1995</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_cons = df_cons.groupby('year').mean()['rating'].reset_index()
df_cons.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1950</td>
      <td>3.915254</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1951</td>
      <td>3.983539</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1952</td>
      <td>4.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1953</td>
      <td>3.754587</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1954</td>
      <td>3.994220</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_cons.sort_values(by = 'rating', ascending = False).reset_index(drop = True)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1957</td>
      <td>4.014241</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1972</td>
      <td>4.011136</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1952</td>
      <td>4.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1954</td>
      <td>3.994220</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1951</td>
      <td>3.983539</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>57</th>
      <td>2005</td>
      <td>3.448434</td>
    </tr>
    <tr>
      <th>58</th>
      <td>2003</td>
      <td>3.444777</td>
    </tr>
    <tr>
      <th>59</th>
      <td>1996</td>
      <td>3.426600</td>
    </tr>
    <tr>
      <th>60</th>
      <td>1997</td>
      <td>3.415764</td>
    </tr>
    <tr>
      <th>61</th>
      <td>2000</td>
      <td>3.356104</td>
    </tr>
  </tbody>
</table>
<p>62 rows × 2 columns</p>
</div>



[к оглавлению](#contents)
