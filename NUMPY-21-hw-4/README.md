# Домашнее задание к лекции «Продвинутый pandas»

<a id='contents'></a>
## Оглавление:
### [Задание 1](#task1)
Для датафрейма log из материалов занятия создайте столбец source_type по следующим правилам:

если источник traffic_source равен yandex или google, то в source_type ставится organic
для источников paid и email из России - ставим ad
для источников paid и email не из России - ставим other
все остальные варианты берем из traffic_source без изменений

Результат классификации запишите в столбец class
### [Задание 2](#task2)
В файле URLs.txt содержатся url страниц новостного сайта. Вам необходимо отфильтровать его по адресам страниц с текстами новостей. Известно, что шаблон страницы новостей имеет внутри url следующую конструкцию: /, затем 8 цифр, затем дефис. Выполните следующие действия:

Прочитайте содержимое файла с датафрейм
Отфильтруйте страницы с текстом новостей, используя метод str.contains и регулярное выражение в соответствии с заданным шаблоном
### [Задание 3](#task3)
Используйте файл с оценками фильмов ml-latest-small/ratings.csv. Посчитайте среднее время жизни пользователей, которые выставили более 100 оценок. Под временем жизни понимается разница между максимальным и минимальным значением столбца timestamp для данного значения userId.
### [Задание 4](#task4)
Дана статистика услуг перевозок клиентов компании по типам (см. файл “Python_13_join.ipynb” в разделе Материалы для лекции «Продвинутый pandas» ---- Ноутбуки к лекции «Продвинутый pandas»).
Необходимо сформировать две таблицы:
таблицу с тремя типами выручки для каждого client_id без указания адреса клиента
аналогичную таблицу по типам выручки с указанием адреса клиента

<a id='task1'></a>
## Задание 1


```python
import pandas as pd
```


```python
route = r'D:\Google Disk\Учеба\Data Scientist с нуля до middle\Python\NUMPY-21_practical_works\2021.06.28_Продвинутый Pandas\12_доп_материалы_к_пандас_1_и_2\visit_log.csv'

df = pd.read_csv(route, sep = ';') # читаем исходник csv в dataframe, прямо указывая разделитель
df.head()
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
      <th>timestamp</th>
      <th>visit_id</th>
      <th>url</th>
      <th>region</th>
      <th>user_id</th>
      <th>traffic_source</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1549980692</td>
      <td>e3b0c44298</td>
      <td>https://host.ru/3c19b4ef7371864fa3</td>
      <td>Russia</td>
      <td>b1613cc09f</td>
      <td>yandex</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1549980704</td>
      <td>6e340b9cff</td>
      <td>https://host.ru/c8d9213a31839f9a3a</td>
      <td>Russia</td>
      <td>4c3ec14bee</td>
      <td>direct</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1549980715</td>
      <td>96a296d224</td>
      <td>https://host.ru/b8b58337d272ee7b15</td>
      <td>Russia</td>
      <td>a8c40697fb</td>
      <td>yandex</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1549980725</td>
      <td>709e80c884</td>
      <td>https://host.ru/b8b58337d272ee7b15</td>
      <td>Russia</td>
      <td>521ac1d6a0</td>
      <td>yandex</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1549980736</td>
      <td>df3f619804</td>
      <td>https://host.ru/b8b58337d272ee7b15</td>
      <td>Russia</td>
      <td>d7323c571c</td>
      <td>yandex</td>
    </tr>
  </tbody>
</table>
</div>




```python
# создаем словарь для проверки
# в качестве ключей задаем проверяемые параметры переводя их в неизменяемый
# список (tuple), иначе словарь список в ключ не примет
check_dic = {tuple(['paid', 'email', 'Russia']):'ad',
             tuple(['yandex', 'google', 'any country']):'organic',
             tuple(['paid', 'email', 'not Russia']):'other'
}
```


```python
def source (row):
    '''
    Во-первых проверяется, относится ли источник к 'paid', 'email' из 'Russia'.
    Если не выполняется, делается проверка, что источник есть среди ключей словаря, но в текущий момент
    цикл не стоит на элементе словаря с 'Russia', чтобы не допустить ошибку, когда источник из 'paid', 'email' не в России
    '''
    for key, value in check_dic.items():
        if ((row.traffic_source in key) and               # проверяем, что в строке есть 'paid', 'email' и 'Russia'
            (row.region in key)):
            return value
        elif ((row.traffic_source in key) and             # проверяем, что в строке есть 'paid', 'email', 'yandex', 'google', но
                                                          # но мы не стоим на строке словаря, содержащим 'Russia'
              (list(check_dic.keys())[0][2] not in key)):
            return value
    return row.traffic_source                             # если ничего не подходит - выводим название источника
```

Тестируем функцию на тестовом DataFrame


```python
test_df = pd.DataFrame({'timestamp':['NaN'],
                        'visit_id':['NaN'],
                        'url':['NaN'],
                        'region':['Russia'],
                        'user_id':['NaN'],
                        'traffic_source':['email']})
test_df['source_type'] = test_df.apply(source, axis = 1)
test_df.head()
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
      <th>timestamp</th>
      <th>visit_id</th>
      <th>url</th>
      <th>region</th>
      <th>user_id</th>
      <th>traffic_source</th>
      <th>source_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Russia</td>
      <td>NaN</td>
      <td>email</td>
      <td>ad</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['source_type'] = df.apply(source, axis = 1)
df.head()
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
      <th>timestamp</th>
      <th>visit_id</th>
      <th>url</th>
      <th>region</th>
      <th>user_id</th>
      <th>traffic_source</th>
      <th>source_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1549980692</td>
      <td>e3b0c44298</td>
      <td>https://host.ru/3c19b4ef7371864fa3</td>
      <td>Russia</td>
      <td>b1613cc09f</td>
      <td>yandex</td>
      <td>organic</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1549980704</td>
      <td>6e340b9cff</td>
      <td>https://host.ru/c8d9213a31839f9a3a</td>
      <td>Russia</td>
      <td>4c3ec14bee</td>
      <td>direct</td>
      <td>direct</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1549980715</td>
      <td>96a296d224</td>
      <td>https://host.ru/b8b58337d272ee7b15</td>
      <td>Russia</td>
      <td>a8c40697fb</td>
      <td>yandex</td>
      <td>organic</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1549980725</td>
      <td>709e80c884</td>
      <td>https://host.ru/b8b58337d272ee7b15</td>
      <td>Russia</td>
      <td>521ac1d6a0</td>
      <td>yandex</td>
      <td>organic</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1549980736</td>
      <td>df3f619804</td>
      <td>https://host.ru/b8b58337d272ee7b15</td>
      <td>Russia</td>
      <td>d7323c571c</td>
      <td>yandex</td>
      <td>organic</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%time
for i in range(10):
    df['source_type'] = df.apply(source, axis = 1)
```

    Wall time: 6.95 s
    

### Альтернативный вариант (более быстрый)


```python
df.loc[(df.traffic_source.isin(['paid', 'email'])) & (df.region == 'Russia'), 'source_type', ] = 'ad'
df.loc[(df.traffic_source.isin(['paid', 'email'])) & (df.region != 'Russia'), 'source_type'] = 'other'
df.loc[df.traffic_source.isin(['yandex', 'google']), 'source_type'] = 'organic'
df.source_type.fillna(df.traffic_source, inplace = True)
df.head()
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
      <th>timestamp</th>
      <th>visit_id</th>
      <th>url</th>
      <th>region</th>
      <th>user_id</th>
      <th>traffic_source</th>
      <th>source_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1549980692</td>
      <td>e3b0c44298</td>
      <td>https://host.ru/3c19b4ef7371864fa3</td>
      <td>Russia</td>
      <td>b1613cc09f</td>
      <td>yandex</td>
      <td>organic</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1549980704</td>
      <td>6e340b9cff</td>
      <td>https://host.ru/c8d9213a31839f9a3a</td>
      <td>Russia</td>
      <td>4c3ec14bee</td>
      <td>direct</td>
      <td>direct</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1549980715</td>
      <td>96a296d224</td>
      <td>https://host.ru/b8b58337d272ee7b15</td>
      <td>Russia</td>
      <td>a8c40697fb</td>
      <td>yandex</td>
      <td>organic</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1549980725</td>
      <td>709e80c884</td>
      <td>https://host.ru/b8b58337d272ee7b15</td>
      <td>Russia</td>
      <td>521ac1d6a0</td>
      <td>yandex</td>
      <td>organic</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1549980736</td>
      <td>df3f619804</td>
      <td>https://host.ru/b8b58337d272ee7b15</td>
      <td>Russia</td>
      <td>d7323c571c</td>
      <td>yandex</td>
      <td>organic</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.groupby(['source_type', 'traffic_source', 'region']).count()
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
      <th></th>
      <th></th>
      <th>timestamp</th>
      <th>visit_id</th>
      <th>url</th>
      <th>user_id</th>
    </tr>
    <tr>
      <th>source_type</th>
      <th>traffic_source</th>
      <th>region</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">ad</th>
      <th>email</th>
      <th>Russia</th>
      <td>2209</td>
      <td>2209</td>
      <td>2209</td>
      <td>2209</td>
    </tr>
    <tr>
      <th>paid</th>
      <th>Russia</th>
      <td>1581</td>
      <td>1581</td>
      <td>1581</td>
      <td>1581</td>
    </tr>
    <tr>
      <th rowspan="6" valign="top">direct</th>
      <th rowspan="6" valign="top">direct</th>
      <th>Australia</th>
      <td>222</td>
      <td>222</td>
      <td>222</td>
      <td>222</td>
    </tr>
    <tr>
      <th>Belarus</th>
      <td>998</td>
      <td>998</td>
      <td>998</td>
      <td>998</td>
    </tr>
    <tr>
      <th>China</th>
      <td>397</td>
      <td>397</td>
      <td>397</td>
      <td>397</td>
    </tr>
    <tr>
      <th>India</th>
      <td>134</td>
      <td>134</td>
      <td>134</td>
      <td>134</td>
    </tr>
    <tr>
      <th>Russia</th>
      <td>4052</td>
      <td>4052</td>
      <td>4052</td>
      <td>4052</td>
    </tr>
    <tr>
      <th>Ukraine</th>
      <td>1210</td>
      <td>1210</td>
      <td>1210</td>
      <td>1210</td>
    </tr>
    <tr>
      <th rowspan="12" valign="top">organic</th>
      <th rowspan="6" valign="top">google</th>
      <th>Australia</th>
      <td>91</td>
      <td>91</td>
      <td>91</td>
      <td>91</td>
    </tr>
    <tr>
      <th>Belarus</th>
      <td>376</td>
      <td>376</td>
      <td>376</td>
      <td>376</td>
    </tr>
    <tr>
      <th>China</th>
      <td>151</td>
      <td>151</td>
      <td>151</td>
      <td>151</td>
    </tr>
    <tr>
      <th>India</th>
      <td>55</td>
      <td>55</td>
      <td>55</td>
      <td>55</td>
    </tr>
    <tr>
      <th>Russia</th>
      <td>1435</td>
      <td>1435</td>
      <td>1435</td>
      <td>1435</td>
    </tr>
    <tr>
      <th>Ukraine</th>
      <td>399</td>
      <td>399</td>
      <td>399</td>
      <td>399</td>
    </tr>
    <tr>
      <th rowspan="6" valign="top">yandex</th>
      <th>Australia</th>
      <td>88</td>
      <td>88</td>
      <td>88</td>
      <td>88</td>
    </tr>
    <tr>
      <th>Belarus</th>
      <td>446</td>
      <td>446</td>
      <td>446</td>
      <td>446</td>
    </tr>
    <tr>
      <th>China</th>
      <td>171</td>
      <td>171</td>
      <td>171</td>
      <td>171</td>
    </tr>
    <tr>
      <th>India</th>
      <td>59</td>
      <td>59</td>
      <td>59</td>
      <td>59</td>
    </tr>
    <tr>
      <th>Russia</th>
      <td>1636</td>
      <td>1636</td>
      <td>1636</td>
      <td>1636</td>
    </tr>
    <tr>
      <th>Ukraine</th>
      <td>500</td>
      <td>500</td>
      <td>500</td>
      <td>500</td>
    </tr>
    <tr>
      <th rowspan="10" valign="top">other</th>
      <th rowspan="5" valign="top">email</th>
      <th>Australia</th>
      <td>115</td>
      <td>115</td>
      <td>115</td>
      <td>115</td>
    </tr>
    <tr>
      <th>Belarus</th>
      <td>569</td>
      <td>569</td>
      <td>569</td>
      <td>569</td>
    </tr>
    <tr>
      <th>China</th>
      <td>252</td>
      <td>252</td>
      <td>252</td>
      <td>252</td>
    </tr>
    <tr>
      <th>India</th>
      <td>82</td>
      <td>82</td>
      <td>82</td>
      <td>82</td>
    </tr>
    <tr>
      <th>Ukraine</th>
      <td>648</td>
      <td>648</td>
      <td>648</td>
      <td>648</td>
    </tr>
    <tr>
      <th rowspan="5" valign="top">paid</th>
      <th>Australia</th>
      <td>74</td>
      <td>74</td>
      <td>74</td>
      <td>74</td>
    </tr>
    <tr>
      <th>Belarus</th>
      <td>368</td>
      <td>368</td>
      <td>368</td>
      <td>368</td>
    </tr>
    <tr>
      <th>China</th>
      <td>138</td>
      <td>138</td>
      <td>138</td>
      <td>138</td>
    </tr>
    <tr>
      <th>India</th>
      <td>39</td>
      <td>39</td>
      <td>39</td>
      <td>39</td>
    </tr>
    <tr>
      <th>Ukraine</th>
      <td>443</td>
      <td>443</td>
      <td>443</td>
      <td>443</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%time
for i in range(10):
    df.loc[(df.traffic_source.isin(['paid', 'email'])) & (df.region == 'Russia'), 'source_type'] = 'ad'
    df.loc[(df.traffic_source.isin(['paid', 'email'])) & (df.region != 'Russia'), 'source_type'] = 'other'
    df.loc[df.traffic_source.isin(['yandex', 'google']), 'source_type'] = 'organic'
    df.source_type.fillna(df.traffic_source, inplace = True)
df.head()
```

    Wall time: 78 ms
    




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
      <th>timestamp</th>
      <th>visit_id</th>
      <th>url</th>
      <th>region</th>
      <th>user_id</th>
      <th>traffic_source</th>
      <th>source_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1549980692</td>
      <td>e3b0c44298</td>
      <td>https://host.ru/3c19b4ef7371864fa3</td>
      <td>Russia</td>
      <td>b1613cc09f</td>
      <td>yandex</td>
      <td>organic</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1549980704</td>
      <td>6e340b9cff</td>
      <td>https://host.ru/c8d9213a31839f9a3a</td>
      <td>Russia</td>
      <td>4c3ec14bee</td>
      <td>direct</td>
      <td>direct</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1549980715</td>
      <td>96a296d224</td>
      <td>https://host.ru/b8b58337d272ee7b15</td>
      <td>Russia</td>
      <td>a8c40697fb</td>
      <td>yandex</td>
      <td>organic</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1549980725</td>
      <td>709e80c884</td>
      <td>https://host.ru/b8b58337d272ee7b15</td>
      <td>Russia</td>
      <td>521ac1d6a0</td>
      <td>yandex</td>
      <td>organic</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1549980736</td>
      <td>df3f619804</td>
      <td>https://host.ru/b8b58337d272ee7b15</td>
      <td>Russia</td>
      <td>d7323c571c</td>
      <td>yandex</td>
      <td>organic</td>
    </tr>
  </tbody>
</table>
</div>



[к оглавлению](#contents)

<a id='task2'></a>
## Задание 2


```python
route = r'D:\Google Disk\Учеба\Data Scientist с нуля до middle\Python\NUMPY-21_practical_works\2021.06.28_Продвинутый Pandas\12_доп_материалы_к_пандас_1_и_2\URLs.txt'

df = pd.read_csv(route) # читаем исходник txt в dataframe
df.head()
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
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>/world/</td>
    </tr>
    <tr>
      <th>1</th>
      <td>/latest/</td>
    </tr>
    <tr>
      <th>2</th>
      <td>/?updated=top</td>
    </tr>
    <tr>
      <th>3</th>
      <td>/politics/36188461-s-marta-zhizn-rossiyan-susc...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>/world/36007585-tramp-pridumal-kak-reshit-ukra...</td>
    </tr>
  </tbody>
</table>
</div>




```python
pattern = r'\/\d{8}-'
df.loc[df.url.str.contains(pattern, regex=True)]
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
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>/politics/36188461-s-marta-zhizn-rossiyan-susc...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>/world/36007585-tramp-pridumal-kak-reshit-ukra...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>/science/36157853-nasa-sobiraet-ekstrennuyu-pr...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>/video/36001498-poyavilis-pervye-podrobnosti-g...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>/world/36007585-tramp-pridumal-kak-reshit-ukra...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>88</th>
      <td>/cis/35984145-kreml-prokommentiroval-soobschen...</td>
    </tr>
    <tr>
      <th>89</th>
      <td>/video/36071019-olimpiyskie-obekty-rio-prevrat...</td>
    </tr>
    <tr>
      <th>90</th>
      <td>/science/36151301-nazvano-posledstvie-zloupotr...</td>
    </tr>
    <tr>
      <th>91</th>
      <td>/incidents/36027330-vospitatelnitsu-zatravili-...</td>
    </tr>
    <tr>
      <th>92</th>
      <td>/world/36103095-dominikanskih-zhurnalistov-ubi...</td>
    </tr>
  </tbody>
</table>
<p>84 rows × 1 columns</p>
</div>



[к оглавлению](#contents)

<a id='task3'></a>
## Задание 3


```python
route = r'D:\Google Disk\Учеба\Data Scientist с нуля до middle\Python\NUMPY-21_practical_works\2021.06.28_Продвинутый Pandas\12_доп_материалы_к_пандас_1_и_2\ml-latest-small\ratings.csv'

df = pd.read_csv(route) # читаем исходник csv в dataframe
df.head()
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
import numpy as np

# производим группировку с одновременной агрегацией: считаем количество записей по рейтингам в рамках одного ID, а также
# минимальное и максимальное значение timestamp для каждого id
df_grouped = df.groupby('userId').agg({'rating': 'count', 'timestamp': [np.min, np.max]})
df_grouped.head()
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

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>rating</th>
      <th colspan="2" halign="left">timestamp</th>
    </tr>
    <tr>
      <th></th>
      <th>count</th>
      <th>amin</th>
      <th>amax</th>
    </tr>
    <tr>
      <th>userId</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>20</td>
      <td>1260759108</td>
      <td>1260759205</td>
    </tr>
    <tr>
      <th>2</th>
      <td>76</td>
      <td>835355395</td>
      <td>835356246</td>
    </tr>
    <tr>
      <th>3</th>
      <td>51</td>
      <td>1298861589</td>
      <td>1298932787</td>
    </tr>
    <tr>
      <th>4</th>
      <td>204</td>
      <td>949778714</td>
      <td>949982274</td>
    </tr>
    <tr>
      <th>5</th>
      <td>100</td>
      <td>1163373044</td>
      <td>1163375145</td>
    </tr>
  </tbody>
</table>
</div>




```python
# отфильтровываем, оставляя только id с количеством оценок более 100, одновременно создавая для них новый столбец life_time,
# представляющий собой разницу между максимальным и минимальным временем входа (timestamp)
df_grouped.loc[df_grouped.rating['count'] > 100, 'life_time'] = df_grouped.timestamp['amax'] - df_grouped.timestamp['amin']
# исключаем всех, кто не соответствовал фильтру, т.е. timestamp = NaN
df_grouped = df_grouped.loc[~df_grouped.life_time.isnull()]
df_grouped.head()
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

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>rating</th>
      <th colspan="2" halign="left">timestamp</th>
      <th>life_time</th>
    </tr>
    <tr>
      <th></th>
      <th>count</th>
      <th>amin</th>
      <th>amax</th>
      <th></th>
    </tr>
    <tr>
      <th>userId</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>204</td>
      <td>949778714</td>
      <td>949982274</td>
      <td>203560</td>
    </tr>
    <tr>
      <th>8</th>
      <td>116</td>
      <td>1154389340</td>
      <td>1154474527</td>
      <td>85187</td>
    </tr>
    <tr>
      <th>15</th>
      <td>1700</td>
      <td>997937239</td>
      <td>1469330735</td>
      <td>471393496</td>
    </tr>
    <tr>
      <th>17</th>
      <td>363</td>
      <td>1127468587</td>
      <td>1127476640</td>
      <td>8053</td>
    </tr>
    <tr>
      <th>19</th>
      <td>423</td>
      <td>855190091</td>
      <td>855195373</td>
      <td>5282</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_grouped.life_time.mean() # считаем среднее по столбцуф
```




    40080507.4496124



[к оглавлению](#contents)

<a id='task4'></a>
## Задание 4

Дана статистика услуг перевозок клиентов компании по типам:
- rzd - железнодорожные перевозки
- auto - автомобильные перевозки
- air - воздушные перевозки
- client_base - адреса клиентов


```python
rzd = pd.DataFrame(
    {
        'client_id': [111, 112, 113, 114, 115],
        'rzd_revenue': [1093, 2810, 10283, 5774, 981]
    }
)
rzd
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
      <th>client_id</th>
      <th>rzd_revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>111</td>
      <td>1093</td>
    </tr>
    <tr>
      <th>1</th>
      <td>112</td>
      <td>2810</td>
    </tr>
    <tr>
      <th>2</th>
      <td>113</td>
      <td>10283</td>
    </tr>
    <tr>
      <th>3</th>
      <td>114</td>
      <td>5774</td>
    </tr>
    <tr>
      <th>4</th>
      <td>115</td>
      <td>981</td>
    </tr>
  </tbody>
</table>
</div>




```python
auto = pd.DataFrame(
    {
        'client_id': [113, 114, 115, 116, 117],
        'auto_revenue': [57483, 83, 912, 4834, 98]
    }
)
auto
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
      <th>client_id</th>
      <th>auto_revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>113</td>
      <td>57483</td>
    </tr>
    <tr>
      <th>1</th>
      <td>114</td>
      <td>83</td>
    </tr>
    <tr>
      <th>2</th>
      <td>115</td>
      <td>912</td>
    </tr>
    <tr>
      <th>3</th>
      <td>116</td>
      <td>4834</td>
    </tr>
    <tr>
      <th>4</th>
      <td>117</td>
      <td>98</td>
    </tr>
  </tbody>
</table>
</div>




```python
air = pd.DataFrame(
    {
        'client_id': [115, 116, 117, 118],
        'air_revenue': [81, 4, 13, 173]
    }
)
air
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
      <th>client_id</th>
      <th>air_revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>115</td>
      <td>81</td>
    </tr>
    <tr>
      <th>1</th>
      <td>116</td>
      <td>4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>117</td>
      <td>13</td>
    </tr>
    <tr>
      <th>3</th>
      <td>118</td>
      <td>173</td>
    </tr>
  </tbody>
</table>
</div>




```python
client_base = pd.DataFrame(
    {
        'client_id': [111, 112, 113, 114, 115, 116, 117, 118],
        'address': ['Комсомольская 4', 'Энтузиастов 8а', 'Левобережная 1а', 'Мира 14', 'ЗЖБИиДК 1', 
                    'Строителей 18', 'Панфиловская 33', 'Мастеркова 4']
    }
)
client_base
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
      <th>client_id</th>
      <th>address</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>111</td>
      <td>Комсомольская 4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>112</td>
      <td>Энтузиастов 8а</td>
    </tr>
    <tr>
      <th>2</th>
      <td>113</td>
      <td>Левобережная 1а</td>
    </tr>
    <tr>
      <th>3</th>
      <td>114</td>
      <td>Мира 14</td>
    </tr>
    <tr>
      <th>4</th>
      <td>115</td>
      <td>ЗЖБИиДК 1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>116</td>
      <td>Строителей 18</td>
    </tr>
    <tr>
      <th>6</th>
      <td>117</td>
      <td>Панфиловская 33</td>
    </tr>
    <tr>
      <th>7</th>
      <td>118</td>
      <td>Мастеркова 4</td>
    </tr>
  </tbody>
</table>
</div>



Таблица с тремя типами выручки для каждого client_id без указания адреса клиента


```python
merged_table = rzd.merge(auto, how='outer').merge(air, how='outer')
merged_table.fillna(0)
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
      <th>client_id</th>
      <th>rzd_revenue</th>
      <th>auto_revenue</th>
      <th>air_revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>111</td>
      <td>1093.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>112</td>
      <td>2810.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>113</td>
      <td>10283.0</td>
      <td>57483.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>114</td>
      <td>5774.0</td>
      <td>83.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>115</td>
      <td>981.0</td>
      <td>912.0</td>
      <td>81.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>116</td>
      <td>0.0</td>
      <td>4834.0</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>117</td>
      <td>0.0</td>
      <td>98.0</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>118</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>173.0</td>
    </tr>
  </tbody>
</table>
</div>



Таблица по типам выручки с указанием адреса клиента


```python
full_table = client_base.merge(merged_table,  how='left')
full_table.fillna(0)
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
      <th>client_id</th>
      <th>address</th>
      <th>rzd_revenue</th>
      <th>auto_revenue</th>
      <th>air_revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>111</td>
      <td>Комсомольская 4</td>
      <td>1093.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>112</td>
      <td>Энтузиастов 8а</td>
      <td>2810.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>113</td>
      <td>Левобережная 1а</td>
      <td>10283.0</td>
      <td>57483.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>114</td>
      <td>Мира 14</td>
      <td>5774.0</td>
      <td>83.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>115</td>
      <td>ЗЖБИиДК 1</td>
      <td>981.0</td>
      <td>912.0</td>
      <td>81.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>116</td>
      <td>Строителей 18</td>
      <td>0.0</td>
      <td>4834.0</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>117</td>
      <td>Панфиловская 33</td>
      <td>0.0</td>
      <td>98.0</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>118</td>
      <td>Мастеркова 4</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>173.0</td>
    </tr>
  </tbody>
</table>
</div>



[к оглавлению](#contents)
