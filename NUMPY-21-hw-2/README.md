# Домашнее задание «Библиотека Pandas»

<a id='contents'></a>
## Оглавление:
### [Задание 1](#task1)
Скачайте с сайта grouplens.org...movielens/ датасет любого размера. Определите какому фильму было выставлено больше всего оценок 5.0.
### [Задание 2](#task2)
По данным файла power.csv посчитайте суммарное потребление стран Прибалтики (Латвия, Литва и Эстония) категорий 4, 12 и 21 за период с 2005 по 2010 года. Не учитывайте в расчетах отрицательные значения quantity.
### [Задание 3](#task3)
Выберите страницу любого сайта с табличными данными. Импортируйте таблицы в pandas dataframe.
Примеры страниц (необязательно брать именно эти):
https://fortrader.org/quotes
www.finanz.ru...om-vremeni

<a id='task1'></a>
## Задание 1


```python
import pandas as pd
```


```python
# загружаем в датафрем данные о рейтингах по фильмам, оставляя только нужные поля и id фильмов только если их рейтинг = 5.0
movies_rates = pd.read_csv("ml-25m/ratings.csv")[['movieId', 'rating']].query(f'rating == 5.0')
movies_rates.head()
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
      <th>rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>296</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>307</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>665</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1237</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2632</td>
      <td>5.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# загружаем в датафрем с данными о наименовании фильма и его id
movies = pd.read_csv("ml-25m/movies.csv")[['movieId', 'title']]
movies.head()
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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Jumanji (1995)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Grumpier Old Men (1995)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Waiting to Exhale (1995)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Father of the Bride Part II (1995)</td>
    </tr>
  </tbody>
</table>
</div>




```python
# последовательно - объединяем две таблицы по полю id фильма, добавляем к полям суффиксы, т.к. возникает ошибка из-за
# одинаковых наименований полей, соединяем левым джойном к таблице с рейтингами, чтобы получить названия только по тем фильмам,
# по которым рейтинг 5.0, оставляем только поле с наименованием фильма, считаем число повторений каждого фильма и выводим самую
# верхнюю строку - с самым часто повторяющимся фильмом из списка, где всем присвоен рейтинг 5.0
movies_rates.join(movies, on = 'movieId', how = 'left', lsuffix = '_mr', rsuffix = '_m')['title'].value_counts().head(1)
```




    Swimming with Sharks (1995)    39553
    Name: title, dtype: int64



[к оглавлению](#contents)

<a id='task2'></a>
## Задание 2


```python
# загружаем данные, одновременно фильтруя строки, с целью исключения полей quantity < 0 сортируем по возрастанию, чтобы проверить
data = pd.read_csv('power.csv').query(f'quantity >= 0').sort_values(by = 'quantity', ascending = True)
data.head()
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
      <th>country</th>
      <th>year</th>
      <th>quantity</th>
      <th>category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>200774</th>
      <td>Belgium</td>
      <td>2013</td>
      <td>0.0</td>
      <td>20</td>
    </tr>
    <tr>
      <th>56203</th>
      <td>South Africa</td>
      <td>2013</td>
      <td>0.0</td>
      <td>8</td>
    </tr>
    <tr>
      <th>56204</th>
      <td>South Africa</td>
      <td>2012</td>
      <td>0.0</td>
      <td>8</td>
    </tr>
    <tr>
      <th>56205</th>
      <td>South Africa</td>
      <td>2011</td>
      <td>0.0</td>
      <td>8</td>
    </tr>
    <tr>
      <th>56206</th>
      <td>South Africa</td>
      <td>2010</td>
      <td>0.0</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
# ищем наименования Латвии, Литвы и Эстонии (как пишутя в датафрейме)
data[data['country'].str.contains('lit|lat|esto', case = False)]['country'].unique()
```




    array(['Latvia', 'Lithuania', 'Estonia'], dtype=object)



фильтруем данные оставляя только: Латвия, Литва и Эстония, категории 4, 12 и 21, годы с 2005 по 2010


```python
data = data[data['country'].str.contains('lit|lat|esto', case = False)]
```


```python
data = data.query(f'(year >= 2005) & (year <= 2010)')
```


```python
data = data.query(f'category in [4, 12, 21]')
```


```python
data.head()
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
      <th>country</th>
      <th>year</th>
      <th>quantity</th>
      <th>category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>103579</th>
      <td>Lithuania</td>
      <td>2007</td>
      <td>0.0</td>
      <td>12</td>
    </tr>
    <tr>
      <th>103578</th>
      <td>Lithuania</td>
      <td>2008</td>
      <td>0.0</td>
      <td>12</td>
    </tr>
    <tr>
      <th>103577</th>
      <td>Lithuania</td>
      <td>2009</td>
      <td>0.0</td>
      <td>12</td>
    </tr>
    <tr>
      <th>118855</th>
      <td>Lithuania</td>
      <td>2009</td>
      <td>0.0</td>
      <td>12</td>
    </tr>
    <tr>
      <th>21901</th>
      <td>Estonia</td>
      <td>2007</td>
      <td>0.0</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>




```python
Суммарное потребление:
```


```python
data['quantity'].sum(axis=0)
```




    240580.0



[к оглавлению](#contents)

<a id='task3'></a>
## Задание 3


```python
receive_tables = pd.read_html('https://www.testfirm.ru/otrasli/')
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
      <th>Код ОКВЭД</th>
      <th>Вид деятельности</th>
      <th>Рентабельность продаж в 2020 г., %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>Все отрасли РФ</td>
      <td>46</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>Растениеводство и животноводство, охота и пред...</td>
      <td>106</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.0</td>
      <td>Лесоводство и лесозаготовки</td>
      <td>67</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>Рыболовство и рыбоводство</td>
      <td>106</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5.0</td>
      <td>Добыча угля</td>
      <td>29</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>81</th>
      <td>92.0</td>
      <td>Деятельность по организации и проведению азарт...</td>
      <td>41</td>
    </tr>
    <tr>
      <th>82</th>
      <td>93.0</td>
      <td>Деятельность в области спорта, отдыха и развле...</td>
      <td>3</td>
    </tr>
    <tr>
      <th>83</th>
      <td>94.0</td>
      <td>Деятельность общественных организаций</td>
      <td>72</td>
    </tr>
    <tr>
      <th>84</th>
      <td>95.0</td>
      <td>Ремонт компьютеров, предметов личного потребле...</td>
      <td>62</td>
    </tr>
    <tr>
      <th>85</th>
      <td>96.0</td>
      <td>Деятельность по предоставлению прочих персонал...</td>
      <td>41</td>
    </tr>
  </tbody>
</table>
<p>86 rows × 3 columns</p>
</div>



[к оглавлению](#contents)
