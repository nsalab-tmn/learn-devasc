## Цели

-   **Часть 1. Запуск виртуальной машины DEVASC**
-   **Часть 2: Создание простого сценария Bash**
-   **Часть 3. Создание образца веб-приложения**
-   **Часть 4. Настройка веб-приложения для использования файлов веб-сайта**
-   **Часть 5: Создание сценария Bash для сборки и запуска контейнера Docker**
-   **Часть 6: Сборка, запуск и проверка контейнера Docker**

## Предпосылки / Сценарий

В этой лабораторной работе вы рассмотрите основные методы создания сценариев bash, поскольку создание сценариев bash является необходимым условием для остальной части лабораторной работы. Затем вы создадите и измените сценарий Python для простого веб-приложения. Затем вы создадите сценарий bash для автоматизации процесса создания файла Docker, построения контейнера Docker и запуска контейнера Docker. Наконец, вы будете использовать команды Docker, чтобы исследовать тонкости экземпляра контейнера Docker.

## Необходимые ресурсы

-   1 ПК с операционной системой по вашему выбору
-   Virtual Box или VMWare
-   Виртуальная машина DEVASC

## Инструкции

### Часть 1. Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC.

### Часть 2. Создайте простой скрипт Bash

Знания Bash имеют решающее значение для работы с непрерывной интеграцией, непрерывным развертыванием, контейнерами и со средой разработки. Сценарии Bash помогают программистам автоматизировать множество задач в одном файле сценария. В этой части вы кратко рассмотрите, как создать сценарий bash. Позже в лабораторной работе вы будете использовать сценарий bash для автоматизации создания веб-приложения внутри контейнера Docker.

**Шаг 1. Создайте пустой файл сценария bash.**

Измените рабочий каталог на **\~/labs/devnet-src/sample-app** и добавьте новый файл с именем **user-input.sh**.

```
devasc@labvm:~$ cd labs/devnet-src/sample-app/
devasc@labvm:~/labs/devnet-src/sample-app$ touch user-input.sh
```

**Шаг 2. Откройте файл в текстовом редакторе nano.**

Используйте команду **nano**, чтобы открыть текстовый редактор nano.

```
devasc@labvm:~/labs/devnet-src/sample-app$ nano user-input.sh
```

**Шаг 3. Добавьте "she-bang" в начало сценария.**

Отсюда вы можете вводить команды для своего сценария bash. Используйте клавиши со стрелками для навигации в **nano**. Обратите внимание на команды внизу (здесь не показаны) для управления файлом. Символ "карат" (`^`) указывает на то, что вы используете клавишу CTRL или Command на клавиатуре. Например, чтобы выйти из **nano**, введите CTRL+X.

Добавьте «she-bang», которое сообщает системе, что этот файл содержит команды, которые необходимо запустить в оболочке bash.

```
#!/bin/bash
```

>   **Примечание**: Вы можете использовать графический текстовый редактор или открыть файл с помощью VS Code. Однако вы должны быть знакомы с текстовыми редакторами командной строки, такими как **nano** и **vim**. Поищите в Интернете учебные пособия, чтобы освежить свои навыки или узнать о них больше.

**Шаг 4. Добавьте в скрипт простые команды bash.**

Введите несколько простых команд bash для вашего скрипта. Следующие команды запрашивают у пользователя имя, устанавливают имя переменной с именем **userName** и отображают строку текста с именем пользователя.

```
echo -n "Enter Your Name: "
read userName
echo "Your name is $userName."
```

**Шаг 5. Выйдите из nano и сохраните ваш скрипт.**

Нажмите **CTRL+X**, затем **Y**, затем **ENTER**, чтобы выйти из **nano** и сохранить сценарий.

**Шаг 6. Запустите свой сценарий из командной строки.**

Вы можете запустить его прямо из командной строки, используя следующую команду.

```
devasc@labvm:~/labs/devnet-src/sample-app$ bash user-input.sh 
Enter Your Name: Bob
Your name is Bob.
devasc@labvm:~/labs/devnet-src/sample-app$
```

**Шаг 7. Измените режим скрипта на исполняемый файл для всех пользователей.**

Измените режим скрипта на исполняемый с помощью команды **chmod**. Установите параметры на **a+x**, чтобы сделать сценарий исполняемым (x) для всех пользователей (a). После использования **chmod** разрешения для уведомлений были изменены для пользователей, групп и других лиц, и теперь они включают «x» (исполняемый файл).

```
devasc@labvm:~/labs/devnet-src/sample-app$ ls -l user-input.sh
-rw-rw-r-- 1 devasc devasc 84 Jun  7 16:43 user-input.sh 

devasc@labvm:~/labs/devnet-src/sample-app$ chmod a+x user-input.sh

devasc@labvm:~/labs/devnet-src/sample-app$ ls -l user-input.sh
-rwxrwxr-x 1 devasc devasc 84 Jun  7 16:43 user-input.sh
```

**Шаг 8. Переименуйте файл, чтобы удалить расширение .sh.**

Вы можете переименовать файл, чтобы удалить расширение, чтобы пользователям не приходилось добавлять .sh к команде для выполнения сценария.

```
devasc@labvm:~/labs/devnet-src/sample-app$ mv user-input.sh user-input
```

**Шаг 9. Выполните сценарий из командной строки.**

Теперь сценарий можно запускать из командной строки без команды **source** или расширения. Чтобы запустить сценарий bash без исходной команды, вы должны указать перед сценарием "./".

```
devasc@labvm:~/labs/devnet-src/sample-app$ ./user-input
Enter Your Name: Bob
Your name is Bob.
devasc@labvm:~/labs/devnet-src/sample-app$
```

**Шаг 10. Изучите другие сценарии bash.**

Если у вас мало или совсем нет опыта создания сценариев bash, потратьте некоторое время на поиск в Интернете руководств по bash, примеров bash и игр на bash.

### Часть 3. Создать образец веб-приложения

Прежде чем мы сможем запустить приложение в контейнере Docker, нам сначала нужно иметь приложение. В этой части вы создадите очень простой скрипт Python, который будет отображать IP-адрес клиента, когда клиент посещает веб-страницу.

**Шаг 1. Установите Flask и откройте порт на брандмауэре DEVASC VM.**

Разработчики веб-приложений, использующие Python, обычно используют фреймворк. Фреймворк - это библиотека кода, упрощающая разработчикам создание надежных, масштабируемых и поддерживаемых веб-приложений. Flask - это среда веб-приложений, написанная на Python. Другие фреймворки включают Tornado и Pyramid.

Вы будете использовать эту платформу для создания образца веб-приложения. Flask получает запросы, а затем предоставляет ответ пользователю в веб-приложении. Это полезно для динамических веб-приложений, поскольку позволяет взаимодействовать с пользователем и получать динамическое содержимое. Что делает ваш пример веб-приложения динамичным, так это то, что оно будет отображать IP-адрес клиента.

> **Примечание**: Понимание функций, методов и библиотек Flask выходит за рамки этого курса. Он используется в этой лабораторной работе, чтобы показать, как быстро вы можете запустить и запустить веб-приложение. Если вы хотите узнать больше, поищите в Интернете дополнительную информацию и руководства по фреймворку Flask.

Откройте окно терминала и импортируйте **flask**.

```
devasc@labvm:~/labs/devnet-src/sample-app$ pip3 install flask
```

**Шаг 2. Откройте файл sample_app.py.**

Откройте файл **sample_app.py**, расположенный в каталоге **/sample-app**. Вы можете сделать это внутри VS Code или использовать текстовый редактор командной строки, такой как **nano** или **vim**.

**Шаг 3. Добавьте команды для импорта методов из flask.**

Добавьте следующие команды, чтобы импортировать необходимые методы из библиотеки flask.

```python
from flask import Flask
from flask import request
```

**Шаг 4. Создайте экземпляр класса Flask.**

Создайте экземпляр класса Flask и назовите его **sample**. Обязательно используйте два символа подчеркивания до и после `name`.

```python
sample = Flask(__name__)
```

**Шаг 5. Определите метод отображения IP-адреса клиента.**

Затем настройте Flask так, чтобы при посещении пользователем страницы по умолчанию (корневого каталога) отображалось сообщение с IP-адресом клиента.

```python
@sample.route("/")
def main():
    return "You are calling me from " + request.remote_addr + "\n"
```

Обратите внимание на оператор Flask `@sample.route("/")`. Такие фреймворки, как Flask, используют метод маршрутизации (`.route`) для ссылки на URL-адрес приложения (не путать с сетевой маршрутизацией). Здесь «`/`» (корневой каталог) привязан к функции `main()`. Итак, когда пользователь переходит по URL-адресу http://localhost:8080/ (корневой каталог), вывод оператора `return` будет отображаться в браузере.

**Шаг 6. Настройте приложение для локального запуска.**

Наконец, настройте Flask для запуска приложения локально по адресу http://0.0.0.0:8080, который также является http://localhost:8080. Обязательно используйте два символа подчеркивания до и после `name`, а также до и после `main`.

```python
if __name__ == "__main__":
    sample.run(host="0.0.0.0", port=8080)
```

**Шаг 7. Сохраните и запустите образец веб-приложения.**

Сохраните свой сценарий и запустите его из командной строки. Вы должны увидеть следующий вывод, который указывает на то, что ваш сервер **sample-app** работает. Если вы не видите следующий вывод или получаете сообщение об ошибке, внимательно проверьте свой сценарий sample_app.py.

```
devasc@labvm:~/labs/devnet-src/sample-app$ python3 sample_app.py 
 * Serving Flask app "sample-app" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://0.0.0.0:8080/ (Press CTRL+C to quit)
```

**Шаг 8. Убедитесь, что сервер работает.**

Вы можете проверить, что сервер работает, одним из двух способов.

1.  Откройте веб-браузер Chromium и введите 0.0.0.0:8080 в поле URL. Вы должны получить следующий результат:

    ```
    You are calling me from 127.0.0.1
    ```

    Если вы получили ответ «HTTP 400 Bad Request», внимательно проверьте свой сценарий sample_app.py.

1.  Откройте другое окно терминала и используйте инструмент командной строки URL (cURL), чтобы проверить ответ сервера.

    ```
    devasc@labvm:~/labs/devnet-src/sample-app$ curl http://0.0.0.0:8080
    You are calling me from 127.0.0.1
    devasc@labvm:~/labs/devnet-src/sample-app$
    ```

**Шаг 9. Остановите сервер.**

Вернитесь в окно терминала, в котором работает сервер, и нажмите CTRL+C, чтобы остановить сервер.

### Часть 4. Настройте веб-приложение для использования файлов веб-сайта

В этой части создайте образец веб-приложения, чтобы включить страницу **index.html** и спецификацию **style.css**. **Index.html** обычно является первой страницей, загружаемой в веб-браузер клиента при посещении вашего веб-сайта. **Style.css** - это таблица стилей, используемая для настройки внешнего вида веб-страницы.

**Шаг 1. Изучите каталоги, которые будут использоваться веб-приложением.**

Каталоги **templates** и **static** уже находятся в каталоге **sample-app**. Откройте **index.html** и **style.css**, чтобы просмотреть их содержимое. Если вы знакомы с HTML и CSS, не стесняйтесь настраивать эти каталоги и файлы по своему усмотрению. Однако не забудьте сохранить встроенный код Python **{{request.remote_addr}}** в файле **index.html**, поскольку это динамический аспект примера веб-приложения.

```
devasc@labvm:~/labs/devnet-src/sample-app$ cat templates/index.html 
<html>
<head>
    <title>Sample app</title>
    <link rel="stylesheet" href="/static/style.css" />
</head>
<body>
    <h1>You are calling me from {{request.remote_addr}}</h1>
</body>
</html>
devasc@labvm:~/labs/devnet-src/sample-app$ cat static/style.css 
body {background: lightsteelblue;}
devasc@labvm:~/labs/devnet-src/sample-app$
```

**Шаг 2. Обновите код Python для образца веб-приложения.**

Теперь, когда вы изучили основные файлы веб-сайта, вам необходимо обновить файл **sample_app.py**, чтобы он отображал файл **index.html**, а не просто возвращал данные. Создание HTML-содержимого с использованием кода Python может быть обременительным, особенно при использовании условных операторов или повторяющихся структур. HTML-файл может быть автоматически отображен во Flask с помощью функции **render_template**. Для этого необходимо импортировать метод **render_template** из библиотеки flask и отредактировать функцию **return**. Внесите выделенные изменения в свой сценарий.

```python
from flask import Flask
from flask import request
from flask import render_template

sample = Flask(__name__)

@sample.route("/")
def main():
    return render_template("index.html")

if __name__ == "__main__":
    sample.run(host="0.0.0.0", port=8080)
```

**Шаг 3. Сохраните и запустите ваш скрипт.**

Сохраните и запустите ваш скрипт **sampe-app.py**. Вы должны получить следующий результат:

```
devasc@labvm:~/labs/devnet-src/sample-app$ python3 sample_app.py 
 * Serving Flask app "sample-app" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://0.0.0.0:8080/ (Press CTRL+C to quit)
```

> **Примечание**: Если вы получили вывод Traceback и сообщение об ошибке с чем-то вроде **OSError: [Errno 98]** Адрес уже используется, значит, вы не завершили работу своего предыдущего сервера. Вернитесь в окно терминала, в котором работает этот сервер, и нажмите CTRL + C, чтобы завершить серверный процесс. Повторно запустите ваш скрипт.

**Шаг 4. Убедитесь, что ваша программа запущена.**

Опять же, вы можете проверить, работает ли ваша программа, одним из двух способов.

1.  Откройте веб-браузер Chromium и введите 0.0.0.0:8080 в поле URL. Вы должны получить тот же результат, что и раньше. Однако ваш фон будет светло-голубым, а текст будет отформатирован как H1.

    **You are calling me from 127.0.0.1**

2.  Откройте другое окно терминала и используйте команду **curl**, чтобы проверить ответ сервера. Здесь вы увидите результат автоматического рендеринга HTML-кода с помощью функции render_template. В этом случае вы получите весь HTML-контент. Однако динамический код Python будет заменен значением для **{{request.remote_addr}}**. Также обратите внимание, что ваше приглашение будет в той же строке, что и последняя строка вывода HMTL. Нажмите ENTER, чтобы перейти на новую строку.

    ```
    devasc@labvm:~/labs/devnet-src/sample-app$ curl http://0.0.0.0:8080
    <html>
    <head>
        <title>Sample app</title>
        <link rel="stylesheet" href="/static/style.css" />
    </head>
    <body>
        <h1>You are calling me from 127.0.0.1</h1>
    </body>
    </html>devasc@labvm:~/labs/devnet-src/sample-app$ 
    devasc@labvm:~/labs/devnet-src/sample-app$
    ```

**Шаг 5. Остановите сервер.**

Вернитесь в окно терминала, в котором работает сервер, и нажмите CTRL+C, чтобы остановить сервер.

### Часть 5. Создайте сценарий Bash для сборки и запуска контейнера Docker

Приложение может быть развернуто на голом железе (физический сервер, выделенный для однотенантной среды) или на виртуальной машине, как вы только что сделали в предыдущей части. Его также можно развернуть в контейнерном решении, таком как Docker. В этой части вы создадите сценарий bash и добавите к нему команды, которые выполняют следующие задачи по созданию и запуску контейнера Docker:

-   Создайте временные каталоги для хранения файлов веб-сайта.
-   Скопируйте каталоги веб-сайта и sample_app.py во временный каталог.
-   Создайте Dockerfile.
-   Создайте контейнер Docker.
-   Запустите контейнер и убедитесь, что он работает.

**Шаг 1. Создайте временные каталоги для хранения файлов веб-сайта.**

Откройте файл сценария bash **sample-app.sh** в каталоге **\~/labs/devnet-src/sample-app**. Добавьте «she-bang» и команды для создания структуры каталогов с **tempdir** в качестве родительской папки.

```
#!/bin/bash

mkdir tempdir
mkdir tempdir/templates
mkdir tempdir/static
```

**Шаг 2. Скопируйте каталоги веб-сайта и sample_app.py во временный каталог.**

В файле **sample-app.sh** добавьте команды для копирования каталога веб-сайта и сценария в **tempdir**.

```
cp sample_app.py tempdir/.
cp -r templates/* tempdir/templates/.
cp -r static/* tempdir/static/.
```

**Шаг 3. Создайте Dockerfile.**

На этом шаге вы вводите необходимые команды bash **echo** в файл **sample-app.sh**, чтобы создать файл Dockerfile в каталоге **tempdir**. Этот Dockerfile будет использоваться для сборки контейнера.

1.  Вам нужен Python, работающий в контейнере, поэтому добавьте команду Docker **FROM**, чтобы установить Python в контейнер.

    ```
    echo "FROM python" >> tempdir/Dockerfile
    ```

1.  Вашему сценарию **sample_app.py** требуется Flask, поэтому добавьте команду Docker **RUN**, чтобы установить Flask в контейнер.

    ```
    echo "RUN pip install flask" >> tempdir/Dockerfile
    ```

1.  Вашему контейнеру потребуются папки веб-сайта и сценарий **sample_app.py** для запуска приложения, поэтому добавьте команды Docker **COPY**, чтобы добавить их в каталог в контейнере Docker. В этом примере вы создадите **/home/myapp** в качестве родительского каталога внутри контейнера Docker. Помимо копирования файла sample_app.py в Dockerfile, вы также скопируете файл index.html из каталога шаблонов и файл style.css из каталога static.

    ```
    echo "COPY  ./static /home/myapp/static/" >> tempdir/Dockerfile
    echo "COPY  ./templates /home/myapp/templates/" >> tempdir/Dockerfile
    echo "COPY  sample_app.py /home/myapp/" >> tempdir/Dockerfile
    ```

1.  Используйте команду Docker **EXPOSE**, чтобы открыть порт 8080 для использования веб-сервером.

    ```
    echo "EXPOSE 8080" >> tempdir/Dockerfile
    ```

1.  Наконец, добавьте команду Docker **CMD** для выполнения скрипта Python.

    ```
    echo "CMD python3 /home/myapp/sample_app.py" >> tempdir/Dockerfile
    ```

**Шаг 4. Создайте контейнер Docker.**

Добавьте команды в файл **sample_app.sh**, чтобы переключиться в каталог **tempdir** и построить контейнер Docker. Параметр **-t** команды **docker build** позволяет указать имя контейнера, а конечная точка (**.**) Указывает, что вы хотите, чтобы контейнер был построен в текущем каталоге.

```
cd tempdir
docker build -t sampleapp .
```

**Шаг 5. Запустите контейнер и убедитесь, что он работает.**

1.  Добавьте команду **docker run** в файл **sample-app.sh**, чтобы запустить контейнер.

    ```
    docker run -t -d -p 8080:8080 --name samplerunning sampleapp
    ```

    Параметры запуска докера указывают следующее:

    -   **-t** указывает, что вы хотите создать терминал для контейнера, чтобы вы могли получить к нему доступ из командной строки.
    -   **-d** указывает, что вы хотите, чтобы контейнер работал в фоновом режиме и распечатал идентификатор контейнера при выполнении команды **docker ps -a**.
    -   **-p** указывает, что вы хотите опубликовать внутренний порт контейнера на хосте. Первый «8080» ссылается на порт для приложения, работающего в контейнере докера (наш пример приложения). второй «8080» указывает докеру использовать этот порт на хосте. Эти значения не обязательно должны совпадать. Например, внутренний порт 80 на внешний 800 (**80:800**).
    -   **--name** сначала указывает, что вы хотите вызвать экземпляр контейнера (**samplerunning**), а затем образ контейнера, на котором будет основан экземпляр (**sampleapp**). Имя экземпляра может быть любым. Однако имя образа должно соответствовать имени контейнера, которое вы указали в команде сборки docker (**sampleapp**).
2.  Добавьте команду **docker ps -a**, чтобы отобразить все запущенные в данный момент контейнеры Docker. Эта команда будет последней, выполняемой сценарием bash.

    ```
    docker ps -a
    ```

**Шаг 6. Сохраните ваш сценарий bash.**

### Часть 6. Сборка, запуск и проверка контейнера Docker

В этой части вы выполните сценарий bash, который создаст каталоги, скопирует файлы, создаст Dockerfile, построит контейнер Docker, запустит экземпляр контейнера Docker и отобразит вывод команды **docker ps -a** с подробностями о текущий контейнер. Затем вы исследуете контейнер Docker, остановите его запуск и удалите контейнер.

**Примечание**: Убедитесь, что вы остановили любые другие процессы веб-сервера, которые у вас еще могут быть запущены из предыдущих частей этой лабораторной работы.

**Шаг 1. Выполните сценарий bash.**

Выполните сценарий bash из командной строки. Вы должны увидеть результат, подобный следующему. После создания каталогов **tempdir** скрипт выполняет команды для создания контейнера Docker. Обратите внимание, что на шаге 7/7 в выходных данных выполняется **sample_app.py**, который создает веб-сервер. Также обратите внимание на идентификатор контейнера. Вы увидите это в командной строке Docker позже в лабораторной работе.

```
devasc@labvm:~/labs/devnet-src/sample-app$ bash ./sample-app.sh 
Sending build context to Docker daemon  6.144kB
Step 1/7 : FROM python
latest: Pulling from library/python
90fe46dd8199: Pulling fs layer 
35a4f1977689: Pulling fs layer 
bbc37f14aded: Pull complete 
74e27dc593d4: Pull complete 
4352dcff7819: Pull complete 
deb569b08de6: Pull complete 
98fd06fa8c53: Pull complete 
7b9cc4fdefe6: Pull complete 
512732f32795: Pull complete 
Digest: sha256:ad7fb5bb4770e08bf10a895ef64a300b288696a1557a6d02c8b6fba98984b86a
Status: Downloaded newer image for python:latest
 ---> 4f7cd4269fa9
Step 2/7 : RUN pip install flask
 ---> Running in 32d28026afea
Collecting flask
  Downloading Flask-1.1.2-py2.py3-none-any.whl (94 kB)
Collecting click>=5.1
  Downloading click-7.1.2-py2.py3-none-any.whl (82 kB)
Collecting Jinja2>=2.10.1
  Downloading Jinja2-2.11.2-py2.py3-none-any.whl (125 kB)
Collecting Werkzeug>=0.15
  Downloading Werkzeug-1.0.1-py2.py3-none-any.whl (298 kB)
Collecting itsdangerous>=0.24
  Downloading itsdangerous-1.1.0-py2.py3-none-any.whl (16 kB)
Collecting MarkupSafe>=0.23
  Downloading MarkupSafe-1.1.1-cp38-cp38-manylinux1_x86_64.whl (32 kB)
Installing collected packages: click, MarkupSafe, Jinja2, Werkzeug, itsdangerous, flask
Successfully installed Jinja2-2.11.2 MarkupSafe-1.1.1 Werkzeug-1.0.1 click-7.1.2 flask-1.1.2 itsdangerous-1.1.0
Removing intermediate container 32d28026afea
 ---> 619aee23fd2a
Step 3/7 : COPY  ./static /home/myapp/static/
 ---> 15fac1237eec
Step 4/7 : COPY  ./templates /home/myapp/templates/
 ---> dc807b5cf615
Step 5/7 : COPY  sample_app.py /home/myapp/
 ---> d4035a63ae14
Step 6/7 : EXPOSE 8080
 ---> Running in 40c2d35aa29a
Removing intermediate container 40c2d35aa29a
 ---> eb789099a678
Step 7/7 : CMD python3 /home/myapp/sample_app.py
 ---> Running in 41982e2c6209
Removing intermediate container 41982e2c6209
 ---> a2588e9b0593
Successfully built a2588e9b0593
Successfully tagged sampleapp:latest
8953a95374ff8ebc203059897774465312acc8f0ed6abd98c4c2b04448a56ba5
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                  PORTS                    NAMES
8953a95374ff        sampleapp           "/bin/sh -c 'python …"   1 second ago        Up Less than a second   0.0.0.0:8080->8080/tcp   samplerunning
devasc@labvm:~/labs/devnet-src/sample-app$
```

**Шаг 2. Изучите работающий контейнер Docker и веб-приложение.**

1.  Создание каталогов **tempdir** не отображается в выходных данных сценария. Вы можете добавить команды **echo** для печати сообщений после их успешного создания. Вы также можете проверить их наличие с помощью команды **ls**. Помните, что в этом каталоге находятся файлы и папки, используемые для создания контейнера и запуска веб-приложения. Это не контейнер, который был построен.

    ```
    devasc@labvm:~/labs/devnet-src/sample-app$ ls tempdir/
    Dockerfile  sample_app.py  static  templates
    devasc@labvm:~/labs/devnet-src/sample-app$
    ```

2.  Обратите внимание на Dockerfile, созданный вашим сценарием bash. Откройте этот файл, чтобы увидеть, как он будет выглядеть в окончательном виде без команд **echo**.

    ```
    devasc@labvm:~/labs/devnet-src/sample-app$ cat tempdir/Dockerfile 
    FROM python
    RUN pip install flask
    COPY  ./static /home/myapp/static/
    COPY  ./templates /home/myapp/templates/
    COPY  sample_app.py /home/myapp/
    EXPOSE 8080
    CMD python3 /home/myapp/sample_app.py
    ```

3.  Вывод команды **docker ps -a** может быть трудночитаемым в зависимости от ширины экрана вашего терминала. Вы можете перенаправить его в текстовый файл, где вам будет удобнее просматривать его без переноса слов.

    ```
    devasc@labvm:~/labs/devnet-src/sample-app$ docker ps -a >> running.txt
    devasc@labvm:~/labs/devnet-src/sample-app$
    ```

4.  Контейнер Docker создает свой собственный IP-адрес из адресного пространства частной сети. Убедитесь, что веб-приложение запущено и сообщает IP-адрес. В веб-браузере по адресу **http://localhost: 8080** вы должны увидеть сообщение **You are calling me from 172.17.0.1** в формате H1 на светло-голубом фоне. Вы также можете использовать команду curl, если хотите.

    ```
    devasc@labvm:~/labs/devnet-src/sample-app$ curl http://172.17.0.1:8080
    <html>
    <head>
        <title>Sample app</title>
        <link rel="stylesheet" href="/static/style.css" />
    </head>
    <body>
        <h1>You are calling me from 172.17.0.1</h1>
    </body>
    </html>devasc@labvm:~/labs/devnet-src/sample-app$ 
    devasc@labvm:~/labs/devnet-src/sample-app$
    ```

5.  По умолчанию Docker использует подсеть IPv4 172.17.0.0/16 для сети контейнеров. (Этот адрес можно изменить при необходимости.) Введите команду **ip address** для отображения всех IP-адресов, используемых вашим экземпляром виртуальной машины DEVASC. Вы должны увидеть адрес обратной связи 127.0.0.1, который веб-приложение использовало ранее в лабораторной работе, и новый интерфейс Docker с IP-адресом 172.17.0.1.

    ```
    devasc@labvm:~/labs/devnet-src/sample-app$ ip address
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    <output omitted>
    4: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
        link/ether 02:42:c2:d1:8a:2d brd ff:ff:ff:ff:ff:ff
        inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
        valid_lft forever preferred_lft forever
        inet6 fe80::42:c2ff:fed1:8a2d/64 scope link 
        valid_lft forever preferred_lft forever
    <output omitted>
    ```

**Шаг 3. Получите доступ и исследуйте работающий контейнер.**

Помните, что контейнер Docker - это способ инкапсуляции всего, что вам нужно для запуска вашего приложения, чтобы его можно было легко развернуть в различных средах, а не только в вашей виртуальной машине DEVASC.

1.  Чтобы получить доступ к запущенному контейнеру, введите команду **docker exec -it**, указав имя запущенного контейнера (samplerunning) и указав, что вам нужна оболочка bash (/bin/bash). Параметр -i указывает, что вы хотите, чтобы он был интерактивным, а параметр **-t** указывает, что вам нужен терминальный доступ. Приглашение изменится на **root@containerID**. Ваш идентификатор контейнера будет отличаться от показанного ниже. Обратите внимание, что идентификатор контейнера совпадает с идентификатором, показанным в выводе команды **docker ps -a**.

    ```
    devasc@labvm:~/labs/devnet-src/sample-app$ docker exec -it samplerunning /bin/bash
    root@8953a95374ff:/#
    ```

2.  Теперь у вас есть root-доступ к **samplerunning** контейнера Docker. Отсюда вы можете использовать команды Linux для изучения контейнера Docker. Введите **ls**, чтобы увидеть структуру каталогов на корневом уровне.

    ```
    root@8953a95374ff:/# ls
    bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
    boot  etc  lib   media  opt  root  sbin  sys  usr
    root@8953a95374ff:/#
    ```

3.  Напомним, что в вашем сценарии bash вы добавили команды в Dockerfile, которые копировали каталоги и файлы ваших приложений в каталог **home/myapp**. Введите команду **ls** еще раз для этой папки, чтобы увидеть ваш сценарий **sample_app.py** и каталоги. Чтобы лучше понять, что входит в ваш контейнер Docker, вы можете использовать команду **ls** для проверки других каталогов, таких как /etc и /bin.

    ```
    root@8953a95374ff:/# ls home/myapp/
    sample_app.py  static  templates
    root@8953a95374ff:/#
    ```

4.  Выйдите из контейнера Docker, чтобы вернуться в командную строку DEVASC VM.

    ```
    root@8953a95374ff:/# exit
    exit
    devasc@labvm:~/labs/devnet-src/sample-app$
    ```

**Шаг 4. Остановите и удалите контейнер Docker.**

1.  Вы можете остановить контейнер Docker с помощью команды **docker stop**, указав имя запущенного контейнера. Очистка и кеширование контейнера займет несколько секунд. Вы можете увидеть, что он все еще существует, введя команду **docker ps -a**. Однако, если вы обновите веб-страницу для **http://localhost:8080**, вы увидите, что веб-приложение больше не работает.

    ```
    devasc@labvm:~/labs/devnet-src/sample-app$ docker stop samplerunning 
    samplerunning
    devasc@labvm:~/labs/devnet-src/sample-app$ docker ps -a
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                        PORTS               NAMES
    df034cb53e72        sampleapp           "/bin/sh -c 'python …"   49 minutes ago      Exited (137) 20 seconds ago                       samplerunning
    devasc@labvm:~/labs/devnet-src/sample-app$
    ```

2.  Вы можете перезапустить остановленный контейнер с помощью команды **docker start**. Емкость сразу же раскрутится.

    ```
    devasc@labvm:~/labs/devnet-src/sample-app$ docker start samplerunning 
    samplerunning
    devasc@labvm:~/labs/devnet-src/sample-app$
    ```

3.  Чтобы удалить контейнер навсегда, сначала остановите его, а затем удалите с помощью команды **docker rm**. Вы всегда можете перестроить его снова, выполнив программу **sample-app**. Используйте команду **docker ps -a**, чтобы убедиться, что контейнер был удален.

    ```
    devasc@labvm:~/labs/devnet-src/sample-app$ docker stop samplerunning 
    samplerunning
    devasc@labvm:~/labs/devnet-src/sample-app$ docker rm samplerunning 
    samplerunning
    devasc@labvm:~/labs/devnet-src/sample-app$ docker ps -a
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    devasc@labvm:~/labs/devnet-src/sample-app$
    ```
