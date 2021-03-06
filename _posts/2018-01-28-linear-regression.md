---
layout: post
title: Линейна регресия
tags: [Machine Learning]
---

Линейната регресия преставлява метод за построяване на връзка между множество от независими променливи $$x_1, x_2, ..., x_n$$ и зависима променлива $$y$$. Използвайки линейна регресия с една независима променлива ще построим прост предсказващ модел. Предположението, което правим е, че връзката между зависимата и независимата променлива е линейна.

Таблицата по-долу съдържа записи за наеми на имоти, заедно с някои техни параметри, като **размер**, **етаж** и **енергиен клас**. Параметрите на жилището са независимите променливи, а **наем** е зависима променлива, която искаме да предвидим. Тъй като разглеждаме линейна регресия с една променлива, като независима променлива за нашият модел ще изпозваме **размер**.

| Размер (кв. м.) | Етаж | Енергиен клас | Наем |
| :-----|-----|-----|-----|
| 500 | 4 | C | 320 |
| 550 | 7 | A | 380 |
| 620 | 9 | A | 400 |
| 630 | 5 | B | 390 |
| 665 | 8 | C | 385 |
| 700 | 4 | B | 410 |
| 770 | 10 | B | 480 |
| 880 | 12 | A | 600 |
| 920 | 14 | C | 570 |
| 1000 | 9 | B | 620 |

## Представяне на модела

Терминологията използвана в машиното самообучение (**MC**) се различава от тази използвана в статистиката. Нека да въведем следните означения.
Ще използваме $$x^{(i)}$$ за да означаваме "входящата" променлива и $$y^{(i)}$$ за "изходящата" или целевата променлива - тази, която се опитваме да предвидим. Двойка от вида $$(x^{(i)},y^{(i)})$$ наричаме обучаващ пример. Множеството, което съдъража списък от $$m$$ обучаващи примера $$(x^{(i)},y^{(i)})$$ където $$i=1,...,m$$ наричаме обучаващо множество. Интексът $$(i)$$ е номерът на реда в обучаващото множество и няма нищо общо със степенуването. Също така ще използваме $$X$$ за да означаваме множеството на входящите атрибути и $$Y$$ за множеството на целевите променливи, когато имаме повече входящи и изходящи параметри. 

Да опишем обучението с учител малко по-формално. Нашата цел е по зададено обучаващо множество да научим функция $$h : X \rightarrow Y$$ така че тя да предвижда добре съответните стойности на **у**. Поради исторически причини тази функция се нарича хипотеза. Представено графично може да се види на картинката по долу.
![ML Pipeline]({{ site.baseurl }}/assets/img/regression/pipeline.png)

Хипотезата за линейна регресия на една променлива има следният вид:

$$h_\theta(x^{(i)}) = \theta_0 + \theta_1x^{(i)}$$

Когато целевата променлива която се опитваме да предвидим е непрекъсната имаме задача за регресия. Когато y може да приема множество от дискретни стойности имаме класификационна задача.

## Ценова функция

Измерването на точността на хипотезата става чрез използването на ценова (оценяваща) функция. Тя взема средната разлика от всички резултати на хипотезата и реланият резултат $$y$$ от обучаващото множество.
$$J(\theta_0, \theta_1) = \dfrac {1}{2m} \displaystyle \sum _{i=1}^m \left ( \hat{y}^{(i)}- y^{(i)} \right)^2 = \dfrac {1}{2m} \displaystyle \sum _{i=1}^m \left (h_\theta (x^{(i)}) - y^{(i)} \right)^2$$

Формулата може да бъде записана и като, $$J(\theta_0, \theta_1) = \frac{1}{2} \bar{x}$$ където $$\bar{x}$$ е средното от квадратите на $$h_\theta (x_{i}) - y_{i}$$, или с други думи разликите между предсказаните стойности $$\hat{y}^{(i)}$$ и дествителните стойности $${y}^{(i)}$$.
Тази функция се нарича средно квадратична грешка или MSE (Mean squared error). По конвенция се взема $$\dfrac {1}{2}$$ от грешката за удобство при изчисляването на градиентното спускане, тъй като производната на MSE се съкращава с $$\dfrac {1}{2}$$.

След като имаме функция, с която да оценим точността на нашата хипотеза следва да намерим най-добрата линия, която описва наличните данни. Тоест трябва да минимизираме функцията J. В следващата точка ще видим как правим това.

## Градиентно спускане

Градиентното спускане е един от най-важните алгоритми в областа на МС. Неговата задача е да намери минимума на дадена функция.
Схематично на високо ниво алгоритъмът изглежда така:

1. инициализираме параметрите на функцията $$\theta_0, \theta_1$$ със случайни стойности (например нула).
2. променяме стойностите на параметрите така че, стойността на функцията $$J(\theta_0, \theta_1)$$ да намалява докато достигнем до минимум.

С какви точно стойности променяме параметрите $$\theta_0,\theta_1$$ изчисляваме по следнта формула:

$$\theta_j := \theta_j - \alpha \frac{\partial}{\partial \theta_j} J(\theta_0, \theta_1)$$ където $$j =0,1$$ 

$$\alpha$$ е константа, която наричаме скорост на обучение

Важно е да се отбележе, че при имплементацията на алгоритъма параметрите трябва да бъдет обновени едновременно. 
Тоест: 

$$ temp_0 := \theta_0 - \alpha \frac{\partial}{\partial \theta_0} J(\theta_0, \theta_1) $$

$$temp_1 := \theta_1 - \alpha \frac{\partial}{\partial \theta_1} J(\theta_0, \theta_1)$$

$$\theta_0 := temp_0$$

$$\theta_1 := temp_1$$

## Имплементация на алгоритъма


```python
%matplotlib inline

import numpy as np
import matplotlib.pyplot as plt
```


```python


#Evaluate the linear regression
def compute_cost(X, y, theta):
    '''
    Comput cost for linear regression
    '''
    #Number of training samples
    m = y.size

    predictions = X.dot(theta).flatten()
    sqErrors = (predictions - y) ** 2

    J = (1.0 / (2 * m)) * sqErrors.sum()

    return J

# d[(y-(a*x+b))**2,b] = 2 (b + a x - y) = 2 (y_pred - y)
# d[(y-(a*x+b))**2,a] = 2 x (b + a x - y) = x * dy/db

def gradient_descent(X, y, theta, alpha, num_iters):
    '''
    Performs gradient descent to learn theta
    by taking num_items gradient steps with learning
    rate alpha
    '''
    m = y.size
    J_history = np.zeros(shape=(num_iters, 1))

    for i in range(num_iters):

        predictions = X.dot(theta).flatten()

        errors_x1 = (predictions - y) * X[:, 0]
        errors_x2 = (predictions - y) * X[:, 1]

        theta[0][0] = theta[0][0] - alpha * (1.0 / m) * errors_x1.sum()
        theta[1][0] = theta[1][0] - alpha * (1.0 / m) * errors_x2.sum()

        J_history[i, 0] = compute_cost(X, y, theta)

    return theta, J_history
```


```python
x = np.array([500.,550.,620.,630.,660.,700.,770.,880.,920.,1000.]) / 1000
y = np.array([320.,380.,400.,390.,380.,410.,480.,600.,570.,620.]) / 1000

#number of training samples
m = y.size

#Add a column of ones to X (interception data)
it = np.ones(shape=(m, 2))
it[:, 1] = x

#Initialize theta parameters
theta = np.zeros(shape=(2, 1))

#Some gradient descent settings
iterations = 1000
alpha = 0.5

#compute and display initial cost
print("Initial cost: %f" % compute_cost(it, y, theta))

theta, J_history = gradient_descent(it, y, theta, alpha, iterations)

print(theta)
#Predict values for various apartments
predict1 = np.array([1, 720.]).dot(theta).flatten()
print('For size if = 720 (sq. m), we predict a rent of %f' % (predict1))
predict2 = np.array([1, 810.]).dot(theta).flatten()
print('For size if = 810 (sq. m), we predict a rent of %f' % (predict2))

#Plot the results
x = np.array([500.,550.,620.,630.,660.,700.,770.,880.,920.,1000.])
y = np.array([320.,380.,400.,390.,380.,410.,480.,600.,570.,620.]) 
result = it.dot(theta).flatten() * 1000.

plt.scatter(x,y)
plt.plot(x, result, color='red')
plt.xlabel("Size (sq. m)")
plt.ylabel("Rental Price (EUR)")
plt.title("Rental Price Linear Regression")  
plt.legend(['Prediction Line', 'Actual Example'])
plt.show()

plt.plot(J_history)
plt.ylabel("Error")
plt.xlabel("Number of iterations")
plt.show()

```

    Initial cost: 0.108555
    [[ 0.00551614]
     [ 0.62169483]]
    For size if = 720 (sq. m), we predict a rent of 447.625791
    For size if = 810 (sq. m), we predict a rent of 503.578325



![png]({{ site.baseurl }}/assets/img/regression/output_12_1.png)



![png]({{ site.baseurl }}/assets/img/regression/output_12_2.png)

## Линейна регресия на много променливи

Когато искаме да включим повече от една променлива хипотезата придобива следният вид: 

$$h_\theta (x) = \theta_0 + \theta_1 x_1 + \theta_2 x_2 + \theta_3 x_3 + \cdots + \theta_n x_n$$

В този случай ще се използваме следните означения:

$$ x_j^{(i)}$$ - стойността на параметър $$j$$ в i-тия обучаващ пример 

$$x^{(i)}$$ - входящите параметри на i-тия  обучаващ пример

$$m$$ - брой обучващи примери

$$n$$ - брой параметри

Ако използваме умножение на матрици хипотезата може да бъде записана по следният начин:

$$ h_\theta(x) = \begin{bmatrix}
                    \theta_0 
                    \theta_1
                    ... 
                    \theta_n
                  \end{bmatrix}
                  
                  \begin{bmatrix}
                      x_0 \\ 
                      x_1 \\
                      \vdots \\ 
                      x_n
                  \end{bmatrix} = \theta^T x $$

За да може да използваме този запис въвеждаме изкуствен параметър: 
$$x_{0}^{(i)} =1 \textrm{ където } i \in (1, ... , n) $$

Градиентното спускане за множество входящи параметри ще изглежда така:

$$\textrm{repeat until convergence:}$$

$$\theta_j := \theta_j - \alpha \frac{1}{m} \sum\limits_{i=1}^{m} (h_\theta(x^{(i)}) - y^{(i)}) \cdot x_j^{(i)}$$
$$j \in (1, ..., n)$$



### Нормализация на даните

Градиентното спускане може да бъде ускорено, ако всички променливи са приблизително в една и съща скала. Също така чрез нормализацията се уеднаквява приноса на всеки един атрибут. Нека да разгледаме следният пример. Ако имаме два атрибута - брой стаи и квадратура на жилище, то квадратурата ще бъде в пъти по голяма (като абсолютна стойност), от броят стати. Ако не извършим нормализация на данните атрибутът - брой стаи ще има нищожен принос към общата стойност на хипотезата и практически ще бъде игнориран. В идеалният случай стойностите на атрибутите трябва да са в интервала $$ -1 \leq x(i) \leq 1$$

Съществуват два подхода за нормализация на данните. 

* Скалиране на данните, чрез разделяне на всяка стойност на обхвата на данните (разликата межеду максималната и минималната стойност на съответният атрибут). 
* Нормализиране по средна стойност извършваме като от всяка стойност извадим средната стойност за атрибута и разделим на обхвата.

$$ x'= \frac {x - mean(x)}{max(x) - min(x)} $$

Вместо използване на обхвата е възможно също така да делим на стандартното отклонение, за да извършим норамализацията.

$$x'= \frac {x - mean(x)}{\sigma}$$

По долу е показан пример за нормализация на данните чрез използване на пакета pandas и чрез деление на стандартното отклонение.


```python
import os 
import pandas as pd
path = os.getcwd() + '/houseprices1.txt'  
data2 = pd.read_csv(path, header=None, names=['Size', 'Floor', 'Rent'])  
data2 = (data2 - data2.mean()) / data2.std()
data2.head()  
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
      <th>Floor</th>
      <th>Rent</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-1.354055</td>
      <td>-1.262528</td>
      <td>-1.284679</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-1.051134</td>
      <td>-0.360722</td>
      <td>-0.715818</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-0.627046</td>
      <td>0.240481</td>
      <td>-0.526197</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.566462</td>
      <td>-0.961926</td>
      <td>-0.621007</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-0.354417</td>
      <td>-0.060120</td>
      <td>-0.668412</td>
    </tr>
  </tbody>
</table>
</div>


