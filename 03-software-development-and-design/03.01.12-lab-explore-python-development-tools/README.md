## Цели

* **Часть 1. Запуск виртуальной машины DEVASC**
* **Часть 2: Обзор установки Python**
* **Часть 3: Виртуальные среды PIP и Python**
* **Часть 4: Совместное использование вашей виртуальной среды**

## Предпосылки / Сценарий

У этого курса есть некоторые основные ожидания от студентов, что они имеют некоторый опыт работы с Python и некоторый практический опыт программирования. Практика в лаборатории с Python дает вашим пальцам «мышечную память» для более эффективной работы с Python. Однако изначально для вас важно знать, как поддерживать среду разработки Python.

В этой лабораторной работе вы изучите установку Python, виртуальные среды PIP и Python.

## Необходимые ресурсы

* 1 ПК с операционной системой по вашему выбору
* Virtual Box или VMWare
* Виртуальная машина DEVASC

## Инструкции

### Часть 1: Запустите виртуальную машину DEVASC

Если вы еще не завершили **Лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC.

### Часть 2: Просмотрите установку Python

Эти команды предоставляют вам базовые методы для получения дополнительных сведений о локальной среде Python.

1.	В виртуальной машине DEVASC вы можете проверить версию Python, уже установленную с помощью команды `python3 –V`.

    ```
    devasc@labvm:~$ python3 -V
    Python 3.8.2
    devasc@labvm:~$
    ```

2.	Чтобы увидеть каталог для локальной среды Python, используйте команду `which` python3.

    ```
    devasc@labvm:~$ which python3
    /usr/bin/python3
    devasc@labvm:~$
    ```

### Часть 3: Виртуальные среды PIP и Python

PIP расшифровывается как Pip Installs Packages. Многие люди сначала узнают о PIP и начинают использовать команды `pip3 install` в общесистемной установке Python. Когда вы запускаете команду `pip3 install` в своей системе, вы можете ввести конкурирующие зависимости в установку вашей системы, которые вам могут понадобиться или не потребоваться для всех проектов Python. Поэтому лучше всего включить виртуальную среду Python. Затем установите только те пакеты, которые необходимы для проекта в этой виртуальной среде. Таким образом, вы точно знаете, какие пакеты установлены в данной настройке. Вы можете легко переключить эти зависимости пакетов при переключении на новую виртуальную среду, не ломая и не вызывая проблем из-за конкурирующих версий программного обеспечения.

Чтобы установить виртуальную среду Python, используйте инструмент `venv` в Python 3, а затем активируйте виртуальную среду, как показано в следующих шагах.

**Шаг 1: Создайте виртуальную среду Python 3.**

1.	Внутри виртуальной машины DEVASC измените каталог **labs/devnet-src/python**.

    ```
    devasc@labvm:~$ cd labs/devnet-src/python/
    devasc@labvm:~/labs/devnet-src/python$
    ```

2.	Введите следующую команду, чтобы использовать инструмент **venv** для создания виртуальной среды Python 3 с именем **devfun**. Ключ `-m` указывает Python запустить модуль **venv**. Имя выбирает программист.

    ```
    devasc@labvm:~/labs/devnet-src/python$ python3 -m venv devfun
    devasc@labvm:~/labs/devnet-src/python$
    ```

**Шаг 2: Активируйте и протестируйте виртуальную среду Python 3.**

1.	Активируйте виртуальную среду. Приглашение изменится, чтобы указать имя среды, в которой вы сейчас работаете, в данном примере это **devfun**. Теперь, когда вы используете здесь форму команды `pip3 install`, система будет устанавливать пакеты только для активной виртуальной среды.

    ```
    devasc@labvm:~/labs/devnet-src/python$ source devfun/bin/activate
    (devfun) devasc@labvm:~/labs/devnet-src/python$
    ```

2.	Запустите команду `pip3 freeze`, чтобы убедиться, что в среде **devfun** не установлены дополнительные пакеты Python.

    ```
    (devfun) devasc@labvm:~/labs/devnet-src/python$ pip3 freeze
    (devfun) devasc@labvm:~/labs/devnet-src/python$
    ```

3.	Теперь установите пакет **requests** Python в среде **devfun**.

    ```
    (devfun) devasc@labvm:~/labs/devnet-src/python$ pip3 install requests
    Collecting requests
    Downloading requests-2.23.0-py2.py3-none-any.whl (58 kB)
        |████████████████████████████████| 58 kB 290 kB/s 
    Collecting urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1
    Downloading urllib3-1.25.9-py2.py3-none-any.whl (126 kB)
        |████████████████████████████████| 126 kB 1.7 MB/s 
    Collecting idna<3,>=2.5
    Downloading idna-2.9-py2.py3-none-any.whl (58 kB)
        |████████████████████████████████| 58 kB 18.3 MB/s 
    Collecting certifi>=2017.4.17
    Downloading certifi-2020.4.5.1-py2.py3-none-any.whl (157 kB)
        |████████████████████████████████| 157 kB 19.8 MB/s 
    Collecting chardet<4,>=3.0.2
    Downloading chardet-3.0.4-py2.py3-none-any.whl (133 kB)
        |████████████████████████████████| 133 kB 59.2 MB/s 
    Installing collected packages: urllib3, idna, certifi, chardet, requests
    Successfully installed certifi-2020.4.5.1 chardet-3.0.4 idna-2.9 requests-2.23.0 urllib3-1.25.9
    (devfun) devasc@labvm:~/labs/devnet-src/python$
    ```

4.	Повторно введите команду `pip3 freeze`, чтобы увидеть пакеты, которые теперь установлены в среде **devfun**.

    > **Примечание**: Ваш список пакетов и номера версий могут немного отличаться.

    ```
    (devfun) devasc@labvm:~/labs/devnet-src/python$ pip3 freeze
    certifi==2020.4.5.1
    chardet==3.0.4
    idna==2.10
    requests==2.24.0
    urllib3==1.25.9
    (devfun) devasc@labvm:~/labs/devnet-src/python$
    ```

5.	Чтобы деактивировать виртуальную среду и вернуться в свою систему, введите команду `deactivate`.

    ```
    (devfun) devasc@labvm:~/labs/devnet-src/python$ deactivate
    devasc@labvm:~/labs/devnet-src/python$
    ```

**Шаг 3: Проверьте текущие пакеты, установленные в системной среде.**

1.	Введите общесистемную команду `python3 -m pip freeze`, чтобы узнать, какие пакеты установлены в системной среде.

    > **Примечание**. Поскольку Python 3 вызывается с помощью следующей команды, вы используете только `pip` вместо `pip3`.

    ```
    devasc@labvm:~/labs/devnet-src/python$ python3 -m pip freeze
    ansible==2.9.4
    apache-libcloud==2.8.0
    appdirs==1.4.3
    argcomplete==1.8.1
    astroid==2.3.3
    bcrypt==3.1.7
    blinker==1.4
    certifi==2019.11.28
    <output omitted>
    xmltodict==0.12.0
    zipp==1.0.0
    devasc@labvm:~/labs/devnet-src/python$
    ```

2.	Если вы хотите быстро найти версию установленного пакета, передайте вывод в команду `grep`. Введите следующее, чтобы увидеть версию установленного пакета **requests**.

    ```
    devasc@labvm:~/labs/devnet-src/python$ python3 -m pip freeze | grep requests
    requests==2.22.0
    requests-kerberos==0.12.0
    requests-ntlm==1.1.0
    requests-toolbelt==0.9.1
    requests-unixsocket==0.2.0
    devasc@labvm:~/labs/devnet-src/python$
    ```

### Часть 4: Совместное использование вашей виртуальной среды**

Вывод команды `pip3 freeze` имеет особый формат. Вы можете использовать все перечисленные зависимости, чтобы другие люди, которые хотят работать над тем же проектом, что и вы, могли получить ту же среду, что и ваша.

Разработчик может создать файл требований, например, requirements.txt, с помощью команды `pip3 freeze> requirements.txt`. Затем другой разработчик может из другой активированной виртуальной среды использовать эту команду `pip3 install -r requirements.txt` для установки пакетов, необходимых для проекта.

1.	Повторно активируйте виртуальную среду **devfun**.

    ```
    devasc@labvm:~/labs/devnet-src/python$ source devfun/bin/activate
    (devfun) devasc@labvm:
    ```

2.	Отправьте вывод команды `pip3 freeze` в текстовый файл с именем `requirements.txt`.

    ```
    (devfun) devasc@labvm:~/labs/devnet-src/python$ pip3 freeze > requirements.txt
    ```

3.	Отключите виртуальную среду **devfun**. Вы можете использовать команду `ls`, чтобы увидеть, что файл **requirements.txt** находится в каталоге `/python`.

    ```
    (devfun) devasc@labvm:~/labs/devnet-src/python$ deactivate
    devasc@labvm:~/labs/devnet-src/python$ ls
    devfun       file-access-input.py  if-acl.py       requirements.txt
    devices.txt  file-access.py        if-vlan.py      while-loop.py
    devnew       hello-world.py        person-info.py
    ```

4.	Создайте и активируйте новую виртуальную среду Python под названием **devnew**.

    ```
    devasc@labvm:~/labs/devnet-src/python$ python3 -m venv devnew
    devasc@labvm:~/labs/devnet-src/python$ source devnew/bin/activate
    (devnew) devasc@labvm:~/labs/devnet-src/python$
    ```

5.	Используйте команду `pip3 install -r requirements.txt`, чтобы установить те же пакеты, которые установлены в виртуальной среде **devfun**.

    ```
    (devnew) devasc@labvm:~/labs/devnet-src/python$ pip3 install -r requirements.txt 
    Collecting certifi==2020.4.5.1
    Using cached certifi-2020.4.5.1-py2.py3-none-any.whl (157 kB)
    Collecting chardet==3.0.4
    Using cached chardet-3.0.4-py2.py3-none-any.whl (133 kB)
    Collecting idna==2.9
    Using cached idna-2.9-py2.py3-none-any.whl (58 kB)
    Requirement already satisfied: pkg-resources==0.0.0 in ./devnew/lib/python3.8/site-packages (from -r requirements.txt (line 4)) (0.0.0)
    Collecting requests==2.23.0
    Using cached requests-2.23.0-py2.py3-none-any.whl (58 kB)
    Collecting urllib3==1.25.9
    Using cached urllib3-1.25.9-py2.py3-none-any.whl (126 kB)
    Installing collected packages: certifi, chardet, idna, urllib3, requests
    Successfully installed certifi-2020.4.5.1 chardet-3.0.4 idna-2.9 requests-2.23.0 urllib3-1.25.9
    (devnew) devasc@labvm:~/labs/devnet-src/python$
    ```

6.	При вводе `pip3 freeze` в среде **devnew** вы должны увидеть следующий результат.

    ```
    (devnew) devasc@labvm:~/labs/devnet-src/python$ pip3 freeze
    certifi==2020.4.5.1
    chardet==3.0.4
    idna==2.9
    pkg-resources==0.0.0
    requests==2.23.0
    urllib3==1.25.9
    (devnew) devasc@labvm:~/labs/devnet-src/python$
    ```

7.	Деактивировать виртуальную среду `devnew`.

    ```
    (devnew) devasc@labvm:~/labs/devnet-src/python$ deactivate
    devasc@labvm:~/labs/devnet-src/python$
    ```

