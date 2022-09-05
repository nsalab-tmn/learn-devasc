## Цели

-   Часть 1: Запустить виртуальную машину DEVASC
-   Часть 2: Изучите код Python, хранящий пароли в виде простого текста
-   Часть 3: Изучите код Python, хранящий пароли с помощью хеша

## Предпосылки / Сценарий

В этой лабораторной работе вы создадите приложение, которое хранит имя пользователя и пароль в виде открытого текста в базе данных с использованием кода Python. Затем вы протестируете сервер, чтобы убедиться, что не только правильно сохранены учетные данные, но и что пользователь может использовать их для входа в систему. Затем вы выполните те же действия, но с хешированным паролем, чтобы учетные данные не могли быть прочитаны. Важно надежно хранить учетные данные и другие данные, чтобы предотвратить взлом различных серверов и систем.

## Необходимые ресурсы

-   1 ПК с операционной системой по вашему выбору
-   Virtual Box или VMWare
-   Виртуальная машина DEVASC

## Инструкции

### Часть 1. Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC сейчас.

### Часть 2. Демонстрация приложения

Ваш инструктор может продемонстрировать приложение для простого текста и хеширования пароля. Однако в этой лабораторной работе вы создадите этот сценарий шаг за шагом.

Приложение сначала сохраняет имя пользователя и пароль в виде открытого текста в базе данных веб-службы. Затем он проверяет, что учетные данные были сохранены и работают правильно. Второй метод, хеширование пароля, также сохраняет их и проверяет их в базе данных веб-службы.

### Часть 3. Установить пакеты и создать веб-службу

В этой части вы будете использовать Flask для создания простой веб-службы, требующей аутентификации пользователя. Для аутентификации пользователя требуется база данных, которая будет удовлетворена с помощью SQLite.

**Шаг 1. Откройте каталог безопасности в VS Code и установите пакеты Python.**

1.  Открытый код VS. Затем щелкните **File \> Open** **Folder...** и перейдите в каталог **devnet-src/security**. Щелкните **ОК**.
2.  На панели **EXPLORER** щелкните файл-заполнитель **password-evolution.py**, чтобы открыть его.
3.  Откройте терминал в VS Code. Щелкните **Terminal \> New Terminal**.
4.  Используйте следующие команды для установки пакетов, необходимых для этой лабораторной работы. Эти пакеты могут быть уже установлены на вашей виртуальной машине. В этом случае вы получите сообщение **Requirement already satisfied**.

    ```
    devasc@labvm:~/labs/devnet-src/security$ pip3 install pyotp
    Defaulting to user installation because normal site-packages is not writeable
    Collecting pyotp
    Using cached pyotp-2.3.0-py2.py3-none-any.whl (10 kB)
    Installing collected packages: pyotp
    Successfully installed pyotp-2.3.0
    devasc@labvm:~/labs/devnet-src/security$ pip3 install flask
    Defaulting to user installation because normal site-packages is not writeable
    Collecting flask
    Using cached Flask-1.1.2-py2.py3-none-any.whl (94 kB)
    Requirement already satisfied: click>=5.1 in /home/devasc/.local/lib/python3.8/site-packages (from flask) (7.1.2)
    Requirement already satisfied: Jinja2>=2.10.1 in /usr/lib/python3/dist-packages (from flask) (2.10.1)
    Requirement already satisfied: itsdangerous>=0.24 in /home/devasc/.local/lib/python3.8/site-packages (from flask) (1.1.0)
    Requirement already satisfied: Werkzeug>=0.15 in /home/devasc/.local/lib/python3.8/site-packages (from flask) (1.0.1)
    Installing collected packages: flask
    Successfully installed flask-1.1.2
    devasc@labvm:~/labs/devnet-src/security$
    ```

**Шаг 2. Импортируйте библиотеки.**

В файле **password-evolution.py** добавьте следующий код. Обратите внимание на команду **db_name = 'test.db'**. Это база данных SQL (sqlite3), в которой хранятся имена пользователей и пароли, которые вы будете создавать.

```python
import pyotp    #generates one-time passwords
import sqlite3  #database for username/passwords
import hashlib  #secure hashes and message digests
import uuid     #for creating universally unique identifiers
from flask import Flask, request
app = Flask(__name__) #Be sure to use two underscores before and after "name"

db_name = 'test.db'
```

**Шаг 3. Создайте локальную веб-службу.**

1.  Затем добавьте в файл следующий код Flask, чтобы создать первую фразу веб-контента по корневому пути. Когда пользователь переходит по URL-адресу (корневой каталог), вывод оператора return будет отображаться в браузере.

    ```python
    @app.route('/')
    def index():
        return 'Welcome to the hands-on lab for an evolution of password systems!'
    ```

1.  Добавьте в файл следующий код, чтобы создать локальную веб-службу на порту 5000 с самозаверяющим сертификатом TLS. Параметр **ssl_context=’adhoc’** позволяет запускать приложение по HTTPS без использования сертификатов или при использовании самозаверяющего сертификата. Обязательно используйте два символа подчеркивания до и после **name** и **main**.

    ```python
    if __name__ == '__main__':
        app.run(host='0.0.0.0', port=5000, ssl_context='adhoc')
    ```

1.  Сохраните и запустите файл **password-evolution.py**. Команда **nohup** (без зависания) поддерживает выполнение процесса даже после выхода из оболочки или терминала. **&** заставляет команду работать в фоновом режиме.

    ```
    devasc@labvm:~/labs/devnet-src/security$ nohup python3 password-evolution.py &
    [1] 26329
    devasc@labvm:~/labs/devnet-src/security$ nohup: ignoring input and appending output to 'nohup.out'

    devasc@labvm:~/labs/devnet-src/security$
    ```

1.  Нажмите **Enter**, чтобы открыть новую командную строку.
2.  Ваш сервер Flask теперь работает. В VS Code в папке **/security** вы должны увидеть текстовый файл **nohup.out**, созданный Flask. Щелкните файл, чтобы прочитать его вывод.
3.  Убедитесь, что веб-служба запущена. Обязательно используйте HTTPS, а не HTTP. Параметр **-k** позволяет curl выполнять "незащищенные" SSL-соединения и передачи. Без опции **-k** вы получите сообщение об ошибке «Проблема с сертификатом SSL: самозаверяющий сертификат». Команда отобразит сообщение из команды **return**, которую вы закодировали в своем скрипте.

    ```
    devasc@labvm:~/labs/devnet-src/security$ curl -k https://0.0.0.0:5000/
    Welcome to the hands-on lab for an evolution of password systems!devasc@labvm:~/labs/devnet-src/security$
    ```

1.  Нажмите **Enter**, чтобы открыть командную строку на новой строке.
2.  Прежде чем продолжить, прервите сценарий. Используйте следующую команду, чтобы остановить его:

    ```
    devasc@labvm:~/labs/devnet-src/security$ pkill -f password-evolution.py
    devasc@labvm:~/labs/devnet-src/security$
    ```

### Часть 4. Изучите код Python, хранящий пароли в виде простого текста

Когда пароли использовались впервые, они просто хранились в базе данных в виде открытого текста. Когда пользователь ввел свои учетные данные, система искала пароль, чтобы убедиться, что он соответствует. Система была очень простой в реализации, но при этом очень небезопасной. В этой части вы измените файл python **password-evolution.py**, чтобы он мог сохранять идентификационные данные пользователя в базе данных **test.db**. Затем вы создадите пользователя и выполните аутентификацию с использованием этих учетных данных. Наконец, вы изучите базу данных **test.db**, чтобы убедиться, что они хранились в виде открытого текста.

**Шаг 1. Удалите конфигурацию сервера.**

Удалите следующие строки из файла python **password-evolution.py**. Вы добавите этот код позже.

```python
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, ssl_context='adhoc')
```

**Шаг 2. Настройте сервер для хранения учетных данных.**

1.  Добавьте (скопируйте) следующий код Flask, чтобы настроить сервер для хранения имени пользователя и пароля в виде открытого текста. Используя метод HTTP POST, этот код позволяет пользователю создать («зарегистрироваться») новое имя пользователя и пароль, которые будут храниться в файле базы данных test.db SQL. Позже, когда пользователь вводит имя пользователя и пароль, этот код вернет сообщение «успешная регистрация».

    ```python
    ######################################### Plain Text #########################################################
    @app.route('/signup/v1', methods=['POST'])
    def signup_v1():
        conn = sqlite3.connect(db_name)
        c = conn.cursor()
        c.execute('''CREATE TABLE IF NOT EXISTS USER_PLAIN
            (USERNAME  TEXT    PRIMARY KEY NOT NULL,
                PASSWORD  TEXT    NOT NULL);''')
        conn.commit()
        try:
            c.execute("INSERT INTO USER_PLAIN (USERNAME,PASSWORD) "
                    "VALUES ('{0}', '{1}')".format(request.form['username'], request.form['password']))
            conn.commit()
        except sqlite3.IntegrityError:
            return "username has been registered."
        print('username: ', request.form['username'], ' password: ', request.form['password'])
        return "signup success"
    ```

    >   **Примечание**: Будьте осторожны с переносом слов в приведенном выше коде. Убедитесь, что отступы сделаны правильно, иначе код может работать некорректно.

1.  Добавьте (скопируйте) следующий код Flask в файл **password-evolution.py**, чтобы проверить новые учетные данные.

    ```python
    def verify_plain(username, password):
        conn = sqlite3.connect('test.db')
        c = conn.cursor()
        query = "SELECT PASSWORD FROM USER_PLAIN WHERE USERNAME = '{0}'".format(username)
        c.execute(query)
        records = c.fetchone()
        conn.close()
        if not records:
            return False
        return records[0] == password
    ```

1.  Добавьте (скопируйте) следующий код Flask в свой файл **password-evolution.py**. Этот код используется при каждой попытке входа в систему для чтения параметров из HTTP-запроса и проверки учетной записи. В случае успешного входа в систему будет возвращено сообщение "login success", в противном случае пользователь увидит сообщение "Invalid username/password".

    ```python
    @app.route('/login/v1', methods=['GET', 'POST'])
    def login_v1():
        error = None
        if request.method == 'POST':
            if verify_plain(request.form['username'], request.form['password']):
                error = 'login success'
            else:
                error = 'Invalid username/password'
        else:
            error = 'Invalid Method'
        return error
    ```

**Шаг 3. Запустите сервер и проверьте его.**

1.  Добавьте обратно ранее удаленный код конфигурации сервера.

    ```python
    if __name__ == '__main__':
        app.run(host='0.0.0.0', port=5000, ssl_context='adhoc')
    ```

1.  Сохраните и запустите сценарий, чтобы запустить обновленную веб-службу.

    ```
    devasc@labvm:~/labs/devnet-src/security$ nohup python3 password-evolution.py &
    [1] 27826
    devasc@labvm:~/labs/devnet-src/security$ nohup: ignoring input and appending output to 'nohup.out'
    ```

1.  Используйте следующие команды curl для создания (регистрации) двух учетных записей пользователей, **alice** и **bob**, и отправки POST в веб-службу. Каждая команда включает имя пользователя, пароль и вызываемую функцию регистрации, которая хранит эту информацию, включая пароль, в виде открытого текста. Вы должны увидеть сообщение об успешной регистрации из команды **return**, которую вы включили на предыдущем шаге.

    >   **Примечание**: После каждой команды нажимайте Enter, чтобы отобразить командную строку в новой строке.

    ```
    devasc@labvm:~/labs/devnet-src/security$ curl -k -X POST -F 'username=alice' -F 'password=myalicepassword'  'https://0.0.0.0:5000/signup/v1'
    signup successdevasc@labvm:~/labs/devnet-src/security$

    devasc@labvm:~/labs/devnet-src/security$ curl -k -X POST -F 'username=bob' -F 'password=passwordforbob'  'https://0.0.0.0:5000/signup/v1'
    signup successdevasc@labvm:~/labs/devnet-src/security$
    devasc@labvm:~/labs/devnet-src/security$
    ```

**Шаг 4. Убедитесь, что ваши новые пользователи могут войти в систему.**

1.  Используйте следующие команды curl, чтобы убедиться, что оба пользователя могут войти в систему со своими паролями, которые хранятся в виде открытого текста.

    ```
    devasc@labvm:~/labs/devnet-src/security$ curl -k -X POST -F 'username=alice' -F 'password=myalicepassword' 'https://0.0.0.0:5000/login/v1'
    login successdevasc@labvm:~/labs/devnet-src/security$
    devasc@labvm:~/labs/devnet-src/security$

    devasc@labvm:~/labs/devnet-src/security$ curl -k -X POST -F 'username=bob' -F 'password=passwordforbob' 'https://0.0.0.0:5000/login/v1'
    login successdevasc@labvm:~/labs/devnet-src/security$
    ```

1.  Завершите работу сервера.

    ```
    login successdevasc@labvm:~/labs/devnet-src/security$ pkill -f password-evolution.py
    [1]+  Terminated              nohup python3 password-evolution.py
    devasc@labvm:~/labs/devnet-src/security$
    ```

**Шаг 5. Проверьте содержимое test.db.**

Вы могли заметить, что SQLite создал файл **test.db** в вашей папке **/security**. Вы можете просмотреть этот файл и увидеть имя пользователя и пароли для **alice** и **bob**. Однако на этом этапе вы будете использовать приложение для просмотра файлов базы данных SQLite.

1.  Откройте приложение DB Browser for SQLite
    -   Выберите значок **меню** в нижнем левом углу виртуальной машины.
    -   Выберите: **Applications \> All**.
    -   Выберите: **DB Browser for SQLite**
1.  После запуска **DB Browser for SQLite** откройте файл **test.db**:
    -   Выберите: **File \> Open database…**
    -   Перейдите в каталог **labs/devnet-src/security** и выберите **test.db**.
    -   Щелкните **Open**.
1.  На вкладке **Database Structure** обратите внимание на таблицу USER_PLAIN, которая совпадает с кодом, который вы создали ранее.
2.  Разверните таблицу, чтобы увидеть два поля: USERNAME и PASSWORD.
3.  Выберите вкладку **Browse Data**.
4.  Таблица: **USER_PLAIN** уже выбрана. Здесь вы можете увидеть имена пользователей bob и alice вместе с их паролями в виде открытого текста.
5.  Закройте приложение **DB Browser for SQLite**.

### Часть 5. Хеширование паролей в Python

Вместо того, чтобы хранить пароли в виде обычного текста, вы можете хэшировать его при создании. Когда пароль хешируется, он преобразуется в нечитаемый набор символов. Это не позволяет кому-либо преобразовать его обратно в его правильную текстовую версию. Даже если база данных украдена, ее нельзя использовать, потому что хэш неизвестен. Теперь вы измените файл **password-evolution.py**, чтобы создать веб-API, который может принимать веб-запросы и сохранять пароль нового пользователя в хешированном формате.

**Шаг 1. Удалите конфигурацию сервера.**

1.  Удалите следующие две строки из файла **password-evolution.py**. Эти строки будут добавлены позже.

    ```python
    if __name__ == '__main__':
        app.run(host='0.0.0.0', port=5000, ssl_context='adhoc')
    ```

**Шаг 2. Настройте сервер для хранения учетных данных.**

1.  Добавьте следующий код в конец файла, чтобы сервер мог хешировать пароль с помощью метода хеширования SHA256. Обратите внимание, что этот код похож на код, который вы включили ранее. Этот код позволяет пользователю создать («зарегистрироваться») новое имя пользователя и пароль, которые будут храниться в файле базы данных **test.db** SQL. Разница в том, что пароли будут храниться в виде хеш-значений, а не в виде открытого текста. Эта процедура использует sha256, но не солит хэш. Вы увидите последствия использования хеша без соли при просмотре файла базы данных **test.db**.

    ```python
    ######################################### Password Hashing #########################################################

    @app.route('/signup/v2', methods=['GET', 'POST'])
    def signup_v2():
        conn = sqlite3.connect(db_name)
        c = conn.cursor()
        c.execute('''CREATE TABLE IF NOT EXISTS USER_HASH
            (USERNAME  TEXT    PRIMARY KEY NOT NULL,
                HASH      TEXT    NOT NULL);''')
        conn.commit()
        try:
            hash_value = hashlib.sha256(request.form['password'].encode()).hexdigest()
            c.execute("INSERT INTO USER_HASH (USERNAME, HASH) "
                    "VALUES ('{0}', '{1}')".format(request.form['username'], hash_value))
            conn.commit()
        except sqlite3.IntegrityError:
            return "username has been registered."
        print('username: ', request.form['username'], ' password: ', request.form['password'], ' hash: ', hash_value)
        return "signup success"
    ```

1.  Добавьте (скопируйте) следующий код в файл **password-evolution.py**, чтобы убедиться, что пароль был сохранен только в хешированном формате. Код определяет функцию **verify_hash**, которая сравнивает имя пользователя и пароль в хеш-формате. Если сравнение верное, пароль был сохранен только в его хеш-формате.

    ```python
    def verify_hash(username, password):
        conn = sqlite3.connect(db_name)
        c = conn.cursor()
        query = "SELECT HASH FROM USER_HASH WHERE USERNAME = '{0}'".format(username)
        c.execute(query)
        records = c.fetchone()
        conn.close()
        if not records:
            return False
        return records[0] == hashlib.sha256(password.encode()).hexdigest()
    ```

1.  Добавьте (скопируйте) следующий код в свой файл **password-evolution.py**. Следующий код считывает параметры из запроса HTTP POST и проверяет, что пользователь ввел правильный пароль во время входа в систему.

    ```python
    @app.route('/login/v2', methods=['GET', 'POST'])
    def login_v2():
        error = None
        if request.method == 'POST':
            if verify_hash(request.form['username'], request.form['password']):
                error = 'login success'
            else:
                error = 'Invalid username/password'
        else:
            error = 'Invalid Method'
        return error
    ```

**Шаг 3. Запустите сервер и проверьте его.**

1.  Добавьте обратно ранее удаленный код конфигурации сервера.

    ```python
    if __name__ == '__main__':
        app.run(host='0.0.0.0', port=5000, ssl_context='adhoc')
    ```

1.  Сохраните и запустите сценарий, чтобы запустить обновленную веб-службу.

    ```
    devasc@labvm:~/labs/devnet-src/security$ nohup python3 password-evolution.py &
    [1] 28411
    devasc@labvm:~/labs/devnet-src/security$ nohup: ignoring input and appending output to 'nohup.out'
    ```

1.  Используйте следующие команды **curl** для создания трех новых учетных записей пользователей с хешированным паролем. Обратите внимание, что два пользователя, **rick** и **allan**, используют один и тот же пароль.

    ```
    devasc@labvm:~/labs/devnet-src/security$ curl -k -X POST -F 'username=rick' -F 'password=samepassword' 'https://0.0.0.0:5000/signup/v2'
    signup successdevasc@labvm:~/labs/devnet-src/security$

    devasc@labvm:~/labs/devnet-src/security$ curl -k -X POST -F 'username=allan' -F 'password=samepassword' 'https://0.0.0.0:5000/signup/v2'
    signup successdevasc@labvm:~/labs/devnet-src/security$

    devasc@labvm:~/labs/devnet-src/security$ curl -k -X POST -F 'username=dave' -F 'password=differentpassword' 'https://0.0.0.0:5000/signup/v2'
    signup successdevasc@labvm:~/labs/devnet-src/security$
    ```

1.  Используйте команды curl, чтобы проверить логин всех трех пользователей с их паролями, хранящимися в хэшах. Пользователь **allan** вводится дважды, в первый раз с неправильным паролем. Обратите внимание на "Invalid username/password", который совпадает с кодом для этой функции, который вы добавили на предыдущем шаге.

    ```
    devasc@labvm:~/labs/devnet-src/security$ curl -k -X POST -F 'username=rick' -F 'password=samepassword' 'https://0.0.0.0:5000/login/v2'
    login successdevasc@labvm:~/labs/devnet-src/security$

    devasc@labvm:~/labs/devnet-src/security$ curl -k -X POST -F 'username=allan' -F 'password=wrongpassword' 'https://0.0.0.0:5000/login/v2'
    Invalid username/passworddevasc@labvm:~/labs/devnet-src/security$

    devasc@labvm:~/labs/devnet-src/security$ curl -k -X POST -F 'username=allan' -F 'password=samepassword' 'https://0.0.0.0:5000/login/v2'
    login successdevasc@labvm:~/labs/devnet-src/security$

    devasc@labvm:~/labs/devnet-src/security$ curl -k -X POST -F 'username=dave' -F 'password=differentpassword' 'https://0.0.0.0:5000/login/v2'
    login successdevasc@labvm:~/labs/devnet-src/security$
    ```

    Это подтверждает, что хешированный пароль надежно хранится, а пароли пользователей защищены в случае их взлома.

1.  Завершите работу сервера.

    ```
    devasc@labvm:~/labs/devnet-src/security$ pkill -f password-evolution.py
    [1]+  Terminated              nohup python3 password-evolution.py
    devasc@labvm:~/labs/devnet-src/security$
    ```

**Шаг 4. Проверьте содержимое test.db.**

1.  Откройте приложение **DB Browser for SQLite**.
2.  Откройте файл **test.db**.
3.  Выберите вкладку **Database Structure**.
4.  Вы заметите две структуры, которые совпадают с кодом, который вы включили ранее: USER_PLAIN и USER HASH.
5.  Выберите вкладку **Browse Data**.
6.  Таблица: **USER_HASH** уже должна быть выбрана. Теперь вы увидите имена пользователей **rick**, **allan** и **dave** вместе с их хешированными паролями. (Возможно, вам потребуется изменить ячейки таблицы.) Обратите внимание, что **rick** и **allan** имеют одинаковые хешированные пароли. Это потому, что у них был один и тот же пароль, а хеш-функция не включала соль, чтобы сделать их хеш уникальным. Заливка хэша - это процесс добавления случайных данных в хеш. Чтобы гарантировать уникальность паролей, повысить их сложность и предотвратить атаки на пароли, даже если входные данные совпадают, во входные данные хеш-функции следует добавить соль.

### Часть 6. Просмотрите финальную программу

Ниже приводится полный сценарий, созданный вами в этой лабораторной работе.

```python
import pyotp
import sqlite3
import hashlib
import uuid
from flask import Flask, request
app = Flask(__name__)

db_name = 'test.db'
@app.route('/')
def index():
    return 'Welcome to the hands-on lab for an evolution of password systems!'
######################################### Plain Text #########################################################
@app.route('/signup/v1', methods=['POST'])
def signup_v1():
    conn = sqlite3.connect(db_name)
    c = conn.cursor()
    c.execute('''CREATE TABLE IF NOT EXISTS USER_PLAIN
           (USERNAME  TEXT    PRIMARY KEY NOT NULL,
            PASSWORD  TEXT    NOT NULL);''')
    conn.commit()
    try:
        c.execute("INSERT INTO USER_PLAIN (USERNAME,PASSWORD) "
                  "VALUES ('{0}', '{1}')".format(request.form['username'], request.form['password']))
        conn.commit()
    except sqlite3.IntegrityError:
        return "username has been registered."
    print('username: ', request.form['username'], ' password: ', request.form['password'])
    return "signup success"
def verify_plain(username, password):
    conn = sqlite3.connect('test.db')
    c = conn.cursor()
    query = "SELECT PASSWORD FROM USER_PLAIN WHERE USERNAME = '{0}'".format(username)
    c.execute(query)
    records = c.fetchone()
    conn.close()
    if not records:
        return False
    return records[0] == password
@app.route('/login/v1', methods=['GET', 'POST'])
def login_v1():
    error = None
    if request.method == 'POST':
        if verify_plain(request.form['username'], request.form['password']):
            error = 'login success'
        else:
            error = 'Invalid username/password'
    else:
        error = 'Invalid Method'
    return error
######################################### Password Hashing #########################################################

@app.route('/signup/v2', methods=['GET', 'POST'])
def signup_v2():
    conn = sqlite3.connect(db_name)
    c = conn.cursor()
    c.execute('''CREATE TABLE IF NOT EXISTS USER_HASH
           (USERNAME  TEXT    PRIMARY KEY NOT NULL,
            HASH      TEXT    NOT NULL);''')
    conn.commit()
    try:
        hash_value = hashlib.sha256(request.form['password'].encode()).hexdigest()
        c.execute("INSERT INTO USER_HASH (USERNAME, HASH) "
                  "VALUES ('{0}', '{1}')".format(request.form['username'], hash_value))
        conn.commit()
    except sqlite3.IntegrityError:
        return "username has been registered."
    print('username: ', request.form['username'], ' password: ', request.form['password'], ' hash: ', hash_value)
    return "signup success"
def verify_hash(username, password):
    conn = sqlite3.connect(db_name)
    c = conn.cursor()
    query = "SELECT HASH FROM USER_HASH WHERE USERNAME = '{0}'".format(username)
    c.execute(query)
    records = c.fetchone()
    conn.close()
    if not records:
        return False
    return records[0] == hashlib.sha256(password.encode()).hexdigest()
@app.route('/login/v2', methods=['GET', 'POST'])
def login_v2():
    error = None
    if request.method == 'POST':
        if verify_hash(request.form['username'], request.form['password']):
            error = 'login success'
        else:
            error = 'Invalid username/password'
    else:
        error = 'Invalid Method'
    return error
if __name__ == '__main__':
        app.run(host='0.0.0.0', port=5000, ssl_context='adhoc')
```
