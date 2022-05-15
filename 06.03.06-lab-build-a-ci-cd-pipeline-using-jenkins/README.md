## Цели

-   **Часть 1. Запуск виртуальной машины DEVASC**
-   **Часть 2: Зарегистрируйте образец приложения в Git**
-   **Часть 3. Измените пример приложения и отправьте изменения в Git**
-   **Часть 4: Загрузите и запустите образ Jenkins Docker**
-   **Часть 5: Настройка Jenkins**
-   **Часть 6: Использование Jenkins для запуска сборки вашего приложения**
-   **Часть 7. Использование Jenkins для тестирования сборки**
-   **Часть 8: Создание конвейера в Jenkins**

## Предпосылки / Сценарий

В этой лабораторной работе вы зафиксируете код примера приложения в репозитории GitHub, измените код локально, а затем зафиксируете свои изменения. Затем вы установите контейнер Docker, который включает последнюю версию Jenkins. Вы настроите Jenkins, а затем используете Jenkins для загрузки и запуска программы Sample App. Затем вы создадите тестовое задание внутри Jenkins, которое будет проверять успешное выполнение вашей программы Sample App каждый раз, когда вы ее создаете. Наконец, вы интегрируете образец приложения и задание тестирования в конвейер непрерывной интеграции / непрерывной разработки, который будет проверять готовность вашего образца приложения к развертыванию при каждом изменении кода.

## Необходимые ресурсы

-   1 ПК с операционной системой по вашему выбору
-   Virtual Box или VMWare
-   Виртуальная машина DEVASC

## Инструкции

### Часть 1. Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC сейчас.

### Часть 2. Зафиксируйте образец приложения в Git

В этой части вы создадите репозиторий GitHub для фиксации файлов примеров приложений, которые вы создали в предыдущей лабораторной работе. Вы создали учетную запись GitHub в предыдущей лабораторной работе. Если вы еще не сделали этого, посетите github.com и создайте учетную запись.

**Шаг 1. Войдите в GitHub и создайте новый репозиторий.**

1.  Войдите на https://github.com/ с вашими учетными данными.
2.  Нажмите кнопку "**New repository**" или щелкните значок «+» в правом верхнем углу и выберите "**New repository**".
3.  Создайте репозиторий, используя следующую информацию:
4.  Repository name: **sample-app**
5.  Description: Explore CI/CD with GitHub and Jenkins
6.  Public/Private: **Private**
7.  Выберите: Create repository

**Шаг 2. Настройте свои учетные данные Git локально на виртуальной машине.**

Откройте окно терминала с VS Code в DEVASC VM. Используйте свое имя вместо "Sample User" вместо имени в кавычках "". Используйте @example.com в качестве адреса электронной почты.

```
devasc@labvm:~$ git config --global user.name "Sample User"
devasc@labvm:~$ git config --global user.email sample@example.com
```

**Шаг 3. Инициализируйте каталог как репозиторий Git.**

Вы будете использовать файлы примеров приложения, созданные в предыдущей лабораторной работе. Однако для вашего удобства эти файлы также хранятся в каталоге **/labs/devnet-src/jenkins/sample-app**. Перейдите в каталог **jenkins/sample-app** и инициализируйте его как репозиторий Git.

```
devasc@labvm:~$ cd labs/devnet-src/jenkins/sample-app/
devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ git init
Initialized empty Git repository in /home/devasc/labs/devnet-src/jenkins/sample-app/.git/
devasc@labvm:~/labs/devnet-src/jenkins/sample-app$
```

**Шаг 4. Укажите репозиторий Git на репозиторий GitHub.**

Используйте команду **git remote add**, чтобы добавить URL-адрес Git с удаленным псевдонимом origin и указать на вновь созданный репозиторий на GitHub. Используя URL-адрес репозитория Git, который вы создали на шаге 1, вам нужно только заменить **github-username** в следующей команде на свое имя пользователя GitHub.

**Примечание**: Ваше имя пользователя GitHub чувствительно к регистру.

```
devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ git remote add origin https://github.com/github-username/sample-app.git
devasc@labvm:~/labs/devnet-src/jenkins/sample-app$
```

**Шаг 5. Подготовьте, зафиксируйте и отправьте файлы примера приложения в репозиторий GitHub.**

1.  Используйте команду **git add**, чтобы поместить файлы в каталог **jenkins/sample-app**. Используйте аргумент звездочки (\*) для размещения всех файлов в текущем каталоге.

    ```
    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ git add *
    ```

1.  Используйте команду **git status**, чтобы увидеть файлы и каталоги, которые размещены и готовы к фиксации в вашем репозитории GitHub.

    ```
    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ git status
    On branch master

    No commits yet

    Changes to be committed:
    (use "git rm --cached <file>..." to unstage)
            new file:   sample-app.sh
            new file:   sample_app.py
            new file:   static/style.css
            new file:   templates/index.html

    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$
    ```

1.  Используйте команду **git commit**, чтобы зафиксировать поэтапные файлы и начать отслеживание изменений. Добавьте сообщение по вашему выбору или воспользуйтесь предложенным здесь.

    ```
    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ git commit -m "Committing sample-app files."
    [master 4030ab6] Committing sample-app files
    4 files changed, 46 insertions(+)
    create mode 100644 sample-app.sh
    create mode 100644 sample_app.py
    create mode 100644 static/style.css
    create mode 100644 templates/index.html
    ```

1.  Используйте команду **git push**, чтобы отправить файлы локального примера приложения в репозиторий GitHub.

    ```
    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ git push origin master
    Username for 'https://github.com': username
    Password for 'https://AllJohns@github.com': password 
    Enumerating objects: 9, done.
    Counting objects: 100% (9/9), done.
    Delta compression using up to 2 threads
    Compressing objects: 100% (5/5), done.
    Writing objects: 100% (8/8), 1.05 KiB | 1.05 MiB/s, done.
    Total 8 (delta 0), reused 0 (delta 0)
    To https://github.com/AllJohns/sample-app.git
    d0ee14a..4030ab6  master -> master
    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$
    ```

> **Примечание**: Если вместо запроса вашего имени пользователя вы получаете сообщение от VS Code с сообщением, что **расширение Git хочет войти в систему с помощью GitHub**, значит, вы неправильно настроили свои учетные данные GitHub на шаге 2 и/или URL-адрес GitHub на шаге 4. URL-адрес должен иметь правильное имя пользователя с учетом регистра и имя репозитория, созданного на шаге 1. Чтобы отменить предыдущую команду **git add**, используйте **команду git remote rm origin**. Затем вернитесь к шагу 2, убедившись, что ввели правильные учетные данные, а на шаге 4 введите правильный URL.

> **Примечание**: Если после ввода имени пользователя и пароля вы получите фатальную ошибку о том, что репозиторий не найден, скорее всего, вы отправили неверный URL. Вам нужно будет отменить команду **git add** с помощью команды **git remote rm origin**.

### Часть 3. Измените образец приложения и отправьте изменения в Git

В части 4 вы установите образ Jenkins Docker, который будет использовать порт 8080. Напомним, что в ваших файлах примера приложения также указывается порт 8080. Сервер Flask и сервер Jenkins не могут использовать 8080 одновременно.

В этой части вы измените номер порта, используемый файлами примера приложения, снова запустите пример приложения, чтобы убедиться, что он работает с новым портом, а затем отправите свои изменения в репозиторий GitHub.

**Шаг 1. Откройте файлы примера приложения.**

Убедитесь, что вы все еще находитесь в каталоге **\~/labs/devnet-src/jenkins/sample-app**, поскольку это файлы, связанные с вашим репозиторием GitHub. Откройте для редактирования как **sample_app.py**, так и **sample-app.sh**.

**Шаг 2. Отредактируйте файлы примера приложения.**

1.  В sample_app.py измените один экземпляр порта 8080 на 5050, как показано ниже.

    ```
    from flask import Flask
    from flask import request
    from flask import render_template

    sample = Flask(__name__)

    @sample.route("/")
    def main():
        return render_template("index.html")

    if __name__ == "__main__":
        sample.run(host="0.0.0.0", port=5050)
    ```

1.  В sample-app.sh измените три экземпляра порта с 8080 на 5050, как показано ниже.

    ```
    #!/bin/bash

    mkdir tempdir
    mkdir tempdir/templates
    mkdir tempdir/static

    cp sample_app.py tempdir/.
    cp -r templates/* tempdir/templates/.
    cp -r static/* tempdir/static/.

    echo "FROM python" >> tempdir/Dockerfile
    echo "RUN pip install flask" >> tempdir/Dockerfile
    echo "COPY  ./static /home/myapp/static/" >> tempdir/Dockerfile
    echo "COPY  ./templates /home/myapp/templates/" >> tempdir/Dockerfile
    echo "COPY  sample_app.py /home/myapp/" >> tempdir/Dockerfile
    echo "EXPOSE 5050" >> tempdir/Dockerfile
    echo "CMD python3 /home/myapp/sample_app.py" >> tempdir/Dockerfile

    cd tempdir

    docker build -t sampleapp .

    docker run -t -d -p 5050:5050 --name samplerunning sampleapp
    docker ps -a
    ```

**Шаг 3. Создайте и проверьте пример приложения.**

1.  Введите команду **bash**, чтобы создать приложение с использованием нового порта 5050.

    ```
    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ bash ./sample-app.sh 
    Sending build context to Docker daemon  6.144kB
    Step 1/7 : FROM python
    ---> 4f7cd4269fa9
    Step 2/7 : RUN pip install flask
    ---> Using cache
    ---> 57a74c0dff93
    Step 3/7 : COPY  ./static /home/myapp/static/
    ---> Using cache
    ---> e70310436097
    Step 4/7 : COPY  ./templates /home/myapp/templates/
    ---> Using cache
    ---> e41ed6d0f933
    Step 5/7 : COPY  sample_app.py /home/myapp/
    ---> 0a8d152f78fd
    Step 6/7 : EXPOSE 5050
    ---> Running in d68f6bfbcffb
    Removing intermediate container d68f6bfbcffb
    ---> 04fa04a1c3d7
    Step 7/7 : CMD python3 /home/myapp/sample_app.py
    ---> Running in ed48fdbc031b
    Removing intermediate container ed48fdbc031b
    ---> ec9f34fa98fe
    Successfully built ec9f34fa98fe
    Successfully tagged sampleapp:latest
    d957a4094c1781ccd7d86977908f5419a32c05a2a1591943bb44eeb8271c02dc
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                  PORTS                    NAMES
    d957a4094c17        sampleapp           "/bin/sh -c 'python …"   1 second ago        Up Less than a second   0.0.0.0:5050->5050/tcp   samplerunning
    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$
    ```

1.  Откройте вкладку браузера и перейдите по адресу localhost: 5050. Вы должны увидеть сообщение **You are calling me from 172.17.0.1**.
2.  Выключите сервер, когда убедитесь, что он работает на порте 5050. Вернитесь в окно терминала, в котором работает сервер, и нажмите CTRL + C, чтобы остановить сервер.

**Шаг 4. Отправьте свои изменения на GitHub.**

1.  Теперь вы готовы отправить свои изменения в репозиторий GitHub. Введите следующие команды.

    ```
    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ git add *
    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ git status
    On branch master
    Changes to be committed:
    (use "git restore --staged <file>..." to unstage)
            modified:   sample-app.sh
            modified:   sample_app.py
            new file:   tempdir/Dockerfile
            new file:   tempdir/sample_app.py
            new file:   tempdir/static/style.css
            new file:   tempdir/templates/index.html

    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ git commit -m "Changed port from 8080 to 5050."
    [master 98d9b2f] Changed port from 8080 to 5050.
    6 files changed, 33 insertions(+), 3 deletions(-)
    create mode 100644 tempdir/Dockerfile
    create mode 100644 tempdir/sample_app.py
    create mode 100644 tempdir/static/style.css
    create mode 100644 tempdir/templates/index.html
    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ git push origin master
    Username for 'https://github.com': username
    Password for 'https://AllJohns@github.com': password
    Enumerating objects: 9, done.
    Counting objects: 100% (9/9), done.
    Delta compression using up to 2 threads
    Compressing objects: 100% (6/6), done.
    Writing objects: 100% (6/6), 748 bytes | 748.00 KiB/s, done.
    Total 6 (delta 2), reused 0 (delta 0)
    remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
    To https://github.com/AllJohns/sample-app.git
    a6b6b83..98d9b2f  master -> master
    devasc@labvm:~/labs/devnet-src/jenkins/sample-app$
    ```

1.  Вы можете убедиться, что ваш репозиторий GitHub обновлен, посетив **https://github.com/github-user/sample-app**. Вы должны увидеть новое сообщение (порт изменен с 8080 на 5050) и что последняя временная метка фиксации обновлена.

### Часть 4. Загрузите и запустите образ Jenkins Docker

В этой части вы загрузите образ Jenkins Docker. Затем вы запустите экземпляр изображения и убедитесь, что сервер Jenkins запущен.

**Шаг 1. Загрузите образ Jenkins Docker.**

Образ Jenkins Docker хранится здесь: https://hub.docker.com/r/jenkins/jenkins. На момент написания этой лабораторной работы на этом сайте указано, что вы используете команду **docker pull jenkins/jenkins** для загрузки последней версии контейнера Jenkins. Вы должны получить следующий результат:

```
devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ docker pull jenkins/jenkins:lts
lts: Pulling from jenkins/jenkins
3192219afd04: Pulling fs layer 
17c160265e75: Pulling fs layer 
cc4fe40d0e61: Pulling fs layer 
9d647f502a07: Pulling fs layer 
d108b8c498aa: Pulling fs layer 
1bfe918b8aa5: Pull complete 
dafa1a7c0751: Pull complete 
650a236d0150: Pull complete 
cba44e30780e: Pull complete 
52e2f7d12a4d: Pull complete 
d642af5920ea: Pull complete 
e65796f9919e: Pull complete 
9138dabbc5cc: Pull complete 
f6289c08656c: Pull complete 
73d6b450f95c: Pull complete 
a8f96fbec6a5: Pull complete 
9b49ca1b4e3f: Pull complete 
d9c8f6503715: Pull complete 
20fe25b7b8af: Pull complete 
Digest: sha256:717dcbe5920753187a20ba43058ffd3d87647fa903d98cde64dda4f4c82c5c48
Status: Downloaded newer image for jenkins/jenkins:lts
docker.io/jenkins/jenkins:lts
devasc@labvm:~/labs/devnet-src/jenkins/sample-app$
```

**Шаг 2. Запустите контейнер Jenkins Docker.**

Введите следующую команду в **одной строке**. Вам может потребоваться скопировать его в текстовый редактор, если вы просматриваете PDF-версию этой лабораторной работы, чтобы избежать разрывов строк. Эта команда запустит контейнер Jenkins Docker, а затем разрешит выполнение команд Docker на вашем сервере Jenkins.

```
devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ docker run --rm -u root -p 8080:8080 -v jenkins-data:/var/jenkins_home -v $(which docker):/usr/bin/docker -v /var/run/docker.sock:/var/run/docker.sock -v "$HOME":/home --name jenkins_server jenkins/jenkins:lts
```

В этой команде запуска докера используются следующие параметры:

-   **--rm** - Эта опция автоматически удаляет контейнер Docker, когда вы прекращаете его запуск.
-   **-u**- Эта опция указывает пользователя. Вы хотите, чтобы этот контейнер Docker запускался от имени пользователя root, чтобы были разрешены все команды Docker, введенные на сервере Jenkins.
-   **-p** - Этот параметр указывает порт, на котором сервер Jenkins будет работать локально.
-   **-v**- Эти параметры связывают монтируемые тома, необходимые для Jenkins и Docker. Первый   
    **-v** указывает, где будут храниться данные Jenkins. Второй **-v** указывает, где взять Docker, чтобы вы могли запускать Docker внутри контейнера Docker, на котором запущен сервер Jenkins. Третий **-v** указывает переменную PATH для домашнего каталога.

**Шаг 3. Убедитесь, что сервер Jenkins запущен.**

Теперь сервер Jenkins должен работать. Скопируйте пароль администратора, который отображается в выводе, как показано ниже.

Не вводите никаких команд в этом окне сервера. Если вы случайно остановите сервер Jenkins, вам нужно будет повторно ввести команду **docker run** из шага 2 выше. После первоначальной установки пароль администратора отображается, как показано ниже.

```
<output omitted>
*************************************************************
*************************************************************
*************************************************************

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

77dc402e31324c1b917f230af7bfebf2 <--Your password will be different

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

*************************************************************
*************************************************************
*************************************************************
<output omitted>
2020-05-12 16:34:29.608+0000 [id=19]    INFO    hudson.WebAppMain$3#run: Jenkins is fully up and running
```

> **Примечание**: Если вы потеряли пароль, или он не отображается, как показано выше, или вам необходимо перезапустить сервер Jenkins, вы всегда можете получить пароль, обратившись к командной строке контейнера Jenkins Docker. Создайте второе окно терминала в VS Code и введите следующие команды, чтобы не останавливать сервер Jenkins:

```
devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ docker exec -it jenkins_server /bin/bash
root@19d2a847a54e:/# cat /var/jenkins_home/secrets/initialAdminPassword 
77dc402e31324c1b917f230af7bfebf2
root@19d2a847a54e:/# exit
exit
devasc@labvm:~/labs/devnet-src/jenkins/sample-app$
```

> **Примечание**: Ваш идентификатор контейнера (19d2a847a54e выделен выше) и пароль будут другими.

**Шаг 4. Изучите уровни абстракции, которые в настоящее время работают на вашем компьютере.**

На следующей схеме ASCII показаны уровни абстракции в этой реализации Docker-inside-Docker (dind). Такой уровень сложности не является необычным для современных сетей и облачных инфраструктур.

```
+----------------------------------------+
|Операционная система вашего компьютера  |
|  +----------------------------------+  |
|  |DEVASC VM                         |  |
|  |  +----------------------------+  |  |
|  |  |Докер контейнер             |  |  |
|  |  |  +----------------------+  |  |  |
|  |  |  |Сервер Jenkins        |  |  |  |
|  |  |  |  +----------------+  |  |  |  |
|  |  |  |  |Докер контейнер |  |  |  |  |
|  |  |  |  +----------------+  |  |  |  |
|  |  |  +----------------------+  |  |  |
|  |  +----------------------------+  |  |
|  +----------------------------------+  |
+----------------------------------------+
```

### Часть 5. Настроить Jenkins

В этой части вы завершите начальную настройку сервера Jenkins.

**Шаг 1. Откройте вкладку веб-браузера.**

Перейдите по адресу http://localhost:8080/ и войдите в систему, используя скопированный пароль администратора.

**Шаг 2. Установите рекомендуемые плагины Jenkins.**

Нажмите **Install suggested plugins** и дождитесь, пока Jenkins загрузит и установит плагины. В окне терминала вы увидите сообщения журнала по мере продолжения установки. Убедитесь, что вы не закрываете это окно терминала. Вы можете открыть другое окно терминала для доступа к командной строке.

**Шаг 3. Пропустите создание нового администратора.**

После завершения установки вам будет представлено окно **Create First Admin User**. А пока нажмите **Skip and continue as admin** внизу.

**Шаг 4. Пропустите создание конфигурации экземпляра.**

В окне **Instance Configuration** ничего не меняйте. Нажмите **Save and Finish** внизу.

**Шаг 5. Начните использовать Jenkins.**

В следующем окне нажмите **Start using Jenkins**. Теперь вы должны оказаться на главной панели с приветствием **Welcome to Jenkins!** сообщение.

### Часть 6. Используйте Jenkins для запуска сборки вашего приложения

Основным элементом Jenkins является работа (также известная как проект). Вы можете создавать задания, которые выполняют множество задач, в том числе следующие:

-   Получите код из репозитория управления исходным кодом, такого как GitHub.
-   Создайте приложение с помощью скрипта или инструмента сборки.
-   Упакуйте приложение и запустите его на сервере

В этой части вы создадите простое задание Jenkins, которое извлекает последнюю версию вашего примера приложения из GitHub и запускает скрипт сборки. Затем в Jenkins вы можете протестировать свое приложение (часть 7) и добавить его в конвейер разработки (часть 8).

**Шаг 1. Создайте новую работу.**

1.  Щелкните ссылку **Create a job** прямо под надписью **Welcome to Jenkins!**. сообщение. Кроме того, вы можете нажать **New Item** в меню слева.
2.  В поле Enter an item name введите имя BuildAppJob.
3.  Выберите **Freestyle** **project** в качестве типа работы. В описании аббревиатура SCM означает управление конфигурацией программного обеспечения, которое представляет собой классификацию программного обеспечения, которое отвечает за отслеживание и контроль изменений в программном обеспечении.
4.  Прокрутите вниз и нажмите **ОК**.

**Шаг 2. Настройте Jenkins BuildAppJob.**

Теперь вы находитесь в окне конфигурации, где можете ввести сведения о своей работе. Вкладки вверху - это просто ярлыки для разделов ниже. Просматривайте вкладки, чтобы изучить параметры, которые можно настроить. Для этой простой работы вам нужно всего лишь добавить несколько деталей конфигурации.

1.  Щелкните вкладку **General**, добавьте описание своей работы. Например, "**My first Jenkins job**".
2.  Щелкните вкладку **Source Code Management** и выберите переключатель **Git**. В поле URL-адрес репозитория добавьте ссылку на репозиторий GitHub для примера приложения, указав свое имя пользователя с учетом регистра. Обязательно добавьте расширение .git в конце URL-адреса. Например:

    ```
    https://github.com/github-username/sample-app.git
    ```

1.  Для учетных данных **Credentials** нажмите кнопку **Add** и выберите **Jenkins**.
2.  В диалоговом окне **Add Credentials** введите свое имя пользователя и пароль GitHub, а затем нажмите **Add**.
3.  **Примечание**: Вы получите сообщение об ошибке, что соединение не удалось. Это потому, что вы еще не выбрали учетные данные.
4.  В раскрывающемся списке **Credentials**, где в настоящее время указано **None**, выберите учетные данные, которые вы только что настроили.
5.  После того, как вы добавили правильный URL-адрес и учетные данные, Jenkins проверяет доступ к репозиторию. У вас не должно быть сообщений об ошибках. Если да, подтвердите свой URL и учетные данные. Вам нужно будет **добавить** их снова, так как на данный момент нет возможности удалить те, которые вы ранее ввели.
6.  Вверху окна конфигурации **BuildAppJob** щелкните вкладку **Build**.
7.  В раскрывающемся списке **Add build step** выберите **Execute shell**.
8.  В поле **Command** введите команду, которую вы используете для запуска сборки для сценария sample-app.sh.

    ```
    bash ./sample-app.sh
    ```

1.  Щелкните кнопку **Save**. Вы вернетесь на панель управления Jenkins с выбранным **BuildAppJob**.

**Шаг 3. Попросите Jenkins создать приложение.**

Слева нажмите **Build Now**, чтобы начать работу. Jenkins загрузит ваш репозиторий Git и выполнит команду сборки **bash ./sample-app.sh**. Ваша сборка должна быть успешной, потому что вы ничего не меняли в коде со времени части 3, когда вы меняли код.

**Шаг 4. Получите доступ к деталям сборки.**

Слева в разделе **Build History** щелкните номер сборки, который должен быть **\#1**, если вы не создавали приложение несколько раз.

**Шаг 5. Просмотрите вывод консоли.**

Слева щелкните **Console Output**. Вы должны увидеть результат, подобный следующему. Обратите внимание на сообщения об успешном выполнении внизу, а также на вывод команды **docker ps -a**. Работают два контейнера докеров: один для вашего примера приложения, работающего на локальном порте 5050, и один для Jenkins на локальном порту 8080.

```
Started by user admin
Running as SYSTEM
Building in workspace /var/jenkins_home/workspace/BuildAppJob
using credential 0cf684ea-48a1-4e8b-ba24-b2fa1c5aa3df
Cloning the remote Git repository
Cloning repository https://github.com/github-user/sample-app
 > git init /var/jenkins_home/workspace/BuildAppJob # timeout=10
Fetching upstream changes from https://github.com/github-user/sample-app
 > git --version # timeout=10
using GIT_ASKPASS to set credentials 
 > git fetch --tags --progress -- https://github.com/github-user/sample-app +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git config remote.origin.url https://github.com/github-user/sample-app # timeout=10
 > git config --add remote.origin.fetch +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git config remote.origin.url https://github.com/github-user/sample-app # timeout=10
Fetching upstream changes from https://github.com/github-user/sample-app
using GIT_ASKPASS to set credentials 
 > git fetch --tags --progress -- https://github.com/github-user/sample-app +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/master^{commit} # timeout=10
 > git rev-parse refs/remotes/origin/origin/master^{commit} # timeout=10
Checking out Revision 230ca953ce83b5d6bdb8f99f11829e3a963028bf (refs/remotes/origin/master)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 230ca953ce83b5d6bdb8f99f11829e3a963028bf # timeout=10
Commit message: "Changed port numbers from 8080 to 5050"
 > git rev-list --no-walk 230ca953ce83b5d6bdb8f99f11829e3a963028bf # timeout=10
[BuildAppJob] $ /bin/sh -xe /tmp/jenkins1084219378602319752.sh
+ bash ./sample-app.sh
Sending build context to Docker daemon  6.144kB

Step 1/7 : FROM python
 ---> 4f7cd4269fa9
Step 2/7 : RUN pip install flask
 ---> Using cache
 ---> 57a74c0dff93
Step 3/7 : COPY  ./static /home/myapp/static/
 ---> Using cache
 ---> aee4eb712490
Step 4/7 : COPY  ./templates /home/myapp/templates/
 ---> Using cache
 ---> 594cdc822490
Step 5/7 : COPY  sample_app.py /home/myapp/
 ---> Using cache
 ---> a001df90cf0c
Step 6/7 : EXPOSE 5050
 ---> Using cache
 ---> eae896e0a98c
Step 7/7 : CMD python3 /home/myapp/sample_app.py
 ---> Using cache
 ---> 272c61fddb45
Successfully built 272c61fddb45
Successfully tagged sampleapp:latest
9c8594e62079c069baf9a88a75c13c8c55a3aeaddde6fd6ef54010953c2d3fbb
CONTAINER ID        IMAGE                 COMMAND                  CREATED                  STATUS                  PORTS                               NAMES
9c8594e62079        sampleapp             "/bin/sh -c 'python …"   Less than a second ago   Up Less than a second   0.0.0.0:5050->5050/tcp              samplerunning
e25f233f9363        jenkins/jenkins:lts   "/sbin/tini -- /usr/…"   29 minutes ago           Up 29 minutes           0.0.0.0:8080->8080/tcp, 50000/tcp   jenkins_server
Finished: SUCCESS
```

**Шаг 6. Откройте другую вкладку веб-браузера и убедитесь, что образец приложения запущен.**

Введите локальный адрес **localhost: 5050**. Вы должны увидеть содержимое вашего index.html, отображаемое на светло-голубом фоне с надписью **You are calling me from 172.17.0.1**, отображаемой как H1.

### Часть 7. Используйте Jenkins для тестирования сборки

В этой части вы создадите второе задание, которое тестирует сборку, чтобы убедиться, что она работает правильно.

> **Примечание**: Вам необходимо остановить и удалить docker-контейнер **samplerunning**.

```
devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ docker stop samplerunning 
samplerunning
devasc@labvm:~/labs/devnet-src/jenkins/sample-app$ docker rm samplerunning 
samplerunning
```

**Шаг 1. Начните новую работу по тестированию вашего примера приложения.**

1.  Вернитесь на вкладку веб-браузера Jenkins и щелкните ссылку **Jenkins** в верхнем левом углу, чтобы вернуться на главную панель управления.
2.  Щелкните ссылку **New Item**, чтобы создать новое задание.
3.  В поле Введите имя элемента введите имя **TestAppJob**.
4.  Выберите **Freestyle** **project** в качестве типа работы.
5.  Прокрутите вниз и нажмите **ОК**.

**Шаг 2. Настройте Jenkins TestAppJob.**

1.  Добавьте описание своей работы. Например, "My first Jenkins test".
2.  Оставьте для параметра **Source Code Management** значение **None**.
3.  Щелкните вкладку Build Triggers и установите флажок Build after other projects are built. Для Projects to watch введите имя BuildAppJob.

**Шаг 3. Напишите тестовый сценарий, который должен запускаться после стабильной сборки BuildAppJob.**

1.  Щелкните вкладку **Build**.
2.  Нажмите Add build step и выберите Execute shell.
3.  Введите следующий сценарий. Команда **if** должна быть в одной строке, включая **; then**. Эта команда выполнит поиск с помощью команды **grep** для вывода, возвращенного командой cURL, чтобы увидеть вернется ли **You are calling me from 172.17.0.1**. Если true, сценарий завершается с кодом 0, что означает отсутствие ошибок в сборке **BuildAppJob**. Если false, сценарий завершается с кодом 1, что означает сбой **BuildAppJob**.

```
if curl http://172.17.0.1:5050/ | grep "You are calling me from 172.17.0.1"; then
   exit 0
else
   exit 1
fi
```

1.  Нажмите **Save**, а затем ссылку **Back to Dashboard** слева.

**Шаг 4. Попросите Jenkins снова запустить задание BuildAppJob.**

1.  Обновите веб-страницу с помощью кнопки обновления в вашем браузере.
2.  Теперь вы должны увидеть две ваши вакансии, перечисленные в таблице. Для задания **BuildAppJob** нажмите кнопку сборки справа (часы со стрелкой).

**Шаг 5. Убедитесь, что оба задания выполнены.**

Если все пойдет хорошо, вы должны увидеть метку времени для обновления столбца **Last Success** как для **BuildAppJob**, так и для **TestAppJob**. Это означает, что ваш код для обоих заданий работал без ошибок. Но вы также можете убедиться в этом сами.

> **Примечание**: Если метки времени не обновляются, убедитесь, что автоматическое обновление включено, щелкнув ссылку в правом верхнем углу.

1.  Щелкните ссылку **TestAppJob**. В разделе **Permalinks** щелкните ссылку для последней сборки, а затем нажмите **Console Output**. Вы должны увидеть результат, подобный следующему:

    ```
    Started by upstream project "BuildAppJob" build number 13
    originally caused by:
    Started by user admin
    Running as SYSTEM
    Building in workspace /var/jenkins_home/workspace/TestAppJob
    [TestAppJob] $ /bin/sh -xe /tmp/jenkins1658055689664198619.sh
    + grep You are calling me from 172.17.0.1
    + curl http://172.17.0.1:5050/
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed

    0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
    100   177  100   177    0     0  29772      0 --:--:-- --:--:-- --:--:-- 35400
        <h1>You are calling me from 172.17.0.1</h1>
    + exit 0
    Finished: SUCCESS
    ```

1.  Нет необходимости проверять, работает ли ваш образец приложения, потому что **TestAppJob** уже сделал это за вас. Однако вы можете открыть вкладку браузера для **172.17.0.1:5050**, чтобы убедиться, что он действительно запущен.

### Часть 8. Создайте конвейер в Jenkins

Хотя в настоящее время вы можете запустить два задания, просто нажав кнопку **Build Now** для **BuildAppJob**, проекты разработки программного обеспечения обычно намного сложнее. Эти проекты могут значительно выиграть от автоматизации сборок для непрерывной интеграции изменений кода и постоянного создания сборок разработки, готовых к развертыванию. В этом суть CI/CD. Конвейер можно автоматизировать для запуска на основе множества триггеров, в том числе периодически, на основе опроса GitHub на предмет изменений или удаленного запуска сценария. Однако в этой части вы создадите сценарий конвейера в Jenkins для запуска двух ваших приложений всякий раз, когда вы нажимаете кнопку конвейера **Build Now**.

**Шаг 1. Создайте задание конвейера.**

1.  Щелкните ссылку **Jenkins** в левом верхнем углу, а затем - **New Item**.
2.  В поле Enter an item name введите SamplePipeline.
3.  Выберите **Pipeline** в качестве типа задания.
4.  Прокрутите вниз и нажмите **OK**.

**Шаг 2. Настройте задание SamplePipeline.**

1.  Вверху щелкните вкладки и исследуйте каждый раздел страницы конфигурации. Обратите внимание, что есть несколько разных способов запустить сборку. Для задания **SamplePipeline** вы запускаете его вручную.
2.  В разделе **Pipeline** добавьте следующий скрипт.

    ```
    node {
    stage('Preparation') {
        catchError(buildResult: 'SUCCESS') {
            sh 'docker stop samplerunning'
            sh 'docker rm samplerunning'
        }
    }
    stage('Build') {
        build 'BuildAppJob'
    }
    stage('Results') {
        build 'TestAppJob'
    }
    }
    ```

    Этот скрипт делает следующее:

    -   Он создает сборку с одним узлом в отличие от распределенной или многоузловой. Распределенные или многоузловые конфигурации предназначены для конвейеров большего размера, чем тот, который вы строите в этой лабораторной работе, и выходят за рамки этого курса.
    -   На этапе **Preparation** **SamplePipeline** сначала проверяет, что все предыдущие экземпляры контейнера докеров **BuildAppJob** остановлены и удалены. Но если еще нет работающего контейнера, вы получите ошибку. Следовательно, вы используете функцию **catchError**, чтобы отловить любые ошибки и вернуть значение «SUCCESS». Это обеспечит переход конвейера к следующему этапу.
    -   На этапе **Build** **SamplePipeline** построит ваш **BuildAppJob**.
    -   На этапе **Results** **SamplePipeline** построит ваш **TestAppJob**.
1.  Нажмите **Save**, и вы вернетесь на панель управления Jenkins для задания SamplePipeline.

**Шаг 3. Запустите SamplePipeline.**

Слева нажмите **Build Now**, чтобы запустить задание **SamplePipeline**. Если вы написали сценарий конвейера без ошибок, то в **Stage View** должны появиться три зеленых прямоугольника с количеством секунд, которое потребовалось для построения каждого этапа. Если нет, нажмите **Configure** слева, чтобы вернуться к конфигурации **SamplePipeline** и проверить свой сценарий конвейера.

**Шаг 4. Проверьте выходные данные SamplePipeline.**

Щелкните ссылку последней сборки в разделе «Постоянные ссылки», а затем щелкните «Вывод консоли». Вы должны увидеть результат, подобный следующему:

```
Started by user admin
Running in Durability level: MAX_SURVIVABILITY
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins in /var/jenkins_home/workspace/SamplePipeline
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Preparation)
[Pipeline] catchError
[Pipeline] {
[Pipeline] sh
+ docker stop samplerunning
samplerunning
[Pipeline] sh
+ docker rm samplerunning
samplerunning
[Pipeline] }
[Pipeline] // catchError
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build)
[Pipeline] build (Building BuildAppJob)
Scheduling project: BuildAppJob
Starting building: BuildAppJob #15
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Results)
[Pipeline] build (Building TestAppJob)
Scheduling project: TestAppJob
Starting building: TestAppJob #18
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS
```

