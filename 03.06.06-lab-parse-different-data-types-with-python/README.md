## Цели

* **Часть 1. Запуск виртуальной машины DEVASC**
* **Часть 2: Анализ XML в Python**
* **Часть 3: Парсинг JSON в Python**
* **Часть 4: Парсинг YAML в Python**

## Предпосылки / Сценарий

Парсинг (разбор) означает анализ сообщения, разбиение его на составные части и понимание цели каждой части в контексте. Когда сообщения передаются между компьютерами, они перемещаются в виде потока символов. Эти символы фактически представляют собой строку. Это сообщение необходимо проанализировать в семантически эквивалентную структуру данных, содержащую данные распознанных типов (например, целые числа, числа с плавающей запятой, строки и логические значения), прежде чем данные можно будет интерпретировать и использовать.

В этой лабораторной работе вы будете использовать Python для анализа каждого формата данных по очереди: XML, JSON и YAML. Мы рассмотрим примеры кода и поговорим о том, как работает каждый парсер.

## Необходимые ресурсы

* 1 ПК с операционной системой по вашему выбору
* Virtual Box или VMWare
* Виртуальная машина DEVASC

## Инструкции

### Часть 1: Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC.

### Часть 2: Парсинг XML в Python

Из-за гибкости, обеспечиваемой Extensible Markup Language (XML), его может быть сложно проанализировать. Поля полнотекстовых тегированных данных XML не соответствуют однозначно типам данных по умолчанию в Python или других популярных языках. Кроме того, не всегда очевидно, как значения атрибутов должны быть представлены в данных.

Разработчики Cisco, работающие в определенных контекстах, могут обойти эти проблемы, поскольку Cisco предоставила такие инструменты, как YANG-CLI, который проверяет и использует XML, относящийся к моделированию данных и связанным задачам. Ниже приводится содержимое файла **myfile.xml**, находящегося в **~/labs/devnet-src/parsing**. Это пример файла, которым управляет YANG-CLI. Вы проанализируете этот файл в Python, чтобы получить доступ к содержащейся в нем информации.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<rpc message-id="1"
 xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
 <edit-config>
  <target>
   <candidate/>
  </target>
  <default-operation>merge</default-operation>
  <test-option>set</test-option>
  <config>
   <int8.1
    xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0"
    nc:operation="create"
    xmlns="http://netconfcentral.org/ns/test">9</int8.1>
  </config>
 </edit-config>
</rpc>
```

**Шаг 1: Создайте сценарий для анализа XML-данных.**

1.	Откройте файл **parsexml.py**, который находится в каталоге **~/labs/devnet-src/parsing**.
2.	Импортируйте модуль **ElementTree** библиотеки **xml** и обработчик регулярных выражений. Модуль **ElementTree** будет использоваться для анализа. Механизм регулярных выражений будет использоваться для поиска определенных данных.

    > **Примечание**: Если у вас нет опыта использования регулярных выражений в Linux, Python или других объектно-ориентированных языках программирования, поищите в Интернете учебные пособия.

    ```python
    import xml.etree.ElementTree as ET
    import re
    ```

3.	Затем используйте функцию `parse` из ET (ElementTree), чтобы проанализировать файл **myfile.xml** и назначить его переменной `(xml`). Затем получите корневой элемент с помощью функции `getroot` и назначьте его переменной (`root`).

    ```python
    xml = ET.parse("myfile.xml")
    root = xml.getroot()
    ```

4.	Теперь на верхнем уровне дерева можно искать содержащий тег `<edit-config>`, и при обнаружении этого помеченного блока можно искать два именованных значения, которые он содержит: `<default-operation>` и `<test-option>`. Создайте регулярное выражение, чтобы получить содержимое корневого содержимого XML в теге `<rpc>`, а затем добавьте дополнительные регулярные выражения для детализации содержимого, чтобы найти значение `<edit-config>`, `<default-operation>`, и элементы `<test-option>`.

    ```python
    ns = re.match('{.*}', root.tag).group(0)
    editconf = root.find("{}edit-config".format(ns))
    defop = editconf.find("{}default-operation".format(ns))
    testop = editconf.find("{}test-option".format(ns))
    ```

5.	Добавьте операторы печати, чтобы распечатать значение элементов `<default-operation>` и `<test-option>`.

    ```python
    print("The default-operation contains: {}".format(defop.text))
    print("The test-option contains: {}".format(testop.text))
    ```

**Шаг 2: Запускаем скрипт.**

Сохраните и запустите `parsexml.py`. Вы должны получить следующий результат.

```
devasc@labvm:~/labs/devnet-src/parsing$ python3 parsexml.py 
The default-operation contains: merge
The test-option contains: set
devasc@labvm:~/labs/devnet-src/parsing$
```

### Часть 3: Разбираем JSON в Python

Анализ нотации объектов JavaScript (JSON) является частым требованием для взаимодействия с REST API. Шаги обычно следующие:

1.	Выполните аутентификацию, используя комбинацию пользователя и пароля, чтобы получить токен, срок действия которого истечет через заданное время. Этот токен используется для аутентификации последующих запросов.
2.	Выполните запрос GET к REST API с аутентификацией по мере необходимости, чтобы получить состояние ресурса, запросив JSON в качестве формата вывода.
3.	При необходимости измените возвращенный JSON.
4.	Выполните POST (или PUT) в том же REST API (опять же, с аутентификацией по мере необходимости), чтобы изменить состояние ресурса, снова запрашивая JSON в качестве формата вывода и интерпретируя его по мере необходимости, чтобы определить, была ли операция успешной.

Примером JSON для анализа является следующий ответ на запрос токена:

```json
{
 "access_token":"ZDI3MGEyYzQtNmFlNS00NDNhLWFlNzAtZGVjNjE0MGU1OGZmZWNmZDEwN2ItYTU3",
 "expires_in":1209600,
 "refresh_token":"MDEyMzQ1Njc4OTAxMjM0NTY3ODkwMTIzNDU2Nzg5MDEyMzQ1Njc4OTEyMzQ1Njc4",
 "refreshtokenexpires_in":7776000
}
```

В сценариях Python библиотеку Python `json` можно использовать для синтаксического анализа JSON в собственные структуры данных Python и сериализации структур данных обратно как JSON. Библиотеку Python `yaml` можно использовать для преобразования данных в YAML.

Следующая программа использует оба модуля для анализа вышеуказанных данных JSON, извлечения и печати значений данных и вывода YAML-версии файла. Он использует метод `load()` библиотеки `json` для анализа строки, в которую был прочитан файл. Затем он использует обычные ссылки на данные Python для извлечения значений из полученной структуры данных Python. Наконец, он использует функцию `dump()` библиотеки `yaml` для сериализации данных Python обратно в виде YAML на терминал.

**Шаг 1: Создайте скрипт для анализа данных JSON.**

1.	Откройте файл **parsejson.py**, который находится в каталоге **~/labs/devnet-src/parsing**.
2.	Импортируйте библиотеки `json` и `yaml`.

    ```python
    import json
    import yaml
    ```

3.	Используйте оператор Python `with`, чтобы открыть **myfile.json** и установить для него имя переменной `json_file`. Затем используйте метод `json.load` для загрузки файла JSON в строку, для которой задано имя переменной `ourjson`.

    > **Примечание**: Нет необходимости явно закрывать файл, поскольку оператор `with` обеспечивает правильное открытие и закрытие файла.

    ```python
    with open('myfile.json','r') as json_file:
        ourjson = json.load(json_file)
    ```

4.	Добавьте оператор печати для `ourjson`, чтобы увидеть, что теперь это словарь Python.

    ```
    print(ourjson)
    ```

**Шаг 2: Запустите сценарий, чтобы распечатать данные JSON, а затем измените его для печати интересующих данных.**

1.	Сохраните и запустите ваш скрипт. Вы должны увидеть следующий результат.

    ```
    devasc@labvm:~/labs/devnet-src/parsing$ python3 parsejson.py 
    {'access_token': 'ZDI3MGEyYzQtNmFlNS00NDNhLWFlNzAtZGVjNjE0MGU1OGZmZWNmZDEwN2ItYTU3', 'expires_in': 1209600, 'refresh_token': 'MDEyMzQ1Njc4OTAxMjM0NTY3ODkwMTIzNDU2Nzg5MDEyMzQ1Njc4OTEyMzQ1Njc4', 'refreshtokenexpires_in': 7776000}
    devasc@labvm:~/labs/devnet-src/parsing$
    ```

2.	Добавьте операторы `print`, которые отображают значение токена и сколько секунд до истечения срока действия токена.

    ```python
    print("The access token is: {}".format(ourjson['access_token']))
    print("The token expires in {} seconds.".format(ourjson['expires_in']))
    ```

3.	Сохраните и запустите ваш скрипт. Вы должны увидеть следующий результат.

    ```
    devasc@labvm:~/labs/devnet-src/parsing$ python3 parsejson.py 
    {'access_token': 'ZDI3MGEyYzQtNmFlNS00NDNhLWFlNzAtZGVjNjE0MGU1OGZmZWNmZDEwN2ItYTU3', 'expires_in': 1209600, 'refresh_token': 'MDEyMzQ1Njc4OTAxMjM0NTY3ODkwMTIzNDU2Nzg5MDEyMzQ1Njc4OTEyMzQ1Njc4', 'refreshtokenexpires_in': 7776000}
    1209600
    The access token is ZDI3MGEyYzQtNmFlNS00NDNhLWFlNzAtZGVjNjE0MGU1OGZmZWNmZDEwN2ItYTU3
    The token expires in 1209600 seconds
    devasc@labvm:~/labs/devnet-src/parsing$
    ```

**Шаг 3: Выведите проанализированные данные JSON в формате данных YAML.**

1.	Добавьте оператор `print`, который будет отображать три тире, необходимые для файла YAML. Два `\n` добавят две строки после предыдущего вывода. Затем добавьте оператор для печати `ourjson` как данных YAML с помощью метода `dump()` библиотеки `yaml`.

    ```python
    print("\n\n---")
    print(yaml.dump(ourjson))
    ```

2.	Сохраните и запустите ваш скрипт. Вы должны увидеть следующий результат.

    ```python
    devasc@labvm:~/labs/devnet-src/parsing$ python3 parsejson.py 
    <output from previous steps omitted>
    ---
    access_token: ZDI3MGEyYzQtNmFlNS00NDNhLWFlNzAtZGVjNjE0MGU1OGZmZWNmZDEwN2ItYTU3
    expires_in: 1209600
    refresh_token: MDEyMzQ1Njc4OTAxMjM0NTY3ODkwMTIzNDU2Nzg5MDEyMzQ1Njc4OTEyMzQ1Njc4
    refreshtokenexpires_in: 7776000

    devasc@labvm:~/labs/devnet-src/parsing$
    ```

### Часть 4: Разобрать YAML в Python

Следующая программа импортирует библиотеки `json` и `yaml`, использует PyYAML для анализа файла YAML, извлечения и печати значений данных и вывода версии файла в формате JSON. Он использует метод `safe_load()` библиотеки `yaml` для анализа потока файлов и обычных ссылок на данные Python для извлечения значений из полученной структуры данных Python. Затем он использует функцию `dumps()` библиотеки `json` для сериализации данных Python обратно в виде JSON.

Пример YAML для анализа - это тот же файл YAML, который вы вывели в части 3:

```yaml
---
access_token: ZDI3MGEyYzQtNmFlNS00NDNhLWFlNzAtZGVjNjE0MGU1OGZmZWNmZDEwN2ItYTU3
expires_in: 1209600
refresh_token: MDEyMzQ1Njc4OTAxMjM0NTY3ODkwMTIzNDU2Nzg5MDEyMzQ1Njc4OTEyMzQ1Njc4
refreshtokenexpires_in: 7776000
```

**Шаг 1: Создайте сценарий для анализа данных YAML.**

1.	Откройте файл **parseyaml.py**, который находится в каталоге **~/labs/devnet-src/parsing**.
2.	Импортируйте библиотеки `json` и `yaml`.

    ```python
    import json
    import yaml
    ```

3.	Используйте оператор Python with, чтобы открыть **myfile.yaml** и установить для него имя переменной `yaml_file`. Затем используйте метод `yaml.safe_load`, чтобы загрузить файл YAML в строку, для которой задано имя переменной `ouryaml`.

    ```python
    with open('myfile.yaml','r') as yaml_file:
        ouryaml = yaml.safe_load(yaml_file)
    ```

4.	Добавьте инструкцию print для ouryaml, чтобы увидеть, что теперь это словарь Python.

    ```python
    print(ouryaml)
    ```

**Шаг 2: Запустите сценарий, чтобы распечатать данные YAML, а затем измените его для печати интересующих данных.**

1.	Сохраните и запустите ваш скрипт. Вы должны увидеть следующий результат.

    ```
    devasc@labvm:~/labs/devnet-src/parsing$ python3 parseyaml.py 
    {'access_token': 'ZDI3MGEyYzQtNmFlNS00NDNhLWFlNzAtZGVjNjE0MGU1OGZmZWNmZDEwN2ItYTU3', 'expires_in': 1209600, 'refresh_token': 'MDEyMzQ1Njc4OTAxMjM0NTY3ODkwMTIzNDU2Nzg5MDEyMzQ1Njc4OTEyMzQ1Njc4', 'refreshtokenexpires_in': 7776000}
    devasc@labvm:~/labs/devnet-src/parsing$
    ```

2.	Добавьте операторы `print`, которые отображают значение токена и сколько секунд до истечения срока действия токена.

    ```python
    print("The access token is {}".format(ouryaml['access_token']))
    print("The token expires in {} seconds.".format(ouryaml['expires_in']))
    ```

3.	Сохраните и запустите ваш скрипт. Вы должны увидеть следующий результат.

    ```python
    devasc@labvm:~/labs/devnet-src/parsing$ python3 parseyaml.py 
    {'access_token': 'ZDI3MGEyYzQtNmFlNS00NDNhLWFlNzAtZGVjNjE0MGU1OGZmZWNmZDEwN2ItYTU3', 'expires_in': 1209600, 'refresh_token': 'MDEyMzQ1Njc4OTAxMjM0NTY3ODkwMTIzNDU2Nzg5MDEyMzQ1Njc4OTEyMzQ1Njc4', 'refreshtokenexpires_in': 7776000}
    The access token is ZDI3MGEyYzQtNmFlNS00NDNhLWFlNzAtZGVjNjE0MGU1OGZmZWNmZDEwN2ItYTU3
    The token expires in 1209600 seconds.
    devasc@labvm:~/labs/devnet-src/parsing$
    ```

**Шаг 3: Выведите проанализированные данные YAML в формате данных JSON.**

1.	Добавьте оператор `print`, чтобы добавить две пустые строки после предыдущего вывода. Затем добавьте оператор для печати `ouryaml` как данных JSON с помощью метода `dumps()` библиотеки `json`. Добавьте параметр `indent`, чтобы украсить данные JSON.

    ```python
    print("\n\n")
    print(json.dumps(ouryaml, indent=4))
    ```

2.	Сохраните и запустите ваш скрипт. Вы должны увидеть следующий результат. Обратите внимание, что результат выглядит так же, как **myfile.json**.

    ```
    devasc@labvm:~/labs/devnet-src/parsing$ python3 parseyaml.py 
    <output from previous steps omitted>
    {
    "access_token": "ZDI3MGEyYzQtNmFlNS00NDNhLWFlNzAtZGVjNjE0MGU1OGZmZWNmZDEwN2ItYTU3",
    "expires_in": 1209600,
    "refresh_token": "MDEyMzQ1Njc4OTAxMjM0NTY3ODkwMTIzNDU2Nzg5MDEyMzQ1Njc4OTEyMzQ1Njc4",
    "refreshtokenexpires_in": 7776000
    }
    devasc@labvm:~/labs/devnet-src/parsing$
    ```



