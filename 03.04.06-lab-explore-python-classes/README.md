## Цели

* **Часть 1: Запуск виртуальной машины DEVASC**
* **Часть 2: Функции, методы и классы обзора**
* **Часть 3: определение функции**
* **Часть 4: Определение класса с помощью методов**
* **Часть 5: Обзор скрипта circleClass.py**

## Предпосылки / Сценарий

В этой лабораторной работе вы рассмотрите методы, функции и классы Python. Затем вы создаете класс и несколько раз инстанцируете его с разными значениями. Наконец, вы рассмотрите пример класса Circle, использованный в курсе.

## Необходимые ресурсы

* 1 ПК с операционной системой по вашему выбору
* Virtual Box или VMWare
* Виртуальная машина DEVASC

## Инструкции

### Часть 1: Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC сейчас.

### Часть 2: Обзор функций, методов и классов

В этой части вы рассмотрите разницу между функциями и методами. Вы также посмотрите базовую структуру класса.

**Шаг 1: Что такое функция?**

Напомним, что функция - это независимо определенный блок кода, который вызывается по имени. В следующем примере функция с именем **functionName** определяется и затем вызывается. Обратите внимание, что это независимый блок кода. Он не инкапсулируется ни в какой другой код.

```python
# Define the function
def functionName:
    ...blocks of code...

# Call the function
functionName()
```

**Шаг 2: Что такое метод?**

Однако метод не может быть вызван сам по себе. Он зависит от объекта, в котором он определен. В следующем примере объявлен класс **className** и определены три метода. Создается экземпляр класса, а затем вызывается каждый метод класса.

> **Примечание**: Этот псевдокод не показывает явно метод конструктора класса **__init__** с переменной **self**. Этот специальный метод рассматривается ниже.

```python
# Определение класса
class className

    # Определение метода
    def method1Name
        ...blocks of code

    # Определение другого метода
    def method2Name
        ...blocks of code

    # Определение еще одного метода
    def method3Name
        ...blocks of code

# Инстанцирование класса
myClass = className()

# Вызов инстанса и связанного метода
myClass.method1Name()
myClass.method2Name()
myClass.method3Name()
```

### Часть 3: Определить функцию

В этой части вы определите функцию с аргументами, а затем вызовете функцию.

1.	Откройте новый текстовый файл и сохраните его как **myCity.py** в каталоге **~/labs/devnet-src/python**.

2.	Определите функцию `myCity` с аргументом `city` в качестве названия города. Когда функция вызывается с указанным названием города, она печатает оператор, который включает название города.

    ```python
    def myCity(city):
        print("I live in " + city + ".")
    ```

3.	Вызовите функцию `myCity`, передав ей разные значения для города, как показано в следующих примерах.

    ```python
    myCity("Austin")
    myCity("Tokyo")
    myCity("Salzburg")
    ```

4.	Сохраните и запустите файл **myCity.py**. Вы должны получить следующий результат.

    ```
    devasc@labvm:~/labs/devnet-src/python$ python3 myCity.py 
    I live in Austin.
    I live in Tokyo.
    I live in Salzburg.
    devasc@labvm:~/labs/devnet-src/python$
    ```

### Часть 4: Определите класс с помощью методов

В этой части вы определите класс, воспользуетесь функцией `__init__()` для определения метода класса, а затем создадите экземпляры класса.

**Шаг 1: Определите, а затем создайте экземпляр класса с помощью метода `__init__()`.**

Класс Python используется для создания объектов, имеющих свойства и методы. Все классы Python обычно включают явно определенную функцию `__init__()`, хотя вы можете создать класс, не определяя ее. Функция `__init__()` всегда запускается при создании экземпляра класса. Создание экземпляра класса создает копию класса, которая наследует все переменные и методы класса.

> Примечание: Хотя ее иногда называют функцией` __init__()`, она зависит от класса. Следовательно, технически это метод.

1.	Откройте новый текстовый файл и сохраните его как **myLocation.py**. 

2.	Определите класс с именем Location и нажмите Enter. Если вы работаете с VS Code, то текстовый редактор должен автоматически делать отступ в четыре пробела.

    ```python
    class Location:
        |<-- сейчас курсор должен быть здесь
    ```

3.	Затем определите функцию `__init__()`. По соглашению первый параметр называется `self`. Параметр `self` – это ссылка на текущий экземпляр самого класса и используется для доступа к переменным, принадлежащим всему классу. Затем функции `__init__()` присваиваются любые переменные, которые нужны всему классу. В следующем примере определите имя и переменную country. Дважды нажмите Enter, а затем дважды нажмите клавишу Backspace до левого поля.

    ```python
        def __init__(self, name, country):
            self.name = name
            self.country = country

    |<-- сейчас курсор должен быть здесь
    ```

4.	Вы можете проверить, готов ли этот класс к использованию. Создайте экземпляр класса, присвоив ему имя по вашему выбору. Затем укажите значения для необходимых переменных класса `name` и `country`. В следующем примере класс Location используется для создания экземпляра класса `loc` с указанными вами `name` и `country`. Используйте свое имя и страну.

    ```python
    loc = Location("Ваше имя", "Ваша страна")
    ```

5.	Чтобы убедиться, что созданный экземпляр класса `loc` теперь имеет назначенное вами имя и страну, добавьте операторы печати в свой сценарий. 

    ```python
    print(loc.name)
    print(loc.country)
    ```

6.	Чтобы убедиться, что `loc` действительно является классом, добавьте следующий оператор печати, который будет печатать тип данных для `loc`.

    ```python
    print(type(loc))
    ```

7.	Сохраните и запустите ваш скрипт. Вы должны получить следующий результат, кроме указанного вами имени и страны.

    ```
    devasc@labvm:~/labs/devnet-src/python$ python3 myLocation.py 
    Your_Name
    Your_Country
    <class '__main__.Location'>
    devasc@labvm:~/labs/devnet-src/python$
    ```

**Шаг 2: Добавьте метод в класс Location.**

Теперь добавьте метод к классу `Location`, который может быть вызван программистом при создании экземпляра класса. В этом простом примере создайте метод для печати утверждения: “My name is [name] and I live in [country].”.

1.	Удалите код, который начинается с создания экземпляра класса `loc`. Ваш скрипт **myLocation.py** теперь должен включать только следующий код.

    ```python
    class Location:
        def __init__(self, name, country):
            self.name = name
            self.country = country
    ```

2.	Поместив курсор в конец строки `self.country = country`, дважды нажмите клавишу Enter и один раз - назад.

    ```python
            self.country = country

        |<-- сейчас курсор должен быть здесь
    ```

3.	Определите вызов нового метода `myLocation` и назначьте ему параметр `self`, чтобы новый метод мог получить доступ к переменным, определенным в функции `__init__()`. Затем определите оператор печати для печати указанной выше строки.

    > Примечание: Оператор печати должен быть в одной строке.

    ```python
        def myLocation(self):
            print("Hi, my name is " + self.name + " and I live in " + self.country + ".")
    ```

4.	Дважды нажмите клавишу Enter и дважды нажмите клавишу возврата.

5.	Сохраните и запустите ваш скрипт, чтобы убедиться в отсутствии ошибок. Вы пока не получите никаких результатов.

**Шаг 3: Создайте экземпляр класса `Location` несколько раз и вызовите метод `myLocation`.**

Теперь, когда у вас есть класс, вы можете создавать его экземпляры столько раз, сколько хотите, каждый раз предоставляя разные значения для переменных класса.

1.	Добавьте следующий код в сценарий **myLocation.py**, чтобы создать экземпляр класса `Location` и вызвать метод. Комментировать не нужно.

    ```python
    # First instantiation of the class Location 
    loc1 = Location("Tomas", "Portugal")
    # Call a method from the instantiated class
    loc1.myLocation()
    ```

2.	Сохраните и запустите ваш скрипт. Вы должны получить следующий результат.

    ```
    devasc@labvm:~/labs/devnet-src/python$ python3 myLocation.py 
    Hi, my name is Tomas and I live in Portugal.
    devasc@labvm:~/labs/devnet-src/python$
    ```

3.	Добавьте еще два экземпляра, а затем четвертый, в котором вы указываете имя и значения для `your_loc`.

    ```python
    loc2 = Location("Ying", "China")
    loc3 = Location("Amare", "Kenya")
    loc2.myLocation()
    loc3.myLocation()
    your_loc = Location("Your_Name", "Your_Country")
    your_loc.myLocation()
    ```

4.	Сохраните и запустите ваш скрипт. Вы должны получить следующий результат.

    ```
    devasc@labvm:~/labs/devnet-src/python$ python3 myLocation.py 
    Hi, my name is Tomas and I live in Portugal.
    Hi, my name is Ying and I live in China.
    Hi, my name is Amare and I live in Kenya.
    Hi, my name is Your_Name and I live in Your_Country.
    devasc@labvm:~/labs/devnet-src/python$
    ```

**Шаг 4: Просмотрите полный сценарий myLocation.py.**

Если у вас были какие-либо ошибки в сценарии, просмотрите следующий пример, который включает весь код, используемый в этой части.

```python
# Define a class with variables for **name** and **country**.
# Then define a method that belongs to the class. The method’s 
# purpose is to print a sentence that uses the variables.
class Location:
    def __init__(self, name, country):
        self.name = name
        self.country = country

    def myLocation(self):
        print("Hi, my name is " + self.name + " and I live in " + self.country + ".")

# First instantiation of the Location class
loc1 = Location("Tomas", "Portugal")
# Call a method from the instantiated class
loc1.myLocation()

# Three more instantiations and method calls for the Location class
loc2 = Location("Ying", "China")
loc3 = Location("Amare", "Kenya")
loc2.myLocation()
loc3.myLocation()
your_loc = Location("Your_Name", "Your_Country")
your_loc.myLocation()
```

### Часть 5: Просмотрите скрипт circleClass.py

Пример в курсе показывает, как создать класс, который вычисляет длину окружности, а затем распечатывает вычисленное значение. В этом сценарии следует отметить несколько моментов.

* Класс включает три метода, включая функцию `__init__()`. Функция `__init__()` предоставляет метод для ввода значения радиуса.
* Метод окружности вычисляет длину окружности и возвращает значение, сохраняя его в переменной `circleValue`. 
* Метод `printCircumference` печатает строку. Обратите внимание, что переменные приводятся как строки с помощью функции `str()`. В противном случае оператор печати вызовет ошибку, поскольку `self.radius` и `myCircumference` не являются строками.
* Класс `Circle` создан трижды.

    ```python
    # Given a radius value, print the circumference of a circle.
    # Formula for a circumference is c = pi * 2 * radius

    class Circle:

        def __init__(self, radius):
            self.radius = radius

        def circumference(self):
        pi = 3.14
        circumferenceValue = pi * self.radius * 2
        return circumferenceValue

        def printCircumference(self):
        myCircumference = self.circumference()
        print ("Circumference of a circle with a radius of " + str(self.radius) + " is " + str(myCircumference))

    # First instantiation of the Circle class.
    circle1 = Circle(2)
    # Call the printCircumference for the instantiated circle1 class.
    circle1.printCircumference()

    # Two more instantiations and method calls for the Circle class.
    circle2 = Circle(5)
    circle2.printCircumference()

    circle3 = Circle(7)
    circle3.printCircumference()
    ```

