## Цели

* **Часть 1: Запуск виртуальной машины DEVASC**
* **Часть 2: Изучите параметры в среде unittest**
* **Часть 3: Тестирование функции Python с помощью unittest**

## Предпосылки / Сценарий

Модульные тесты проверяют независимые единицы кода, такие как функции, классы, модули и библиотеки. Есть много причин для написания скрипта с использованием библиотеки Python unittest. Одна из очевидных причин заключается в том, что, если вы обнаружите проблему в изолированном коде путем преднамеренного тестирования, вы знаете, что проблема заключается в функции или другом тестируемом модуле. Проблема не в большом приложении, которое может вызывать эту функцию. Вы также будете точно знать, что вызвало ошибку, поскольку вы написали модульный тест, который выявил проблему. Ошибки, обнаруженные таким образом, обычно быстро и легко исправляются, а исправления, сделанные на этом подробном уровне, с меньшей вероятностью вызовут непредвиденные побочные эффекты позже в другом коде, который полагается на проверенный код.

Вы можете запускать модульные тесты вручную, если код небольшой, но обычно модульные тесты следует автоматизировать. При написании модульного теста подумайте о следующем:

* Модульный тест должен быть простым и легким в реализации.
* Модульный тест должен быть хорошо документирован, чтобы было легко понять, как его запустить, даже через несколько лет.
* Рассмотрите методы тестирования и исходные данные со всех сторон.
* Результаты тестов должны быть согласованными. Это важно для автоматизации тестирования.
* Тестовый код должен работать независимо от тестируемого кода. Если вы пишете тесты, которые должны изменить состояние программы, зафиксируйте состояние перед его изменением и верните его обратно после запуска теста.
* Когда тест не проходит, результаты должны быть легко читаемыми и четко указывать на то, что ожидается, и где есть проблемы.

В этой лабораторной работе вы исследуете фреймворк **unittest** и будете использовать **unittest** для тестирования функции.

## Необходимые ресурсы

* 1 ПК с операционной системой по вашему выбору
* Virtual Box или VMWare
* Виртуальная машина DEVASC

## Инструкции

### Часть 1: Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC.

### Часть 2: Изучите параметры в среде unittest

Python предоставляет платформу модульного тестирования (называемую **unittest**) как часть стандартной библиотеки Python. Если вы не знакомы с этой структурой, изучите «Python unittest Framework», чтобы ознакомиться с ней. Найдите его в Интернете, чтобы найти документацию на python.org. Вам потребуются эти знания или документация, чтобы ответить на вопросы в этой части.

Какой класс **unittest** вы используете для создания отдельной единицы тестирования?

Исполнитель тестов отвечает за выполнение тестов и предоставление вам результатов. Средство выполнения тестов может быть с графическим интерфейсом, но в этой лабораторной работе вы будете использовать командную строку для запуска тестов.

Как исполнитель тестов узнает, какие методы являются тестовыми?

Напишите здесь свои ответы.

Какая команда перечислит все параметры командной строки для **unittest**, показанные в следующих выходных данных?

```
devasc@labvm:~/labs/devnet-src$ python3 -m unittest -h
<output omitted>
optional arguments:
  -h, --help           show this help message and exit
  -v, --verbose        Verbose output
  -q, --quiet          Quiet output
  --locals             Show local variables in tracebacks
  -f, --failfast       Stop on first fail or error
  -c, --catch          Catch Ctrl-C and display results so far
  -b, --buffer         Buffer stdout and stderr during tests
  -k TESTNAMEPATTERNS  Only run tests which match the given substring

Examples:
  python3 -m unittest test_module               - run tests from test_module
  python3 -m unittest module.TestClass          - run tests from module.TestClass
  python3 -m unittest module.Class.test_method  - run specified test method
  python3 -m unittest path/to/test_file.py      - run tests from test_file.py
<output omitted>
For test discovery all test modules must be importable from the top level
directory of the project.
devasc@labvm:~/labs/devnet-src$
```


### Часть 3: Тестирование функции Python с помощью unittest

В этой части вы будете использовать **unittest** для тестирования функции, которая выполняет рекурсивный поиск объекта JSON. Функция возвращает значения, помеченные заданным ключом. Программистам часто требуется выполнять такого рода операции с объектами JSON, возвращаемыми вызовами API.

В этом тесте будут использоваться три файла, как показано в следующей таблице:

| **Файл** | **Описание**                |
| --- | --- |
| **recursive_json_search.py** | Этот скрипт будет включать функцию `json_search()`, которую мы хотим протестировать. |
| **test_data.py**              | Это данные, которые ищет функция `json_search()`. |
| **test_json_search.py**      | Это файл, который вы создадите для тестирования функции `json_search()` в скрипте **recursive_json_search.py**. |

**Шаг 1: Просмотрите файл test_data.py.**

Откройте файл **~/labs/devnet-src/unittest/test_data.py** и изучите его содержимое. Эти данные JSON типичны для данных, возвращаемых при вызове API Cisco DNA Center. Данные образца достаточно сложны, чтобы быть хорошим тестом. Например, в нем чередуются типы `dict` и `list`

```
devasc@labvm:~/labs/devnet-src$ more unittest/test_data.py 
key1 = "issueSummary"
key2 = "XY&^$#*@!1234%^&"

data = {
  "id": "AWcvsjx864kVeDHDi2gB",
  "instanceId": "E-NETWORK-EVENT-AWcvsjx864kVeDHDi2gB-1542693469197",
  "category": "Warn",
  "status": "NEW",
  "timestamp": 1542693469197,
  "severity": "P1",
  "domain": "Availability",
  "source": "DNAC",
  "priority": "P1",
  "type": "Network",
  "title": "Device unreachable",
  "description": "This network device leaf2.abc.inc is unreachable from controller. The device role is ACCESS.",
  "actualServiceId": "10.10.20.82",
  "assignedTo": "",
  "enrichmentInfo": {
    "issueDetails": {
      "issue": [
        {
--More--(12%)
```

**Шаг 2: Создайте функцию `json_search ()`, которую вы будете тестировать.**

Наша функция должна ожидать ключ и объект JSON в качестве входных параметров и возвращать список совпадающих пар ключ/значение. Вот текущая версия функции, которую необходимо протестировать, чтобы убедиться, что она работает должным образом. Цель этой функции – сначала импортировать тестовые данные. Затем она ищет данные, которые соответствуют ключевым переменным в файле **test_data.py**. Если он найдет совпадение, он добавит совпадающие данные в список. Функция `print()` в конце выводит на печать содержимое списка для первой переменной `key1 = "issueSummary"`.

```python
from test_data import *
def json_search(key,input_object):
    ret_val=[]
    if isinstance(input_object, dict): # Iterate dictionary
        for k, v in input_object.items(): # searching key in the dict
            if k == key:
                temp={k:v}
                ret_val.append(temp)
            if isinstance(v, dict): # the value is another dict so repeat
                json_search(key,v)
            elif isinstance(v, list): # it's a list
                for item in v:
                    if not isinstance(item, (str,int)): # if dict or list repeat
                        json_search(key,item)
    else: # Iterate a list because some APIs return JSON object in a list
        for val in input_object:
            if not isinstance(val, (str,int)):
                json_search(key,val)
    return ret_val
print(json_search("issueSummary",data))
```

1.	Откройте файл **~/labs/devnet-src/unittest/recursive_json_search.py**. 

2.	Скопируйте приведенный выше код в файл и сохраните его.

    > **Примечание**: Если вы просматриваете эту лабораторную работу в виде файла PDF, вам может потребоваться отредактировать разрывы строк, чтобы сделать код действительным. Обратите внимание, что встроенные комментарии не должны переходить на следующую строку. При вставке в `recursive_json_search.py` должна быть 21 строка кода, включая открытый комментарий `# Fill the Python code in this file`.

3.	Запустите код. Вы не должны получать ошибок и вывод `[ ]`, указывающий на пустой список. Если функция `json_search()` была закодирована правильно (а это не так), это будет означать, что нет данных с ключом «issueSummary», сообщаемых данными JSON, возвращаемыми API Cisco DNA Center. Другими словами, проблем нет.

    <pre><code>
    devasc@labvm:~/labs/devnet-src/unittest$ python3 recursive_json_search.py 
    <strong><em> []</em></strong>
    devasc@labvm:~/labs/devnet-src/unittest$
    </code></pre>

4.	Но как узнать, что функция `json_search()` работает так, как задумано? Вы можете открыть файл **test_data.py** и выполнить поиск по ключевому слову «issueSummary», как показано ниже. Если бы вы это сделали, вы действительно обнаружили бы проблему. Это небольшой набор данных и относительно простой рекурсивный поиск. Однако производственные данные и код редко бывают такими простыми. Следовательно, тестирование кода жизненно важно для быстрого поиска и исправления ошибок в вашем коде.

    <pre><code>< output omitted >
        "issue": [
            {
            "issueId": "AWcvsjx864kVeDHDi2gB",
            "issueSource": "Cisco DNA",
            "issueCategory": "Availability",
            "issueName": "snmp_device_down",
            "issueDescription": "This network device leaf2.abc.inc is unreachable from controller. The device role is ACCESS.",
            "issueEntity": "network_device",
            "issueEntityValue": "10.10.20.82",
            "issueSeverity": "HIGH",
            "issuePriority": "",
            <strong><em>"issueSummary"</em></strong>: "Network Device 10.10.20.82 Is Unreachable From Controller",
            "issueTimestamp": 1542693469197,
            "suggestedActions": [
                {
    < output omitted >
    </code></pre>

**Шаг 3: Создайте несколько модульных тестов, которые будут проверять, работает ли функция должным образом.**

1.	Откройте файл **~labs/devnet-src/unittest/test_json_search.py**.

2.	В первой строке скрипта после комментария импортируйте библиотеку `unittest`.

    ```
    import unittest
    ```

3.	Добавьте строки для импорта тестируемой функции, а также тестовых данных JSON, которые она использует.

    ```
    from recursive_json_search import *
    from test_data import *
    ```

4.	Теперь добавьте следующий код класса `json_search_test` в файл **test_json_search.py**. Код создает подкласс `TestCase` фреймворка `unittest`. Класс определяет некоторые методы тестирования, которые будут использоваться в функции `json_search()` в скрипте **recursive_json_search.py**. Обратите внимание, что каждый метод тестирования начинается с test_, что позволяет платформе `unittest` обнаруживать их автоматически. Добавьте следующие строки в конец файла **~labs/devnet-src/unittest/test_json_search.py**:

    ```python
    class json_search_test(unittest.TestCase):
        '''test module to test search function in `recursive_json_search.py`'''
        def test_search_found(self):
            '''key should be found, return list should not be empty'''
            self.assertTrue([]!=json_search(key1,data))
        def test_search_not_found(self):
            '''key should not be found, should return an empty list'''
            self.assertTrue([]==json_search(key2,data))
        def test_is_a_list(self):
            '''Should return a list'''
            self.assertIsInstance(json_search(key1,data),list)
    ```

    В коде `unittest` вы используете три метода для проверки функции поиска:

    1.	Учитывая существующий ключ в объекте JSON, посмотрите, может ли тестовый код найти такой ключ.
    2.	Учитывая несуществующий ключ в объекте JSON, посмотрите, подтверждает ли тестовый код, что ключ не может быть найден.
    3.	Проверяем, возвращает ли наша функция список, как всегда.

    Для создания этих тестов сценарий использует некоторые из встроенных методов assert в классе TestCase `unittest` для проверки условий. Метод `assertTrue(x)` проверяет, истинно ли условие, а `assertIsInstance(a, b)` проверяет, является ли `a` экземпляром типа `b`. Используемый здесь тип - `list`.

    Также обратите внимание, что комментарии для каждого метода указываются в тройной одинарной кавычке (`'''`). Это необходимо, если вы хотите, чтобы тест выводил описание метода тестирования при запуске. Использование единственного символа решетки (`#`) для комментария не приведет к распечатке описания неудачного теста.

5.	Для последней части скрипта добавьте метод `unittest.main()`. Это позволяет запускать `unittest` из командной строки. Цель `if __name__ == '__main__'` - убедиться, что метод `unittest.main()` запускается только в том случае, если скрипт запускается напрямую. Если сценарий импортируется в другую программу, `unittest.main()` не запускается. Например, для запуска этого теста вы можете использовать другое средство запуска тестов, отличное от `unittest`.

    ```python
    if __name__ == '__main__':
        unittest.main()
    ```

**Шаг 4: Запустите тест, чтобы увидеть первые результаты.**

1.	Запустите тестовый сценарий в его текущем состоянии, чтобы увидеть, какие результаты он возвращает в данный момент. Сначала вы видите пустой список. Во-вторых, вы видите расширение `.F.` выделено в выводе. Точка (.) Означает, что тест пройден, а F означает, что тест не прошел. Таким образом, первый тест пройден, второй тест не пройден, а третий тест пройден.

    <pre><code>devasc@labvm:~/labs/devnet-src/unittest$ python3 test_json_search.py 
    []
    <strong><em>.F.</em></strong>
    ======================================================================
    FAIL: test_search_found (__main__.json_search_test)
    key should be found, return list should not be empty
    ----------------------------------------------------------------------
    Traceback (most recent call last):
    File "test_json_search.py", line 11, in test_search_found
        self.assertTrue([]!=json_search(key1,data))
    AssertionError: False is not true

    ----------------------------------------------------------------------
    Ran 3 tests in 0.001s

    FAILED (failures=1)
    devasc@labvm:~/labs/devnet-src/unittest$
    </code></pre>

2.	Чтобы перечислить каждый тест и его результаты, снова запустите сценарий в модуле `unittest` с подробным (-v) параметром. Обратите внимание, что вам не нужно расширение .py для сценария `test_json_search.py`. Вы можете видеть, что ваш тестовый метод `test_search_found` не работает.

    > **Примечание**: Python не обязательно запускает ваши тесты по порядку. Тесты выполняются в алфавитном порядке на основе названий методов тестирования.

    <pre><code>devasc@labvm:~/labs/devnet-src/unittest$ python3 -m unittest -v test_json_search
    []
    test_is_a_list (test_json_search.json_search_test)
    Should return a list ... ok
    <strong><em>test_search_found (test_json_search.json_search_test)</em></strong>
    <strong><em>key should be found, return list should not be empty ... FAIL</em></strong>
    test_search_not_found (test_json_search.json_search_test)
    key should not be found, should return an empty list ... ok

    ======================================================================
    FAIL: test_search_found (test_json_search.json_search_test)
    key should be found, return list should not be empty
    ----------------------------------------------------------------------
    Traceback (most recent call last):
    File "/home/devasc/labs/devent-src/unittest/test_json_search.py", line 11, in test_search_found
        self.assertTrue([]!=json_search(key1,data))
    AssertionError: False is not true

    ----------------------------------------------------------------------
    Ran 3 tests in 0.001s

    FAILED (failures=1)
    devasc@labvm:~/labs/devnet-src/unittest$
    </code></pre>

**Шаг 5: Выясните и исправьте первую ошибку в скрипте recursive_json_search.py.**

Утверждение, **ключ должен быть найден, список возврата не должен быть пустым ... FAIL**, указывает, что ключ не был найден. Почему? Если мы посмотрим на текст нашей рекурсивной функции, мы увидим, что оператор `ret_val = [ ]` многократно выполняется при каждом вызове функции. Это приводит к тому, что функция очищает список и теряет накопленные результаты оператора `ret_val.append (temp)`, который добавляется к списку, созданному `ret_val = [ ]`.

```python
def json_search(key,input_object):
    ret_val=[]
    if isinstance(input_object, dict):
        for k, v in input_object.items():
            if k == key:
                temp={k:v}
                ret_val.append(temp)
```

1.	Переместите `ret_val = [ ]` из нашей функции в `recursive_json_search.py`, чтобы итерация не перезаписывала накопленный список каждый раз.

    ```python
    ret_val=[]
    def json_search(key,input_object):
    ```

2.	Сохраните и запустите сценарий. Вы должны получить следующий вывод, подтверждающий, что вы решили проблему. Список больше не пуст после запуска скрипта.

    ```
    devasc@labvm:~/labs/devnet-src/unittest$ python3 recursive_json_search.py 
    [{'issueSummary': 'Network Device 10.10.20.82 Is Unreachable From Controller'}]
    devasc@labvm:~/labs/devnet-src/unittest$
    ```

**Шаг 6: Запустите тест еще раз, чтобы увидеть, все ли ошибки в скрипте теперь исправлены.**

1.	Вы получили какой-то результат при последнем запуске `recursive_json_search.py`, вы еще не можете быть уверены, что устранили все ошибки в скрипте? Снова запустите `unittest` без опции `-v`, чтобы проверить, возвращает ли `test_json_search` какие-либо ошибки. Как правило, вы не хотите использовать параметр -v, чтобы минимизировать вывод на консоль и ускорить выполнение тестов. В начале журнала вы можете увидеть `..F`, что означает, что третий тест не прошел. Также обратите внимание, что список все еще распечатывается. Вы можете остановить это поведение, удалив функцию `print()` в скрипте `resursive_json_search.py`. Но это не обязательно для ваших целей в этой лаборатории.

    <pre><code>devasc@labvm:~/labs/devnet-src/unittest$ python3 -m unittest test_json_search
    [{'issueSummary': 'Network Device 10.10.20.82 Is Unreachable From Controller'}]
    <strong><em>..F</em></strong>
    ======================================================================
    FAIL: test_search_not_found (test_json_search.json_search_test)
    key should not be found, should return an empty list
    ----------------------------------------------------------------------
    Traceback (most recent call last):
    File "/home/devasc/labs/devnet-src/unittest/test_json_search.py", line 14, in test_search_not_found
        self.assertTrue([]==json_search(key2,data))
    AssertionError: False is not true

    ----------------------------------------------------------------------
    Ran 3 tests in 0.001s

    FAILED (failures=1)
    devasc@labvm:~/labs/devnet-src/unittest$
    </code></pre>

2.	Откройте файл `test_data.py` и найдите `issueSummary`, который является значением для key1. Вы должны найти его дважды, но только один раз в объекте `data` JSON. Но если вы выполните поиск значения ключа 2, то есть `XY&^$#*@!1234%^&`, вы найдете его только в верхней части, где оно определено, потому что его нет в объекте данных JSON. Третий тест - убедиться, что его нет. Третий тестовый комментарий указывает, что **ключ не должен быть найден, он должен возвращать пустой список**. Однако функция вернула непустой список.

**Шаг 7: Выясните и исправьте вторую ошибку в скрипте recursive_json_search.py.**

1.	Еще раз просмотрите код `recursive_json_search.py`. Обратите внимание, что `ret_val` теперь является глобальной переменной после того, как вы исправили ее на предыдущем шаге. Это означает, что его значение сохраняется при нескольких вызовах функции `json_search()`. Это хороший пример того, почему использование глобальных переменных в функциях - плохая практика.
2.	Чтобы решить эту проблему, оберните функцию `json_search()` внешней функцией. Удалите существующую функцию `json_search()` и замените ее на реорганизованную ниже: (Не повредит дважды вызвать функцию, но повторять функцию не рекомендуется).

    ```python
    from test_data import *
    def json_search(key,input_object):
        """
        Search a key from JSON object, get nothing back if key is not found
        key : "keyword" to be searched, case sensitive
        input_object : JSON object to be parsed, test_data.py in this case
        inner_function() is actually doing the recursive search
        return a list of key:value pair
        """
        ret_val=[]
        def inner_function(key,input_object):
            if isinstance(input_object, dict): # Iterate dictionary
                for k, v in input_object.items(): # searching key in the dict
                    if k == key:
                        temp={k:v}
                        ret_val.append(temp)
                    if isinstance(v, dict): # the value is another dict so repeat
                        inner_function(key,v)
                    elif isinstance(v, list):
                        for item in v:
                            if not isinstance(item, (str,int)): # if dict or list repeat
                                inner_function(key,item)
            else: # Iterate a list because some APIs return JSON object in a list
                for val in input_object:
                    if not isinstance(val, (str,int)):
                        inner_function(key,val)
        inner_function(key,input_object)
        return ret_val
    print(json_search("issueSummary",data))
    ```

3.	Сохраните файл и запустите `unittest` в каталоге. Имя файла не нужно. Это связано с тем, что функция `unittest` Test Discovery запускает любой найденный локальный файл, имя которого начинается с test. Вы должны получить следующий результат. Обратите внимание, что все тесты теперь проходят успешно и список для ключа «issueSummary» заполнен. Вы можете безопасно удалить функцию `print()`, поскольку она обычно не используется, когда этот тест объединяется с другими тестами для более крупного прогона теста.

    ```
    devasc@labvm:~/labs/devnet-src/unittest$ python3 -m unittest
    [{'issueSummary': 'Network Device 10.10.20.82 Is Unreachable From Controller'}]
    ...
    ----------------------------------------------------------------------
    Ran 3 tests in 0.001s

    OK
    devasc@labvm:~/labs/devnet-src/unittest$
    ```



