## Цели

-   **Часть 1: Запуск виртуальной машины DEVASC**
-   **Часть 2: Создание виртуальной среды Python**
-   **Часть 3: Использование библиотеки тестирования pyATS**
-   **Часть 4: Использование Genie для синтаксического анализа вывода команды IOS**
-   **Часть 5: Использование Genie для сравнения конфигураций**
-   **Часть 6: Очистка лаборатории и дальнейшее расследование**

## Предпосылки / Сценарий

В этой лабораторной работе вы изучите основы pyATS (произносится как «ру», за которыми следует каждая буква отдельно, «A», «T», «S») и Genie. Инструмент pyATS представляет собой экосистему сквозного тестирования, специализирующуюся на управляемом данными и повторно используемом тестировании, и спроектированный так, чтобы быть подходящим для гибких итераций быстрой разработки. Расширяемый по дизайну, pyATS позволяет разработчикам начинать с небольших, простых и линейных тестовых случаев и масштабироваться до больших, сложных и асинхронных наборов тестов.

Genie расширяет и развивает pyATS для использования в сетевой среде. Примеры функций Genie включают:

-   подключение устройств, парсеры и API
-   платформенно-независимые объектные модели Python для таких функций, как OSPF и BGP
-   пул многоразовых тестовых случаев
-   Испытательный двигатель с приводом от YAML

## Необходимые ресурсы

-   1 ПК с операционной системой по вашему выбору
-   Virtual Box или VMWare
-   Виртуальная машина DEVASC
-   Виртуальная машина CSR1kv

## Инструкции

### Часть 1. Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC сейчас.

### Часть 2. Создайте виртуальную среду Python

В этой части вы создадите виртуальную среду Python, называемую виртуальной средой Python или «venv».

**Шаг 1. Откройте терминал в DEVASC-LABVM.**

Дважды щелкните значок эмулятора терминала на рабочем столе.

**Шаг 2. Создание виртуальной среды Python (venv).**

Инструмент pyATS лучше всего устанавливать для индивидуальной работы внутри venv. Среда venv копируется из вашей базовой среды Python, но хранится отдельно от нее. Это позволяет избежать установки программного обеспечения, которое может навсегда изменить общее состояние вашего компьютера. Среда venv подробно описывалась ранее в ходе **лабораторной работы по изучению инструментов разработки Python**.

1.  Создайте каталог **pyats** и перейдите в этот каталог. Вы можете использовать символы **&&** для объединения двух команд в одной строке.

    ```
    devasc@labvm:~$ mkdir labs/devnet-src/pyats && cd labs/devnet-src/pyats
    devasc@labvm:~/labs/devnet-src/pyats$
    ```

1.  Создайте новую виртуальную среду Python, которая создает каталог csr1kv в каталоге pyats.

    ```
    devasc@labvm:~/labs/devnet-src/pyats$ python3 -m venv csr1kv
    ```

1.  **Примечание**: Вы также можете использовать точку "." вместо имени каталога, если вы хотите создать среду venv в текущем каталоге.

**Шаг 3. Просмотрите свою виртуальную среду Python (venv).**

1.  Измените каталоги на ваш новый "целевой" каталог **csr1kv** и перечислите файлы. Venv создает автономное дерево каталогов (тестовый проект), которое содержит установку Python для конкретной версии Python, а также ряд дополнительных пакетов. Он также создает подкаталог **bin**, содержащий копию двоичного файла Python.
2.  Обратите внимание, в частности, на подкаталог **bin** и созданные файлы **pyvenv.cfg**.

    ```
    devasc@labvm:~/labs/devnet-src/pyats$ cd csr1kv
    devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ ls -l
    total 20
    drwxrwxr-x 2 devasc devasc 4096 May 31 16:07 bin
    drwxrwxr-x 2 devasc devasc 4096 May 31 16:07 include
    drwxrwxr-x 3 devasc devasc 4096 May 31 16:07 lib
    lrwxrwxrwx 1 devasc devasc    3 May 31 16:07 lib64 -> lib
    -rw-rw-r-- 1 devasc devasc   69 May 31 16:07 pyvenv.cfg
    drwxrwxr-x 3 devasc devasc 4096 May 31 16:07 share
    devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Изучите содержимое файла **pyvenv.cfg**. Обратите внимание, что этот файл указывает на расположение вашей установки Python в /usr/bin.

    ```
    devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ cat pyvenv.cfg
    home = /usr/bin
    include-system-site-packages = false
    version = 3.8.2
    devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Символьная ссылка (также известная как символическая ссылка) - это особый тип файла, который служит ссылкой на другой файл или каталог. Чтобы лучше понять venv и то, как он использует символические ссылки, перечислите файлы Python в каталоге /usr/bin, на который есть ссылка в файле **pyvenv.cfg**. Используйте параметр ls номер один (-1), чтобы перечислить файлы каждый в одной строке.

    ```
    devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ ls -1 /usr/bin/python*
    /usr/bin/python3
    /usr/bin/python3.8
    /usr/bin/python3.8-config
    /usr/bin/python3-config
    /usr/bin/python-argcomplete-check-easy-install-script3
    /usr/bin/python-argcomplete-tcsh3
    devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Теперь проверьте содержимое подкаталога **bin**, созданного venv. Обратите внимание, что в этом подкаталоге есть два файла, оба из которых являются символическими ссылками. В данном случае это ссылка на двоичные файлы Python в **/usr/bin**. Символьные ссылки используются для связывания библиотек и обеспечения постоянного доступа к этим файлам без необходимости перемещать или создавать копию исходного файла. Также есть файл **activate**, о котором мы поговорим дальше.

    ```
    devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ ls -l bin
    total 44
    -rw-r--r-- 1 devasc devasc 2225 May 31 16:07 activate
    -rw-r--r-- 1 devasc devasc 1277 May 31 16:07 activate.csh
    -rw-r--r-- 1 devasc devasc 2429 May 31 16:07 activate.fish
    -rw-r--r-- 1 devasc devasc 8471 May 31 16:07 Activate.ps1
    -rwxrwxr-x 1 devasc devasc  267 May 31 16:07 easy_install
    -rwxrwxr-x 1 devasc devasc  267 May 31 16:07 easy_install-3.8
    -rwxrwxr-x 1 devasc devasc  258 May 31 16:07 pip
    -rwxrwxr-x 1 devasc devasc  258 May 31 16:07 pip3
    -rwxrwxr-x 1 devasc devasc  258 May 31 16:07 pip3.8
    lrwxrwxrwx 1 devasc devasc    7 May 31 16:07 python -> python3
    lrwxrwxrwx 1 devasc devasc   16 May 31 16:07 python3 -> /usr/bin/python3
    devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Запустите виртуальную среду с помощью **bin/activate**. Обратите внимание, что вашему запросу теперь предшествует (**csr1kv**). Все команды, выполняемые с этого момента, находятся в этом venv.

    ```
    devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ source bin/activate
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  **Примечание**: Команда **deactivate** используется для выхода из среды venv и возврата в обычную среду оболочки.

### Часть 3. Используйте библиотеку тестирования pyATS

В этой части вы будете использовать pyATS, библиотека тестирования python.

**Шаг 1. Установка pyATS.**

Установите pyATS с помощью **pip3**. Это займет несколько минут. Во время установки вы можете увидеть некоторые ошибки. Обычно их можно игнорировать, если можно проверить pyATS, как показано на следующем шаге.

```
(csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ pip3 install pyats[full]
Collecting pyats[full]
Downloading pyats-20.4-cp38-cp38-manylinux1_x86_64.whl (2.0 MB)

<output omitted>
(csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
```

**Шаг 2. Проверка pyATS.**

Убедитесь, что pyATS был успешно установлен с помощью команды **pyats --help**. Обратите внимание, что вы можете получить дополнительную помощь по любой команде pyats с помощью команды **pyats \<command\> --help**.

```
(csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ pyats --help
Usage:
  pyats <command> [options]

Commands:
    create              create scripts and libraries from template
    diff                Command to diff two snapshots saved to file or directory
    dnac                Command to learn DNAC features and save to file (Prototype)
    learn               Command to learn device features and save to file
    logs                command enabling log archive viewing in local browser
    parse               Command to parse show commands
    run                 runs the provided script and output corresponding results.
    secret              utilities for working with secret strings.
    shell               enter Python shell, loading a pyATS testbed file and/or pickled data
    validate            utlities that helps to validate input files
    version             commands related to version display and manipulation

General Options:
  -h, --help            Show help

Run 'pyats <command> --help' for more information on a command.
(csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
```

**Шаг 3. Клонируйте и изучите образцы скриптов pyATS с GitHub.**

1.  Клонируйте репозиторий примеров скриптов Github pyATS **CiscoTestAutomation**.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ git clone https://github.com/CiscoTestAutomation/examples
    Cloning into 'examples'...
    remote: Enumerating objects: 35, done.
    remote: Counting objects: 100% (35/35), done.
    remote: Compressing objects: 100% (31/31), done.
    remote: Total 658 (delta 11), reused 18 (delta 4), pack-reused 623
    Receiving objects: 100% (658/658), 1.00 MiB | 4.82 MiB/s, done.
    Resolving deltas: 100% (338/338), done.
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Убедитесь, что копия была успешной, перечислив файлы в текущем каталоге. Обратите внимание на новый подкаталог **example**.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ ls -l
    total 24
    drwxrwxr-x  2 devasc devasc 4096 May 31 16:07 bin
    drwxrwxr-x 21 devasc devasc 4096 May 31 16:47 examples
    drwxrwxr-x  2 devasc devasc 4096 May 31 16:07 include
    drwxrwxr-x  3 devasc devasc 4096 May 31 16:07 lib
    lrwxrwxrwx  1 devasc devasc    3 May 31 16:07 lib64 -> lib
    -rw-rw-r--  1 devasc devasc   69 May 31 16:07 pyvenv.cfg
    drwxrwxr-x  3 devasc devasc 4096 May 31 16:07 share
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Перечислите файлы в подкаталоге **examples**. Обратите внимание, что есть подкаталог **basic**, а также несколько других файлов.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ ls -l examples
    total 88
    drwxrwxr-x  3 devasc devasc  4096 May 31 16:47 abstraction_example
    drwxrwxr-x  2 devasc devasc  4096 May 31 16:47 basic
    <output omitted>
    drwxrwxr-x  2 devasc devasc  4096 May 31 16:47 uids
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Перечислите файлы в этом подкаталоге **basic**. Это расположение сценариев, которые вы будете использовать на следующем шаге.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ ls -l examples/basic
    total 12
    -rw-rw-r-- 1 devasc devasc  510 May 31 16:47 basic_example_job.py
    -rwxrwxr-x 1 devasc devasc 4475 May 31 16:47 basic_example_script.py
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

**Шаг 4. Изучите основные файлы сценариев.**

Синтаксис объявления теста для pyATS основан на популярных фреймворках модульного тестирования Python, таких как pytest. Он поддерживает основные операторы тестирования, такие как утверждение, что переменная имеет заданное значение, а также явное предоставление результатов через определенные API.

1.  Скрипт Python, который вы будете использовать, - это **basic_example_script.py**. Отобразите содержимое скрипта Python с помощью команды **cat**. Подключите его к **more**, если вы хотите просматривать его по одному экрану или строке за раз. Обратите внимание, что этот сценарий содержит следующие разделы, как показано в выходных данных ниже:
    -   Общий блок настройки
    -   Несколько блоков тестирования
    -   Общий блок очистки
1.  Эти блоки содержат операторы, которые подготавливают и / или определяют готовность тестовой топологии (процесс, который может включать внедрение проблемы), выполняют тесты, а затем возвращают топологию в известное состояние.
2.  Каждый из блоков тестирования, часто называемых в документации pyATS тестовыми наборами, может содержать несколько тестов со своим собственным кодом настройки и очистки. Однако передовая практика предполагает, что общий раздел «Очистка» в конце должен быть разработан с учетом идемпотентности, то есть он должен проверять и восстанавливать все изменения, внесенные установкой и тестированием, а также восстанавливать топологию в исходное желаемое состояние.
    **Примечание**: Хотя не обязательно понимать код, вам будет полезно прочитать комментарии в скрипте Python.

    ```python
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ cat examples/basic/basic_example_script.py | more
    #!/usr/bin/env python
    ###################################################################
    # basic_example.py : A very simple test script example which include:
    #     common_setup
    #     Tescases
    #     common_cleanup
    # The purpose of this sample test script is to show the "hello world"
    # of aetest.
    ###################################################################

    # To get a logger for the script
    import logging

    # Needed for aetest script
    from pyats import aetest

    # Get your logger for your script
    log = logging.getLogger(__name__)

    ###################################################################
    ###                  COMMON SETUP SECTION                       ###
    ###################################################################

    # This is how to create a CommonSetup
    # You can have one of no CommonSetup
    # CommonSetup can be named whatever you want

    class common_setup(aetest.CommonSetup):
        """ Common Setup section """

        # CommonSetup have subsection. 
        # You can have 1 to as many subsection as wanted
        # here is an example of 2 subsections

        # First subsection
        @aetest.subsection
        def sample_subsection_1(self):
            """ Common Setup subsection """
            log.info("Aetest Common Setup ")

        # If you want to get the name of current section, 
        # add section to the argument of the function.

        # Second subsection
        @aetest.subsection
        def sample_subsection_2(self, section):
            """ Common Setup subsection """
            log.info("Inside %s" % (section))

            # And how to access the class itself ?

            # self refers to the instance of that class, and remains consistent
            # throughout the execution of that container.
            log.info("Inside class %s" % (self.uid))

    ###################################################################
    ###                     TESTCASES SECTION                       ###
    ###################################################################

    # This is how to create a testcase
    # You can have 0 to as many testcase as wanted

    # Testcase name : tc_one
    class tc_one(aetest.Testcase):
        """ This is user Testcases section """

        # Testcases are divided into 3 sections
        # Setup, Test and Cleanup.

        # This is how to create a setup section
        @aetest.setup
        def prepare_testcase(self, section):
            """ Testcase Setup section """
            log.info("Preparing the test")
            log.info(section)

        # This is how to create a test section
        # You can have 0 to as many test section as wanted
        
        # First test section
        @ aetest.test
        def simple_test_1(self):
            """ Sample test section. Only print """
            log.info("First test section ")

        # Second test section
        @ aetest.test
        def simple_test_2(self):
            """ Sample test section. Only print """
            log.info("Second test section ")

        # This is how to create a cleanup section
        @aetest.cleanup
        def clean_testcase(self):
            """ Testcase cleanup section """
            log.info("Pass testcase cleanup")

    # Testcase name : tc_two
    class tc_two(aetest.Testcase):
        """ This is user Testcases section """

        @ aetest.test
        def simple_test_1(self):
            """ Sample test section. Only print """
            log.info("First test section ")
            self.failed('This is an intentional failure')

        # Second test section
        @ aetest.test
        def simple_test_2(self):
            """ Sample test section. Only print """
            log.info("Second test section ")

        # This is how to create a cleanup section
        @aetest.cleanup
        def clean_testcase(self):
            """ Testcase cleanup section """
            log.info("Pass testcase cleanup")

    #####################################################################
    ####                       COMMON CLEANUP SECTION                 ###
    #####################################################################

    # This is how to create a CommonCleanup
    # You can have 0 , or 1 CommonCleanup.
    # CommonCleanup can be named whatever you want :)
    class common_cleanup(aetest.CommonCleanup):
        """ Common Cleanup for Sample Test """

        # CommonCleanup follow exactly the same rule as CommonSetup regarding
        # subsection 
        # You can have 1 to as many subsection as wanted
        # here is an example of 1 subsections

        @aetest.subsection
        def clean_everything(self):
            """ Common Cleanup Subsection """
            log.info("Aetest Common Cleanup ")

    if __name__ == '__main__': # pragma: no cover
        aetest.main()
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

Скрипт pyATS - это файл Python, в котором объявлены тесты pyATS. Его можно запускать напрямую как отдельный файл сценария Python, генерируя вывод только в окно вашего терминала. В качестве альтернативы, один или несколько скриптов pyATS могут быть скомпилированы в «задание» и запускаться вместе как пакет с помощью модуля pyATS EasyPy. EasyPy обеспечивает параллельное выполнение нескольких сценариев, собирает журналы в одном месте и обеспечивает центральную точку для внесения изменений в тестируемую топологию.

1.  Используйте **cat** для отображения вашего файла задания pyATS, **pyats_sample_job.py**. Обратите внимание на инструкции по запуску этого файла, выделенные ниже.

    ```python
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ cat examples/basic/basic_example_job.py
    # To run the job:
    # pyats run job basic_example_job.py
    # Description: This example shows the basic functionality of pyats
    #              with few passing tests

    import os
    from pyats.easypy import run

    # All run() must be inside a main function
    def main():
        # Find the location of the script in relation to the job file
        test_path = os.path.dirname(os.path.abspath(__file__))
        testscript = os.path.join(test_path, 'basic_example_script.py')

        # Execute the testscript
        run(testscript=testscript)
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

**Шаг 5. Запустите pyATS вручную, чтобы вызвать базовый тестовый пример.**

1.  Используя задание pyATS и файлы сценария, запустите pyATS вручную, чтобы вызвать базовый тестовый пример. Это позволит убедиться, что задание pyATS и файлы скриптов работают правильно. Информация в выходных данных выходит за рамки этой лабораторной работы, однако вы заметите, что задание и сценарий прошли все необходимые задачи.
2.  **Примечание**: Вывод ниже был усечен. Репозиторий Cisco Test Automation на GitHub может быть изменен, включая файлы заданий pyATS и скриптов. Ваш результат может быть изменен, но не должен повлиять на ваш результат. Например, в файл **basic_example_script.py** был добавлен преднамеренный сбой. Это преднамеренный сбой и не вызывает никаких проблем. Это пример того, что репозитории являются динамическими. Это одна из выделенных строк ниже.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ pyats run job examples/basic/basic_example_job.py
    2020-05-31T17:10:17: %EASYPY-INFO: Starting job run: basic_example_job
    2020-05-31T17:10:17: %EASYPY-INFO: Runinfo directory: /home/devasc/.pyats/runinfo/basic_example_job.2020May31_17:10:16.735106
    2020-05-31T17:10:17: %EASYPY-INFO: --------------------------------------------------------------------------------
    2020-05-31T17:10:18: %EASYPY-INFO: Starting task execution: Task-1
    2020-05-31T17:10:18: %EASYPY-INFO:     test harness = pyats.aetest
    2020-05-31T17:10:18: %EASYPY-INFO:     testscript   = /home/devasc/labs/devnet-src/pyats/csr1kv/examples/basic/basic_example_script.py
    2020-05-31T17:10:18: %AETEST-INFO: +------------------------------------------------------------------------------+
    2020-05-31T17:10:18: %AETEST-INFO: |                            Starting common setup                             |
    <output omitted>
    -------+
    2020-05-31T17:10:18: %SCRIPT-INFO: First test section 
    2020-05-31T17:10:18: %AETEST-ERROR: Failed reason: This is an intentional failure
    2020-05-31T17:10:18: %AETEST-INFO: The result of section simple_test_1 is => FAILED
    2020-05-31T17:10:18: %AETEST-INFO: +------------------------------------------------------------------------------+
    2020-05-31T17:10:18: %AETEST-INFO: |                        Starting section simple_test_2                        |
    <output omitted>
    -------+
    2020-05-31T17:10:20: %EASYPY-INFO: |                                Easypy Report                                 |
    2020-05-31T17:10:20: %EASYPY-INFO: +------------------------------------------------------------------------------+
    <output omitted>
    2020-05-31T17:10:20: %EASYPY-INFO: Overall Stats
    2020-05-31T17:10:20: %EASYPY-INFO:     Passed     : 3
    2020-05-31T17:10:20: %EASYPY-INFO:     Passx      : 0
    2020-05-31T17:10:20: %EASYPY-INFO:     Failed     : 1
    2020-05-31T17:10:20: %EASYPY-INFO:     Aborted    : 0
    2020-05-31T17:10:20: %EASYPY-INFO:     Blocked    : 0
    2020-05-31T17:10:20: %EASYPY-INFO:     Skipped    : 0
    2020-05-31T17:10:20: %EASYPY-INFO:     Errored    : 0
    2020-05-31T17:10:20: %EASYPY-INFO: 
    2020-05-31T17:10:20: %EASYPY-INFO:     TOTAL      : 4
    2020-05-31T17:10:20: %EASYPY-INFO: 
    2020-05-31T17:10:20: %EASYPY-INFO: Success Rate   : 75.00 %
    2020-05-31T17:10:20: %EASYPY-INFO: 
    2020-05-31T17:10:20: %EASYPY-INFO: +------------------------------------------------------------------------------+
    2020-05-31T17:10:20: %EASYPY-INFO: |                             Task Result Summary                              |
    2020-05-31T17:10:20: %EASYPY-INFO: +------------------------------------------------------------------------------+
    2020-05-31T17:10:20: %EASYPY-INFO: Task-1: basic_example_script.common_setup                                 PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: Task-1: basic_example_script.tc_one                                       PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: Task-1: basic_example_script.tc_two                                       FAILED
    2020-05-31T17:10:20: %EASYPY-INFO: Task-1: basic_example_script.common_cleanup                               PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: 
    2020-05-31T17:10:20: %EASYPY-INFO: +------------------------------------------------------------------------------+
    2020-05-31T17:10:20: %EASYPY-INFO: |                             Task Result Details                              |
    2020-05-31T17:10:20: %EASYPY-INFO: +------------------------------------------------------------------------------+
    2020-05-31T17:10:20: %EASYPY-INFO: Task-1: basic_example_script
    2020-05-31T17:10:20: %EASYPY-INFO: |-- common_setup                                                          PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: |   |-- sample_subsection_1                                               PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: |   `-- sample_subsection_2                                               PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: |-- tc_one                                                                PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: |   |-- prepare_testcase                                                  PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: |   |-- simple_test_1                                                     PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: |   |-- simple_test_2                                                     PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: |   `-- clean_testcase                                                    PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: |-- tc_two                                                                FAILED
    2020-05-31T17:10:20: %EASYPY-INFO: |   |-- simple_test_1                                                     FAILED
    2020-05-31T17:10:20: %EASYPY-INFO: |   |-- simple_test_2                                                     PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: |   `-- clean_testcase                                                    PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: `-- common_cleanup                                                        PASSED
    2020-05-31T17:10:20: %EASYPY-INFO:     `-- clean_everything                                                  PASSED
    2020-05-31T17:10:20: %EASYPY-INFO: Sending report email...
    2020-05-31T17:10:20: %EASYPY-INFO: Missing SMTP server configuration, or failed to reach/authenticate/send mail. Result notification email failed to send.
    2020-05-31T17:10:20: %EASYPY-INFO: Done!

    Pro Tip
    -------
    Use the following command to view your logs locally:
        pyats logs view

    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

### Часть 4. Используйте Genie для анализа вывода команды IOS

В этой части вы будете использовать Genie, чтобы получить неструктурированный вывод IOS и преобразовать его в вывод JSON.

**Примечание**: Поддерживаются не все команды IOS. Полную документацию Genie можно найти по адресу: https://developer.cisco.com/docs/genie-docs/

**Шаг 1. Создайте тестовый YAML-файл.**

Инструменты pyATS и Genie используют файл YAML, чтобы узнать, к каким устройствам подключаться и какие учетные данные нужны. Этот файл известен как файл тестовой среды. Genie включает встроенные функции для создания файла тестовой среды для вас.

1.  Введите команду **genie --help**, чтобы увидеть все доступные команды. Для получения дополнительной справки по любой команде используйте параметр **\<command\>**, как показано ниже, для команды **create**. Обратите внимание, что **testbed** - это один из вариантов команды **create**.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ genie --help
    Usage:
    genie <command> [options]

    Commands:
        create              Create Testbed, parser, triggers, ...
        diff                Command to diff two snapshots saved to file or directory
        dnac                Command to learn DNAC features and save to file (Prototype)
        learn               Command to learn device features and save to file
        parse               Command to parse show commands
        run                 Run Genie triggers & verifications in pyATS runtime environment
        shell               enter Python shell, loading a pyATS testbed file and/or pickled data

    General Options:
    -h, --help            Show help

    Run 'genie <command> --help' for more information on a command.
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ genie create --help
    Usage:
    genie create <subcommand> [options]

    Subcommands:
        parser              create a new Genie parser from template
        testbed             create a testbed file automatically
        trigger             create a new Genie trigger from template

    General Options:
    -h, --help            Show help
    -v, --verbose         Give more output, additive up to 3 times.
    -q, --quiet           Give less output, additive up to 3 times, corresponding to WARNING, ERROR,
                            and CRITICAL logging levels
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Чтобы создать YAML-файл для тестовой системы, введите команду ниже. Параметр **--output** создаст файл **testbed.yml** в каталоге с именем **yaml**. Каталог будет создан автоматически. Параметр **--encode-password** будет кодировать пароли в файле YAML. Параметр **interactive** означает, что вам будет задан ряд вопросов. Ответьте «no» на первые три вопроса. Затем предоставьте следующие ответы, чтобы создать файл **testbed.yaml**.
-   **Device** **hostname** - должно совпадать с именем хоста устройства, которым в данной лабораторной работе является **CSR1kv**.
-   **I IP** **address** - он должен соответствовать вашему IPv4-адресу CSR1kv, который вы обнаружили ранее в этой лабораторной работе. Здесь показано **192.168.56.101**.
-   **Username** - Это локальное имя пользователя, используемое для ssh, то есть **cisco**.
-   **Default** **password** - это локальный пароль, используемый для ssh, то есть **cisco123!**.
-   **Enable** **password** - оставьте пустым. На маршрутизаторе не настроен привилегированный пароль.
-   **Protocol** - SSH вместе с группой обмена ключами, ожидаемой маршрутизатором.
-   **OS** - ОС на роутере.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ genie create testbed interactive --output yaml/testbed.yml --encode-password
    Start creating Testbed yaml file ...
    Do all of the devices have the same username? [y/n] n
    Do all of the devices have the same default password? [y/n] n
    Do all of the devices have the same enable password? [y/n] n

    Device hostname: CSR1kv
    IP (ip, or ip:port): 192.168.56.101
    Username: cisco
    Default Password (leave blank if you want to enter on demand): cisco123!
    Enable Password (leave blank if you want to enter on demand): 
    Protocol (ssh, telnet, ...): ssh -o KexAlgorithms=diffie-hellman-group14-sha1
    OS (iosxr, iosxe, ios, nxos, linux, ...): iosxe
    More devices to add ? [y/n] n
    Testbed file generated: 
    yaml/testbed.yml 

    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Используйте **cat** для просмотра файла **testbed.yml** в каталоге **yaml**. Обратите внимание на свои записи в файле YAML. Ваш пароль SSH зашифрован, и активный пароль «ПОПРОСИТ» пользователя ввести пароль, если он потребуется.

    ```yaml
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ cat yaml/testbed.yml
    devices:
    CSR1kv:
        connections:
        cli:
            ip: 192.168.56.101
            protocol: ssh -o KexAlgorithms=diffie-hellman-group14-sha1
        credentials:
        default:
            password: '%ENC{w5PDosOUw5fDosKQwpbCmMKH}'
            username: cisco
        enable:
            password: '%ASK{}'
        os: iosxe
        type: iosxe
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

**Шаг 2. Используйте Genie для синтаксического анализа вывода команды show ip interface brief в JSON.**

1.  Если вы еще не завершили **лабораторную работу «Установка ВМ CSR1000v»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину CSR1kv сейчас.
2.  На виртуальной машине CSR1kv введите команду **show ip interface brief** из привилегированного режима exec. Ваш адрес может быть увеличен до другого адреса, кроме 192.168.56.101. Запишите IPv4-адрес вашей виртуальной машины CSR1kv. Вы будете использовать его позже в лабораторной работе.

    ```
    CSR1kv> en
    CSR1kv# show ip interface brief
    Interface              IP-Address      OK? Method Status                Protocol
    GigabitEthernet1       192.168.56.101  YES DHCP   up                    up      
    CSR1kv#
    ```

1.  Используя YAML-файл вашей тестовой системы, вызовите Genie для синтаксического анализа неструктурированного вывода команды **show ip interface brief** в структурированный JSON. Эта команда включает команду IOS для анализа (**show ip interface brief**), файл тестовой среды YAML (**testbed.yml**) и указанное устройство в файле тестовой среды (**CSR1kv**).

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ genie parse "show ip interface brief" --testbed-file yaml/testbed.yml --devices CSR1kv
    Enter enable password for device CSR1kv: <Enter>
    2020-05-31T18:59:23: %UNICON-WARNING: Device 'CSR1kv' connection 'cli' does not have IP and/or port specified, ignoring
    Device 'CSR1kv' connection 'cli' does not have IP and/or port specified, ignoring
    0%|                                                                                     | 0/1 [00:00<?, ?it/s]{
    "interface": {
        "GigabitEthernet1": {
        "interface_is_ok": "YES",
        "ip_address": "192.168.56.101",
        "method": "DHCP",
        "protocol": "up",
        "status": "up"
        }
    }
    }
    100%|█████████████████████████████████████████████████████████████████████████████| 1/1 [00:00<00:00,  2.22it/s]

    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ ^C
    ```

**Шаг 3. Используйте Genie для синтаксического анализа вывода команды show version в JSON.**

В качестве другого примера можно проанализировать неструктурированный вывод команды **show version** в структурированный JSON.

```
(csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ genie parse "show version" --testbed-file yaml/testbed.yml --devices CSR1kv
Enter enable password for device CSR1kv: <Enter>
2020-05-31T18:41:32: %UNICON-WARNING: Device 'CSR1kv' connection 'cli' does not have IP and/or port specified, ignoring
Device 'CSR1kv' connection 'cli' does not have IP and/or port specified, ignoring
  0%|                                                                                     | 0/1 [00:00<?, ?it/s]{
  "version": {
    "chassis": "CSR1000V",
    "chassis_sn": "9K8P1OFYE3D",
    "compiled_by": "mcpre",
    "compiled_date": "Thu 30-Jan-20 18:48",
    "curr_config_register": "0x2102",
    "disks": {
      "bootflash:.": {
        "disk_size": "7774207",
        "type_of_disk": "virtual hard disk"
      },
      "webui:.": {
        "disk_size": "0",
        "type_of_disk": "WebUI ODM Files"
      }
    },
    "hostname": "CSR1kv",
    "image_id": "X86_64_LINUX_IOSD-UNIVERSALK9-M",
    "image_type": "production image",
    "last_reload_reason": "reload",
    "license_level": "ax",
    "license_type": "Default. No valid license found.",
    "main_mem": "2182252",
    "mem_size": {
      "non-volatile configuration": "32768",
      "physical": "3985032"
    },
    "next_reload_license_level": "ax",
    "number_of_intfs": {
      "Gigabit Ethernet": "1"
    },
    "os": "IOS-XE",
    "platform": "Virtual XE",
    "processor_type": "VXE",
    "returned_to_rom_by": "reload",
    "rom": "IOS-XE ROMMON",
    "rtr_type": "CSR1000V",
    "system_image": "bootflash:packages.conf",
    "uptime": "2 days, 6 hours, 26 minutes",
    "uptime_this_cp": "2 days, 6 hours, 27 minutes",
    "version": "16.9.5",
    "version_short": "16.9"
  }
}
100%|█████████████████████████████████████████████████████████████████████████████| 1/1 [00:00<00:00,  2.06it/s]

(csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
```

### Часть 5. Используйте Genie для сравнения конфигураций

Как вы видели, Genie можно использовать для разобрать команды show в структурированный файл json. Genie также можно использовать для:

-   Ежегодно делайте снимки конфигураций и сравнивайте их.
-   Автоматизируйте развертывание тестирования в виртуальной среде для тестирования перед развертыванием в производственной среде
-   Для устранения неполадок конфигураций путем сравнения между устройствами

В частях 5 и 6 вы увидите, как сравнивать два разных результата.

**Шаг 1. Добавьте IPv6-адрес в CSR1kv.**

1.  На виртуальной машине CSR1kv добавьте следующий IPv6-адрес:

    ```
    CSR1kv(config)# interface gig 1
    CSR1kv(config-if)# ipv6 address 2001:db8:acad:56::101/64
    ```

**Шаг 2. Используйте Genie для проверки конфигурации и анализа вывода в JSON.**

1.  Разберите неструктурированный вывод команды **show ipv6 interface** в структурированный JSON. Используйте параметр **--output**, чтобы отправить вывод в каталог **verify-ipv6-1**. Обратите внимание на вывод, что Genie сообщает вам, что были созданы два файла.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ genie parse "show ipv6 interface gig 1" --testbed-file yaml/testbed.yml --devices CSR1kv --output verify-ipv6-1
    Enter enable password for device CSR1kv: <Enter>
    2020-05-31T19:36:19: %UNICON-WARNING: Device 'CSR1kv' connection 'cli' does not have IP and/or port specified, ignoring
    Device 'CSR1kv' connection 'cli' does not have IP and/or port specified, ignoring
    100%|█████████████████████████████████████████████████████████████████████████████| 1/1 [00:00<00:00,  2.08it/s]
    +==============================================================================+
    | Genie Parse Summary for CSR1kv                                               |
    +==============================================================================+
    |  Connected to CSR1kv                                                         |
    |  -  Log: verify-ipv6-1/connection_CSR1kv.txt                                 |
    |------------------------------------------------------------------------------|
    |  Parsed command 'show ipv6 interface gig 1'                                  |
    |  -  Parsed structure: verify-ipv6-1/CSR1kv_show-ipv6-interface-              |
    | gig-1_parsed.txt                                                             |
    |  -  Device Console:   verify-ipv6-1/CSR1kv_show-ipv6-interface-              |
    | gig-1_console.txt                                                            |
    |------------------------------------------------------------------------------|
    csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Перечислите файлы, созданные Genie, в каталоге **verify-ipv6-1**. Обратите внимание, что было создано два файла с одинаковыми именами, но один из них оканчивается на **\_console.txt**, а другой - на **\_parsed.txt**. Имя каждого файла включает имя устройства и команду IOS, используемую в команде синтаксического анализа Genie.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ ls -l verify-ipv6-1
    total 16
    -rw-rw-rw- 1 devasc devasc 9094 May 31 19:36 connection_CSR1kv.txt
    -rw-rw-r-- 1 devasc devasc  745 May 31 19:36 CSR1kv_show-ipv6-interface-gig-1_console.txt
    -rw-rw-r-- 1 devasc devasc  877 May 31 19:36 CSR1kv_show-ipv6-interface-gig-1_parsed.txt
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Используйте **cat** для проверки содержимого файла **\_console.txt**. Обратите внимание на настроенный глобальный индивидуальный адрес IPv6 и автоматический локальный адрес канала EUI-64.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ cat verify-ipv6-1/CSR1kv_show-ipv6-interface-gig-1_console.txt 
    +++ CSR1kv: executing command 'show ipv6 interface gig 1' +++
    show ipv6 interface gig 1
    GigabitEthernet1 is up, line protocol is up
    IPv6 is enabled, link-local address is FE80::A00:27FF:FE73:D79F 
    No Virtual link-local address(es):
    Description: VBox
    Global unicast address(es):
        2001:DB8:ACAD:56::101, subnet is 2001:DB8:ACAD:56::/64 
    Joined group address(es):
        FF02::1
        FF02::1:FF00:101
        FF02::1:FF73:D79F
    MTU is 1500 bytes
    ICMP error messages limited to one every 100 milliseconds
    ICMP redirects are enabled
    ICMP unreachables are sent
    ND DAD is enabled, number of DAD attempts: 1
    ND reachable time is 30000 milliseconds (using 30000)
    ND NS retransmit interval is 1000 milliseconds
    CSR1kv#
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Используйте cat для проверки содержимого файла **\_parsed.txt**. Это проанализированный файл JSON команды show **ipv6 interface gig 1**.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ cat verify-ipv6-1/CSR1kv_show-ipv6-interface-gig-1_parsed.txt 
    {
    "GigabitEthernet1": {
        "enabled": true,
        "ipv6": {
        "2001:DB8:ACAD:56::101/64": {
            "ip": "2001:DB8:ACAD:56::101",
            "prefix_length": "64",
            "status": "valid"
        },
        "FE80::A00:27FF:FE73:D79F": {
            "ip": "FE80::A00:27FF:FE73:D79F",
            "origin": "link_layer",
            "status": "valid"
        },
        "enabled": true,
        "icmp": {
            "error_messages_limited": 100,
            "redirects": true,
            "unreachables": "sent"
        },
        "nd": {
            "dad_attempts": 1,
            "dad_enabled": true,
            "ns_retransmit_interval": 1000,
            "reachable_time": 30000,
            "suppress": false,
            "using_time": 30000
        }
        },
        "joined_group_addresses": [
        "FF02::1",
        "FF02::1:FF00:101",
        "FF02::1:FF73:D79F"
        ],
        "mtu": 1500,
        "oper_status": "up"
    },
    "_exclude": []
    }
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

**Шаг 3. Измените локальный адрес канала IPv6.**

На ВМ CSR1kv добавьте следующий IPv6-адрес:

```
CSR1kv> en
CSR1kv# configure terminal
Enter configuration commands, one per line. End with CNTL/Z.
CSR1kv(config)# interface gig 1
CSR1kv(config-if)# ipv6 address fe80::56:1 link-local
```

**Шаг 4. Используйте Genie для проверки конфигурации и анализа вывода в JSON.**

1.  Разберите неструктурированный вывод команды **show ipv6 interface** в структурированный JSON. Используйте параметр **--output**, чтобы отправить вывод в другой каталог **verify-ipv6-2**. Вы можете использовать историю команд, чтобы вспомнить предыдущую команду (стрелка вверх). Просто убедитесь, что вы изменили **1** на **2**, чтобы создать новый каталог **verify-ipv6-2**.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ genie parse "show ipv6 interface gig 1" --testbed-file yaml/testbed.yml --devices CSR1kv --output verify-ipv6-2
    Enter enable password for device CSR1kv: <Enter>
    2020-05-31T20:03:58: %UNICON-WARNING: Device 'CSR1kv' connection 'cli' does not have IP and/or port specified, ignoring
    Device 'CSR1kv' connection 'cli' does not have IP and/or port specified, ignoring
    100%|█████████████████████████████████████████████████████████████████████████████| 1/1 [00:00<00:00,  2.24it/s]
    +==============================================================================+
    | Genie Parse Summary for CSR1kv                                               |
    +==============================================================================+
    |  Connected to CSR1kv                                                         |
    |  -  Log: verify-ipv6-2/connection_CSR1kv.txt                                 |
    |------------------------------------------------------------------------------|
    |  Parsed command 'show ipv6 interface gig 1'                                  |
    |  -  Parsed structure: verify-ipv6-2/CSR1kv_show-ipv6-interface-              |
    | gig-1_parsed.txt                                                             |
    |  -  Device Console:   verify-ipv6-2/CSR1kv_show-ipv6-interface-              |
    | gig-1_console.txt                                                            |
    |------------------------------------------------------------------------------|

    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Перечислите файлы, созданные Genie, в каталоге **verify-ipv6-2**. Они похожи на два файла, которые вы создали перед изменением локального адреса ссылки IPv6.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ ls -l verify-ipv6-2
    total 16
    -rw-rw-rw- 1 devasc devasc 4536 May 31 20:04 connection_CSR1kv.txt
    -rw-rw-r-- 1 devasc devasc  728 May 31 20:04 CSR1kv_show-ipv6-interface-gig-1_console.txt
    -rw-rw-r-- 1 devasc devasc  846 May 31 20:04 CSR1kv_show-ipv6-interface-gig-1_parsed.txt
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Используйте **cat**, чтобы изучить содержимое каждого файла. Изменения выделены в выводе ниже.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ cat verify-ipv6-2/CSR1kv_show-ipv6-interface-gig-1_console.txt 
    +++ CSR1kv: executing command 'show ipv6 interface gig 1' +++
    show ipv6 interface gig 1
    GigabitEthernet1 is up, line protocol is up
    IPv6 is enabled, link-local address is FE80::56:1 
    No Virtual link-local address(es):
    Description: VBox
    Global unicast address(es):
        2001:DB8:ACAD:56::101, subnet is 2001:DB8:ACAD:56::/64 
    Joined group address(es):
        FF02::1
        FF02::1:FF00:101
        FF02::1:FF56:1
    MTU is 1500 bytes
    ICMP error messages limited to one every 100 milliseconds
    ICMP redirects are enabled
    ICMP unreachables are sent
    ND DAD is enabled, number of DAD attempts: 1
    ND reachable time is 30000 milliseconds (using 30000)
    ND NS retransmit interval is 1000 milliseconds
    CSR1kv#
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ cat verify-ipv6-2/CSR1kv_show-ipv6-interface-gig-1_parsed.txt 
    {
    "GigabitEthernet1": {
        "enabled": true,
        "ipv6": {
        "2001:DB8:ACAD:56::101/64": {
            "ip": "2001:DB8:ACAD:56::101",
            "prefix_length": "64",
            "status": "valid"
        },
        "FE80::56:1": {
            "ip": "FE80::56:1",
            "origin": "link_layer",
            "status": "valid"
        },
        "enabled": true,
        "icmp": {
            "error_messages_limited": 100,
            "redirects": true,
            "unreachables": "sent"
        },
        "nd": {
            "dad_attempts": 1,
            "dad_enabled": true,
            "ns_retransmit_interval": 1000,
            "reachable_time": 30000,
            "suppress": false,
            "using_time": 30000
        }
        },
        "joined_group_addresses": [
        "FF02::1",
        "FF02::1:FF00:101",
        "FF02::1:FF56:1"
        ],
        "mtu": 1500,
        "oper_status": "up"
    },
    "_exclude": []
    }
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

**Шаг 5. Используйте Genie для сравнения различий между конфигурациями.**

На предыдущем шаге довольно легко найти изменение локального адреса IPv6. Но предположим, что вы искали проблему в сложной конфигурации. Возможно, вы пытаетесь найти разницу между конфигурацией OSPF на маршрутизаторе, который получает правильные маршруты, и другим маршрутизатором, который не получает, и хотите увидеть различия в их конфигурациях OSPF. Или, возможно, вы пытаетесь обнаружить разницу в длинном списке утверждений ACL между двумя маршрутизаторами, которые должны иметь идентичные политики безопасности. Genie может провести сравнение за вас и упростить поиск различий.

1.  Используйте следующую команду, чтобы Genie обнаружил различия между двумя проанализированными файлами JSON. Обратите внимание, что в выходных данных указано, где можно найти сравнения Genie. В этом случае первое имя файла - это предыдущая конфигурация, а второе имя файла - это текущая конфигурация.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ genie diff verify-ipv6-1 verify-ipv6-2
    1it [00:00, 579.32it/s]
    +==============================================================================+
    | Genie Diff Summary between directories verify-ipv6-1/ and verify-ipv6-2/     |
    +==============================================================================+
    |  File: CSR1kv_show-ipv6-interface-gig-1_parsed.txt                           |
    |   - Diff can be found at ./diff_CSR1kv_show-ipv6-interface-gig-1_parsed.txt  |
    |------------------------------------------------------------------------------|
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

1.  Используйте **cat**, чтобы просмотреть содержимое файла с различиями. Знак плюс «+» указывает на добавления, а знак минус «-» указывает на то, что было удалено.

    ```
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ cat ./diff_CSR1kv_show-ipv6-interface-gig-1_parsed.txt
    --- verify-ipv6-1/CSR1kv_show-ipv6-interface-gig-1_parsed.txt
    +++ verify-ipv6-2/CSR1kv_show-ipv6-interface-gig-1_parsed.txt
    GigabitEthernet1:
    ipv6:
    +  FE80::56:1: 
    +   ip: FE80::56:1
    +   origin: link_layer
    +   status: valid
    -  FE80::A00:27FF:FE73:D79F: 
    -   ip: FE80::A00:27FF:FE73:D79F
    -   origin: link_layer
    -   status: valid
    joined_group_addresses:
    -  index[2]: FF02::1:FF73:D79F
    +  index[2]: FF02::1:FF56:1
    (csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
    ```

### Часть 6. Очистка лаборатории и дальнейшее расследование

В этой части вы деактивируете свой Python venv и исследуете другие варианты использования Genie.

**Шаг 1. Деактивируйте виртуальную среду Python.**

По завершении этой лабораторной работы вы можете деактивировать виртуальную среду Python с помощью команды **deactivate**. Обратите внимание, что вашему запросу больше не предшествует «(csr1kv)».

```
(csr1kv) devasc@labvm:~/labs/devnet-src/pyats/csr1kv$ deactivate
devasc@labvm:~/labs/devnet-src/pyats/csr1kv$
```

**Шаг 2. Изучите другие варианты использования pyATS и Genie.**

1.  Ранее в этой лабораторной работе вы клонировали папку **examples** из Cisco Test Automation с pyATS и репозиторием Genie на GitHub.
2.  Есть много других вариантов использования и в этом репозитории GitHub. Возможно, вы захотите изучить другие папки и другие варианты использования. См. Следующие веб-сайты для получения дополнительной информации:
    -   Искать: «Проверка NetDevOps с использованием Cisco pyATS \| Genie для сетевых инженеров: кодирование не требуется»
    -   Cisco GitHub: <https://github.com/CiscoTestAutomation>
