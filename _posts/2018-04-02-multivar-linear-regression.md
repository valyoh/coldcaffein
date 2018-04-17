---
layout: post
title: Линейна регресия на много променливи
tags: [Machine Learning]
---

На [предишната]({{ site.baseurl }}/2018/01/28/linear-regression.html) лекция разгледахме линейна регресия на една променлива. Също така генерализирахме формулата за хипотезата на линейна регсия, така че да работи с множество атрибути. За домашно имахме да имплементираме линейна регресия на много променливи, като за обучаващи данни трябваше да използваме данните от файл houseprices2.txt
Сега нака да разгледаме решението на домашното.


```python
# обичайните заподозрени 
# с тези импорти започваме почти всеки ноутбук
import os  
import numpy as np  
import pandas as pd  
import matplotlib.pyplot as plt  
%matplotlib inline
```

Нека първо да заредим данните. Правим това с помоща на библиотеката pandas и функция read_csv(). Файлът с данни не съдържа заглавен ред и затова ръчно задаваме етикети на колоните - размер, брой стаи и цена. Заредените по този начин данните се съхраняват в обект - dataframe. С извикване на функцията head() можем да видим първите няколко реда от данните.


```python
path = os.getcwd() + '/houseprices2.txt'  
df = pd.read_csv(path, header=None, names=['Size', 'Rooms', 'Price'])  
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
      <th>Size</th>
      <th>Rooms</th>
      <th>Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2104</td>
      <td>3</td>
      <td>399900</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1600</td>
      <td>3</td>
      <td>329900</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2400</td>
      <td>3</td>
      <td>369000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1416</td>
      <td>2</td>
      <td>232000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3000</td>
      <td>4</td>
      <td>539900</td>
    </tr>
  </tbody>
</table>
</div>



Друга полезна финцкия на pandas е describe(), която ни дава някои базови статистически данни за множеството. Това е полезно за да придобием представа с какви данни си имаме работа.


```python
df.describe()
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
      <th>Size</th>
      <th>Rooms</th>
      <th>Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>47.000000</td>
      <td>47.000000</td>
      <td>47.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>2000.680851</td>
      <td>3.170213</td>
      <td>340412.659574</td>
    </tr>
    <tr>
      <th>std</th>
      <td>794.702354</td>
      <td>0.760982</td>
      <td>125039.899586</td>
    </tr>
    <tr>
      <th>min</th>
      <td>852.000000</td>
      <td>1.000000</td>
      <td>169900.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1432.000000</td>
      <td>3.000000</td>
      <td>249900.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1888.000000</td>
      <td>3.000000</td>
      <td>299900.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2269.000000</td>
      <td>4.000000</td>
      <td>384450.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>4478.000000</td>
      <td>5.000000</td>
      <td>699900.000000</td>
    </tr>
  </tbody>
</table>
</div>



Обърнете внимание че стойностите в различните колони са в много различен порядък. Броят на стаите е $10^1$. Размерът е в порядък $10^3$, а цената e в порядък $10^5$. Ако приложим линейна регресия върху тези данни директно приносът на "размер" ще бъде непропрционално голям спрямо "брой стаи". За да решим този проблем правим нормализация да данните. Тоест искаме всички данни да бъдат в една и съща скала. Обикновенно нормализацията се прави като от всяка стойност на даден атрибут се извади средната стойност за атрибута и след това получената стойност се раздели на стандартното отклонение. Формулата е написана по-долу.

$$x'= \frac {x - \bar{x}}{\sigma}$$ 

Използвайки pandas това се прави на един ред.


```python
histData = df.copy(deep=True)

#df = (df - df.mean()) / df.std()  
df = (df - df.mean()) / (df.max() - df.min())  
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
      <th>Size</th>
      <th>Rooms</th>
      <th>Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.028494</td>
      <td>-0.042553</td>
      <td>0.112240</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.110502</td>
      <td>-0.042553</td>
      <td>-0.019835</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.110127</td>
      <td>-0.042553</td>
      <td>0.053938</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.161247</td>
      <td>-0.292553</td>
      <td>-0.204552</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.275598</td>
      <td>0.207447</td>
      <td>0.376391</td>
    </tr>
  </tbody>
</table>
</div>



След това трябва да модифицираме имплементацията на линейната регресия, така че да работи с множество независими променливи. Нека да видм кода на градиентното спускане.


```python

def compute_cost(X, y, theta):  
    inner = np.power(((X * theta.T) - y), 2)
    return np.sum(inner) / (2 * len(X))

def gradient_descent(X, y, theta, alpha, num_iters):  
    temp = np.matrix(np.zeros(theta.shape))
    parameters = int(theta.ravel().shape[1])
    cost = np.zeros(num_iters)

    for i in range(num_iters):
        error = (X * theta.T) - y

        for j in range(parameters):
            term = np.multiply(error, X[:,j])
            temp[0,j] = theta[0,j] - ((alpha / len(X)) * np.sum(term))

        theta = temp
        cost[i] = compute_cost(X, y, theta)

    return theta, cost
```

Нека да разгледаме кода изчисляващ грешката - това е имплементирано във функцията compute_cost. Тук използваме умножение на матрици макар това да не e очевидно на пръв поглед. Този код ще работи коректно без значение колко променливи (колони) има X, стига броят на параметрите за theta да бъде същият, като броят на променливите в X.

Ценовата функция от предишната лекция може да бъде използвана директо. Тук обаче сме я пренаписали, като изпозваме матрично умножение от пакета numpy. Това е така наречената векторизирана имплементация. Тази имплементация е изключеително бърза тък като numpy е високо оптимизиран пакет за линейна алгебра.

Нака да проверим какво сме направили. Първо трябва да инициализираме няколко матрици, които да подаваме на нашите функции.


```python
# добявме една нова колона с единници
# това е изкуственият параметър x0
df.insert(0, 'Ones', 1)

# сетваме X (обучаващите данни) и y (целевата променлива)
cols = df.shape[1]  
X = df.iloc[:,0:cols-1]  
y = df.iloc[:,cols-1:cols]

# конвертираме стойностите в numpy матрици и инициализираме theta
X = np.matrix(X.values)  
y = np.matrix(y.values)  
theta = np.matrix(np.array([0,0,0]))  
```

Сега вече може да приложим линейната регресия.


```python
# инициализираме променливи за скоростта на обучение и броят итерации
alpha = 0.5  
num_iters = 1000

# прилагаме градиентното спускане за множеството
g, cost = gradient_descent(X, y, theta, alpha, num_iters)

# изчисляваме грешката на модела
compute_cost(X, y, g) 
```




    0.0072740479559596288



Сега можем да отпечатаме грешката като функция на броя итерации, за да се уверим, че грешката в действителност намалява в процеса на обучение на нашият модел.


```python
fig, ax = plt.subplots(figsize=(10,6))  
ax.plot(np.arange(num_iters), cost, 'r')  
ax.set_xlabel('Iterations')  
ax.set_ylabel('Error')  
ax.set_title('Error vs. Iterations')  
plt.show()
```


![png]({{ site.baseurl }}/assets/img/regression/error_vs_iterations.png)


Накрая визуализираме нашето предвиждане за цената на апартаментита, базирано на двете променливи - размер на жилището и брой стаи.


```python
x = np.linspace(df.Size.min(), df.Size.max(), 100)  
y = np.linspace(df.Rooms.min(), df.Rooms.max(), 100)  
f = g[0, 0] + (g[0, 1] * x) + (g[0, 2] * y)

import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')
ax.plot(x, y, f, 'r')  
ax.scatter(df.Size, df.Rooms, df.Price) 
ax.set_xlabel('Size')
ax.set_ylabel('Rooms')
ax.set_zlabel('Price Predict')
plt.show()
```


![png]({{ site.baseurl }}/assets/img/regression/3d_plot.png)


Когато правим предвиждания с нормализирани данни след като направим нашето предсказание е добре да извършим денормализация, така че да виждаме говорещи стойности за целевата променлива.


```python
def denormalize(x, std, mean):
    return x * std + mean

dn = denormalize(-0.220817, 794.702354, 2000.680851)

print(dn)

xs = df.Size * histData.Size.std() + histData.Size.mean()
ys = df.Rooms * histData.Rooms.std() + histData.Rooms.mean() 
zs = df.Price * histData.Price.std() + histData.Price.mean()
```

    1825.1970613

