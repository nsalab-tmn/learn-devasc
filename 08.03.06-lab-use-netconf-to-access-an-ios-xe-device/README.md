## Цели

-   Часть 1: Постройте сеть и проверьте подключение
-   Часть 2: Использование сеанса NETCONF для сбора информации
-   Часть 3: Используйте ncclient для подключения к NETCONF
-   Часть 4: Использование ncclient для получения конфигурации
-   Часть 5: Использование ncclient для настройки устройства
-   Часть 6. Задача: изменить программу, используемую в этой лабораторной работе

## Предпосылки / Сценарий

Протокол конфигурации сети (NETCONF), определенный в RFC 4741 и 6241, использует модели данных YANG для связи с различными устройствами в сети. YANG (еще одно следующее поколение) - это язык моделирования данных. Этот язык определяет данные, которые отправляются по протоколам сетевого управления, таким как NETCONF. При использовании NETCONF для доступа к устройству IOS XE данные возвращаются в формате XML.

В этой лабораторной работе вы будете использовать клиент NETCONF, ncclient, который представляет собой модуль Python для написания сценариев на стороне клиента. Вы будете использовать ncclient для проверки настройки NETCONF, получения конфигурации устройства и изменения конфигурации устройства.

## Необходимые ресурсы

-   1 ПК с операционной системой по вашему выбору
-   Virtual Box или VMWare
-   Виртуальная машина DEVASC
-   Виртуальная машина CSR1kv

## Инструкции

### Часть 1. Запустите виртуальные машины и проверьте подключение

В этой части вы запускаете две виртуальные машины и проверяете возможность подключения. Затем вы установите соединение с защищенной оболочкой (SSH).

**Шаг 1. Запустите виртуальные машины**

Если вы еще не завершили **лабораторные работы «Установка виртуальной лабораторной среды»** и **«Установка ВМ CSR1kv»**, сделайте это сейчас. Если вы уже выполнили эти лабораторные работы, запустите виртуальную машину DEVASC и CSR1000v.

**Шаг 2. Проверьте подключение между виртуальными машинами.**

1.  На виртуальной машине CSR1kv нажмите Enter, чтобы получить командную строку, а затем используйте команду **show ip interface brief**, чтобы убедиться, что адрес IPv4 - 192.168.56.101. Если адрес другой, запишите его.
1.  Откройте терминал в VS Code на виртуальной машине DEVASC.
1.  Проверьте связь с CSR1kv. Вы уже должны были сделать это ранее в лабораторных условиях. Если вы не можете выполнить эхо-запрос, посетите лабораторные работы, перечисленные выше в части 1а.

    ```
    devasc@labvm:~$ ping -c 5 192.168.56.101
    PING 192.168.56.101 (192.168.56.101) 56(84) bytes of data.
    64 bytes from 192.168.56.101: icmp_seq=1 ttl=254 time=1.37 ms
    64 bytes from 192.168.56.101: icmp_seq=2 ttl=254 time=1.15 ms
    64 bytes from 192.168.56.101: icmp_seq=3 ttl=254 time=0.981 ms
    64 bytes from 192.168.56.101: icmp_seq=4 ttl=254 time=1.01 ms
    64 bytes from 192.168.56.101: icmp_seq=5 ttl=254 time=1.14 ms

    --- 192.168.56.101 ping statistics ---
    5 packets transmitted, 5 received, 0% packet loss, time 4006ms
    rtt min/avg/max/mdev = 0.981/1.130/1.365/0.135 ms
    devasc@labvm:~$
    ```

**Шаг 3. Проверьте подключение SSH к виртуальной машине CSR1kv.**

1.  В терминале для виртуальной машины DEVASC подключитесь по SSH к виртуальной машине CSR1kv с помощью следующей команды:

    ```
    devasc@labvm:~$ ssh  cisco@192.168.56.101
    ```

    >   **Примечание**: При первом подключении к CSR1kv по SSH виртуальная машина DEVASC предупреждает вас о подлинности CSR1kv. Поскольку вы доверяете CSR1kv, ответьте на приглашение да.

    ```
    The authenticity of host '192.168.56.101 (192.168.56.101)' can't be established.
    RSA key fingerprint is SHA256:HYv9K5Biw7PFiXeoCDO/LTqs3EfZKBuJdiPo34VXDUY.
    Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
    Warning: Permanently added '192.168.56.101' (RSA) to the list of known hosts.
    ```

1.  Введите **cisco123!** в качестве пароля, и теперь вы должны попасть в привилегированную командную строку EXEC для CSR1kv.

    ```
    Password: 

    CSR1kv#
    ```

1.  Оставьте сеанс SSH открытым для следующей части.

### Часть 2. Используйте сеанс NETCONF для сбора информации

В этой части вы убедитесь, что NETCONF работает, включите NETCONF, если это не так, и убедитесь, что NETCONF готов к SSH-соединению. Затем вы подключитесь к процессу NETCONF, начнете сеанс NETCONF, соберете информацию об интерфейсе и закроете сеанс.

**Шаг 1. Проверьте, работает ли NETCONF на CSR1kv.**

1.  NETCONF может уже быть запущен, если другой учащийся включил его или если более поздняя версия IOS включает его по умолчанию. В сеансе SSH с CSR1kv используйте команду **show platform software yang-management process**, чтобы проверить, запущен ли демон NETCONF SSH (ncsshd).

    ```
    CSR1kv# show platform software yang-management process 
    confd            : Running 
    nesd             : Running 
    syncfd           : Running 
    ncsshd           : Running 
    dmiauthd         : Running 
    nginx            : Running 
    ndbmand          : Running 
    pubd             : Running 

    CSR1kv#
    ```

1.  Если NETCONF не запущен, как показано в выходных данных выше, введите команду глобальной конфигурации **netconf-yang**.

    ```
    CSR1kv# config t
    CSR1kv (config)# netconf-yang
    ```

1.  Введите **exit**, чтобы закрыть сеанс SSH.

**Шаг 2. Получите доступ к процессу NETCONF через терминал SSH.**

На этом этапе вы повторно установите сеанс SSH с CSR1kv. Но на этот раз вы укажете порт 830 NETCONF и отправите **netconf** как команду подсистемы.

>   **Примечание**: Для получения дополнительной информации об этих параметрах изучите страницы руководства по SSH (**man ssh**).

1.  Введите следующую команду в окне терминала. Вы можете использовать стрелку вверх, чтобы вызвать последнюю команду SSH, и просто добавьте параметры -p и -s, как показано. Затем введите cisco123! в качестве пароля.

    ```
    devasc@labvm:~$ ssh cisco@192.168.56.101 -p 830 -s netconf
    cisco@192.168.56.101's password:
    ```

1.  CSR1kv ответит сообщением **hello**, которое включает более 400 строк вывода, в которых перечислены все его возможности NETCONF. Конец сообщений NETCONF обозначается **]]\>]]\>**.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <hello xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
    <capabilities>
    <capability>urn:ietf:params:netconf:base:1.0</capability>
    <capability>urn:ietf:params:netconf:base:1.1</capability>
    <capability>urn:ietf:params:netconf:capability:writable-running:1.0</capability>
    <capability>urn:ietf:params:netconf:capability:xpath:1.0</capability>
    <capability>urn:ietf:params:netconf:capability:validate:1.0</capability>
    <capability>urn:ietf:params:netconf:capability:validate:1.1</capability>
    (output omitted)
        </capability>
    </capabilities>
    <session-id>20</session-id></hello>]]>]]>
    ```

**Шаг 3. Запустите сеанс NETCONF, отправив приветственное сообщение от клиента.**

Чтобы начать сеанс NETCONF, клиенту необходимо отправить собственное приветственное сообщение. Приветственное сообщение должно включать версию базовых возможностей NETCONF, которую клиент хочет использовать.

1.  Скопируйте и вставьте следующий XML-код в сеанс SSH. Обратите внимание, что конец приветственного сообщения клиента обозначен значком **]]\>]]\>**.

    ```xml
    <hello xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
    <capabilities>
    <capability>urn:ietf:params:netconf:base:1.0</capability>
    </capabilities>
    </hello>
    ]]>]]>
    ```

1.  Переключитесь на виртуальную машину CSR1kv и используйте команду **show netconf-yang sessions**, чтобы убедиться, что сеанс NETCONF запущен. Если экран ВМ CSR1kv темный, нажмите **Enter**, чтобы его разбудить.

Открыть окно конфигурации

    ```
    CSR1kv> en
    CSRk1v# show netconf-yang sessions
    R: Global-lock on running datastore
    C: Global-lock on candidate datastore
    S: Global-lock on startup datastore

    Number of sessions : 1

    session-id  transport    username             source-host           global-lock
    -------------------------------------------------------------------------------
    20          netconf-ssh  cisco                192.168.56.1          None

    CSR1kv#
    ```

Закрыть окно конфигурации

**Шаг 4. Отправляйте сообщения RPC на устройство IOS XE.**

Во время сеанса SSH клиент NETCONF может использовать сообщения удаленного вызова процедур (RPC) для отправки операций NETCONF на устройство IOS XE. В таблице перечислены некоторые из наиболее распространенных операций NETCONF.

| **Операция**          | **Описание**                                                                    |
|-----------------------|---------------------------------------------------------------------------------|
| \<get\>               | Получение информации о текущей конфигурации и состоянии устройства              |
| \<get-config\>        | Получить все или часть указанного хранилища данных конфигурации                 |
| \<edit-config\>       | Загружает всю или часть конфигурации в указанное хранилище данных конфигурации. |
| \<copy-config\>       | Заменить все хранилище данных конфигурации другим                               |
| \<delete-config\>     | Удалить хранилище данных конфигурации                                           |
| \<commit\>            | Скопируйте хранилище данных кандидата в текущее хранилище данных                |
| \<lock\> / \<unlock\> | Блокировка или разблокировка всей системы хранения данных конфигурации          |
| \<close-session\>     | Грамотное завершение сеанса NETCONF                                             |
| \<kill-session\>      | Принудительное завершение сеанса NETCONF                                        |

1.  Скопируйте и вставьте следующий XML-код сообщения **get** RPC в сеанс SSH терминала, чтобы получить информацию об интерфейсах на R1.

    ```
    <rpc message-id="103" xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
    <get>
    <filter>
        <interfaces xmlns="urn:ietf:params:xml:ns:yang:ietf-interfaces"/>
    </filter>
    </get>
    </rpc>
    ]]>]]>
    ```

1.  Напомним, что XML не требует отступов или пробелов. Следовательно, CSR1kv вернет длинную строку данных XML.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="103"><data><interfaces xmlns="urn:ietf:params:xml:ns:yang:ietf-interfaces"><interface><name>GigabitEthernet1</name><description>VBox</description><type xmlns:ianaift="urn:ietf:params:xml:ns:yang:iana-if-type">ianaift:ethernetCsmacd</type><enabled>true</enabled><ipv4 xmlns="urn:ietf:params:xml:ns:yang:ietf-ip"></ipv4><ipv6 xmlns="urn:ietf:params:xml:ns:yang:ietf-ip"></ipv6></interface></interfaces></data></rpc-reply>]]>]]>
    ```

1.  Скопируйте возвращенный XML, но не включайте последние символы «]]\>]]\>». Эти символы не являются частью XML, возвращаемого маршрутизатором.
1.  Поищите в Интернете «prettify XML». Найдите подходящий сайт и используйте его инструмент для преобразования вашего XML в более читаемый формат, например следующий:

    ```xml
    <?xml version="1.0"?>
    <rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="103">
    <data>
        <interfaces xmlns="urn:ietf:params:xml:ns:yang:ietf-interfaces">
        <interface>
            <name>GigabitEthernet1</name>
            <description>VBox</description>
            <type xmlns:ianaift="urn:ietf:params:xml:ns:yang:iana-if-type">ianaift:ethernetCsmacd</type>
            <enabled>true</enabled>
            <ipv4 xmlns="urn:ietf:params:xml:ns:yang:ietf-ip"/>
            <ipv6 xmlns="urn:ietf:params:xml:ns:yang:ietf-ip"/>
        </interface>
        </interfaces>
    </data>
    </rpc-reply>
    ```

**Шаг 5. Закройте сеанс NETCONF.**

1.  Чтобы закрыть сеанс NETCONF, клиенту необходимо отправить следующее сообщение RPC:

    ```xml
    <rpc message-id="9999999" xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
    <close-session />
    </rpc>
    ```

1.  Через несколько секунд вы вернетесь в окно терминала. Вернитесь к приглашению CSR1kv и покажите открытые сеансы netconf. Вы увидите, что сеанс закрыт.

    ```
    CSR1kv# show netconf-yang sessions
    There are no active sessions

    CSR1kv#
    ```

### Часть 3. Используйте ncclient для подключения к NETCONF

Работа с NETCONF не требует работы с необработанными сообщениями NETCONF RPC и XML. В этой части вы узнаете, как использовать модуль **ncclient** Python для простого взаимодействия с сетевыми устройствами с помощью NETCONF. Кроме того, вы узнаете, как определить, какие модели YANG поддерживаются устройством. Эта информация полезна при построении системы автоматизации производственной сети, которая требует, чтобы определенные модели YANG поддерживались данным сетевым устройством.

**Шаг 1. Убедитесь, что ncclient установлен и готов к использованию.**

В терминале DEVASC-VM введите команду **pip3 list --format = columns**, чтобы увидеть все установленные в данный момент модули Python. Подключите вывод к **more**. Ваш результат может отличаться от следующего. Но вы должны увидеть в списке **ncclient**, как показано. Если нет, используйте команду **pip3 install ncclient**, чтобы установить его.

```
devasc@labvm:~$ pip3 list --format=columns | more
Package                Version    
---------------------- -----------
ansible                2.9.6      
apache-libcloud        2.8.0      
appdirs                1.4.3      
argcomplete            1.8.1      
astroid                2.3.3      
(output omitted)
ncclient               0.6.7      
netaddr                0.7.19     
netifaces              0.10.4     
netmiko                3.1.0      
ntlm-auth              1.1.0      
oauthlib               3.1.0
(output omitted)
xmltodict              0.12.0     
zipp                   1.0.0      
devasc@labvm:~$
```

**Шаг 2. Создайте сценарий для использования ncclient для подключения к службе NETCONF.**

Модуль ncclient предоставляет класс **manager** с методом **connect()** для настройки удаленных соединений NETCONF. После успешного подключения возвращаемый объект представляет подключение NETCONF к удаленному устройству.

1.  В коде VS щелкните **File \> Open** **Folder...** и перейдите в каталог **devnet-src**. Щелкните **ОК**.
1.  Откройте окно терминала в VS Code: **Terminal\> New Terminal**.
1.  Создайте подкаталог **netconf** в каталоге **/devnet-src**.

    ```
    devasc@labvm:~/labs/devnet-src$ mkdir netconf
    devasc@labvm:~/labs/devnet-src$
    ```

1.  На панели **EXPLORER** в **DEVNET-SRC** щелкните правой кнопкой мыши каталог **netconf** и выберите **New File**.
1.  Назовите файл **ncclient-netconf.py**.
1.  В файле сценария импортируйте класс менеджера из модуля **ncclient**. Затем создайте переменную m для представления метода **connect()**. Метод **connect()** включает всю информацию, необходимую для подключения к службе NETCONF, работающей на CSR1kv. Обратите внимание, что порт 830 для NETCONF.

    ```python
    from ncclient import manager

    m = manager.connect(
        host="192.168.56.101",
        port=830,
        username="cisco",
        password="cisco123!",
        hostkey_verify=False
        )
    ```

Если **hostkey_verify** имеет значение True, CSR1kv попросит вас проверить отпечаток SSH. В лабораторных условиях можно безопасно установить это значение в False, как мы это сделали здесь.

1.  Сохраните и запустите программу, чтобы убедиться, что ошибок нет. Вы пока не увидите никаких результатов.

    ```
    devasc@labvm:~/labs/devnet-src$ cd netconf/
    devasc@labvm:~/labs/devnet-src/netconf$ python3 ncclient-netconf.py 
    devasc@labvm:~/labs/devnet-src/netconf$
    ```

1.  Вы можете проверить, что CSR1kv принял запрос на сеанс NETCONF. В виртуальной машине CSR1kv должно быть сообщение системного журнала **%DMI-5-AUTH_PASSED**. Если экран черный, нажмите **Enter**, чтобы разбудить роутер. Сообщение системного журнала можно увидеть над баннером.

**Шаг 3. Добавьте в сценарий функцию печати, чтобы были перечислены возможности NETCONF для CSR1kv.**

Объект **m**, возвращаемый функцией **manager.connect()**, представляет удаленный сеанс NETCONF. Как вы видели ранее, в каждом сеансе NETCONF сервер сначала отправляет свои возможности, которые представляют собой список в формате XML поддерживаемых моделей YANG. С модулем **ncclient** полученный список возможностей сохраняется в списке **m.server_capabilities**.

1.  Используйте цикл **for** и функцию **print** для отображения возможностей устройства:

    ```python
    print("#Supported Capabilities (YANG models):")
    for capability in m.server_capabilities:
        print(capability)
    ```

1.  Сохраните и запустите программу. Результат тот же, что и при отправке сложного сообщения **hello** ранее, но без открывающего и закрывающего тега XML \<capability\> в каждой строке.

    ```
    devasc@labvm:~/labs/devnet-src/netconf$ python3 ncclient-netconf.py 
    #Supported Capabilities (YANG models):
    urn:ietf:params:netconf:base:1.0
    urn:ietf:params:netconf:base:1.1
    urn:ietf:params:netconf:capability:writable-running:1.0
    urn:ietf:params:netconf:capability:xpath:1.0
    <output omitted>
    urn:ietf:params:xml:ns:netconf:base:1.0?module=ietf-netconf&revision=2011-06-01
    urn:ietf:params:xml:ns:yang:ietf-netconf-with-defaults?module=ietf-netconf-with-defaults&revision=2011-06-01

            urn:ietf:params:netconf:capability:notification:1.1
        
    devasc@labvm:~/labs/devnet-src/netconf$
    ```

### Часть 4. Используйте ncclient для получения конфигурации

В этой части вы будете использовать NETCONF ncclient для получения конфигурации для CSR1kv, использовать модуль **xml.dom.minidom** для форматирования конфигурации и использовать фильтр с get_config() для получения части текущей конфигурации.

**Шаг 1. Используйте функцию get_config(), чтобы получить текущую конфигурацию для R1.**

1.  Если вы хотите пропустить отображение вывода возможностей (более 400 строк), закомментируйте блок операторов, которые печатают возможности, как показано ниже:

    ```python
    '''
    print("#Supported Capabilities (YANG models):")
    for capability in m.server_capabilities:
        print(capability)
    '''
    ```

1.  Вы можете использовать метод **get_config()** объекта сеанса **m** NETCONF для получения конфигурации для CSR1kv. Метод **get_config()** ожидает параметр исходной строки, указывающий исходное хранилище данных NETCONF. Используйте функцию печати, чтобы отобразить результаты. Единственное хранилище данных NETCONF в настоящее время на CSR1kv - это хранилище данных **running**. Вы можете проверить это с помощью команды **show netconf-yang datastores**.

    ```python
    netconf_reply = m.get_config(source="running")
    print(netconf_reply)
    ```

1.  Сохраните и запустите вашу программу. На выходе будет более 100 строк, поэтому IDLE может их сжать. Дважды щелкните сообщение **Squeezed text** в окне оболочки IDLE, чтобы развернуть вывод.

    ```xml
    devasc@labvm:~/labs/devnet-src/netconf$ python3 ncclient-netconf.py 
    <?xml version="1.0" encoding="UTF-8"?>
    <rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="urn:uuid:3f31bedc-5671-47ca-9781-4d3d7aadae24" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0"><data><native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native"><version>16.9</version><boot-start-marker/><boot-end-marker/><banner><motd><banner>
    (output omitted)
    devasc@labvm:~/labs/devnet-src/netconf$
    ```

1.  Обратите внимание, что возвращенный XML не отформатирован. Вы можете скопировать его на тот же сайт, который вы нашли ранее, чтобы усовершенствовать XML.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="urn:uuid:3f31bedc-5671-47ca-9781-4d3d7aadae24">
    <data>
        <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
        <version>16.9</version>
        <boot-start-marker/>
        <boot-end-marker/>
        <banner>
            <motd>
            <banner>^C</banner>
            </motd>
        </banner>
        <service>
            <timestamps>
            <debug>
                <datetime>
                <msec/>
                </datetime>
            </debug>
            <log>
                <datetime>
                <msec/>
                </datetime>
            </log>
            </timestamps>
        </service>
        <platform>
            <console xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-platform">
            <output>virtual</output>
            </console>
        </platform>
        <hostname>CSR1kv</hostname>
    (output omitted)
    ```

**Шаг 2. Используйте Python для уточнения XML.**

Python имеет встроенную поддержку работы с XML-файлами. Модуль **xml.dom.minidom** можно использовать для предварительной настройки вывода с помощью функции **toprettyxml()**.

1.  В начале вашего скрипта добавьте инструкцию для импорта модуля **xml.dom.minidom**.

    ```python
    import xml.dom.minidom
    ```

1.  Замените простую функцию печати **print(netconf_reply)** версией, которая печатает предопределенный вывод XML.

    ```python
    print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())
    ```

1.  Сохраните и запустите вашу программу. XML отображается в более читаемом формате.

    ```xml
    devasc@labvm:~/labs/devnet-src/netconf$ python3 ncclient-netconf.py 
    <?xml version="1.0" ?>
    <rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="urn:uuid:3a5f6abc-76b4-436d-9e9a-7758091c28b7">
            <data>
                    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
                            <version>16.9</version>
                            <boot-start-marker/>
                            <boot-end-marker/>
                            <banner>
                                    <motd>
                                            <banner>^C</banner>
                                    </motd>
                            </banner>
    (output omitted)
            </data>
    </rpc-reply>

    devasc@labvm:~/labs/devnet-src/netconf$
    ```

**Шаг 3. Используйте фильтр с get_config(), чтобы получить только определенную модель YANG.**

Сетевой администратор может захотеть получить только часть текущей конфигурации на устройстве. NETCONF поддерживает возврат только данных, которые определены в параметре фильтра функции **get_conf()**.

1.  Создайте переменную с именем **netconf_filter**, которая извлекает только данные, определенные моделью YANG Cisco IOS XE Native.

    ```python
    netconf_filter = """
    <filter>
        <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native" />
    </filter>
    """
    netconf_reply = m.get_config(source="running", filter=netconf_filter)
    print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())
    ```

1.  Сохраните и запустите вашу программу. Начало вывода такое же, как показано ниже. Однако на этот раз отображается только элемент XML \<native\>. Раньше отображались все модели YANG, доступные на CSR1kv. Фильтрация извлеченных данных для отображения только встроенного модуля YANG значительно сокращает объем вывода. Это связано с тем, что собственный модуль YANG включает только подмножество всех моделей Cisco IOX XE YANG.

    ```xml
    devasc@labvm:~/labs/devnet-src/netconf$ python3 ncclient-netconf.py 
    <?xml version="1.0" ?> 
    <rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="urn:uuid:4da5b736-1d33-47c3-8e3c-349414be0958">
            <data>
                    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
                            <version>16.9</version>
                            <boot-start-marker/>
                            <boot-end-marker/>
                            <banner>
                                    <motd>
                                            <banner>^C</banner>
                                    </motd>
                            </banner>
                            <service>
                                    <timestamps>
                                            <debug>
                                                    <datetime>
                                                            <msec/>
                                                    </datetime>
                                            </debug>
                                            <log>
                                                    <datetime>
                                                            <msec/>
                                                    </datetime>
                                            </log>
                                    </timestamps>
                            </service>
                            <platform>
                                    <console xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-platform">
                                            <output>virtual</output>
                                    </console>
                            </platform>
                            <hostname>CSR1kv</hostname>
    (output omitted)
                    </native>
            </data>
    </rpc-reply>

    devasc@labvm:~/labs/devnet-src/netconf$
    ```

### Часть 5. Используйте ncclient для настройки устройства

В этой части вы будете использовать **ncclient** для настройки CSR1kv с помощью метода **edit_config()** модуля **manager**.

**Шаг 1. Используйте ncclient, чтобы отредактировать имя хоста для CSR1kv.**

Чтобы обновить существующий параметр в конфигурации для CSR1kv, вы можете извлечь местоположение параметра из конфигурации, полученной ранее. На этом шаге вы установите переменную для изменения значения **\<hostname\>**.

```xml
devasc@labvm:~/labs/devnet-src/netconf$ python3 ncclient-netconf.py 
<?xml version="1.0" ?> 
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="urn:uuid:4da5b736-1d33-47c3-8e3c-349414be0958">
        <data>
                <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
(output omitted)
                        <hostname>CSR1kv</hostname>
(output omitted)
```

1.  Ранее вы определили переменную \<filter\>. Чтобы изменить конфигурацию устройства, вы определите переменную \<config\>. Добавьте следующую переменную в свой сценарий **ncclient_netconf.py**. Вы можете использовать NEWHOSTNAME или любое другое имя хоста, какое пожелаете.

    ```python
    netconf_hostname = """
    <config>
    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
        <hostname>NEWHOSTNAME</hostname>
    </native>
    </config>
    """
    ```

1.  Используйте функцию **edit_config()** объекта сеанса **m** NETCONF для отправки конфигурации и сохранения результатов в переменной **netconf_reply**, чтобы их можно было распечатать. Параметры функции **edit_config()** следующие:
    -   **target** - целевое хранилище данных NETCONF, которое необходимо обновить
    -   **config** - модификация конфигурации, которая должна быть отправлена

    ```python
    netconf_reply = m.edit_config(target="running", config=netconf_hostname)
    ```

1.  Функция **edit_config()** возвращает ответное сообщение XML RPC с **\<ok/\>**, указывающим, что изменение было успешно применено. Повторите предыдущий оператор печати, чтобы отобразить результаты.

    ```python
    print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())
    ```

1.  Сохраните и запустите вашу программу. Вы должны получить результат, аналогичный показанному ниже. Вы также можете убедиться, что имя хоста изменилось, переключившись на виртуальную машину CSR1kv.

    ```xml
    devasc@labvm:~/labs/devnet-src/netconf$ python3 ncclient-netconf.py 
    (output omitted)
    <?xml version="1.0" ?>
    <rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="urn:uuid:e304b225-7951-4029-afd5-59e8e7edbaa0">
            <ok/>
    </rpc-reply>

    devasc@labvm:~/labs/devnet-src/netconf$
    ```

1.  Отредактируйте свой сценарий, чтобы снова изменить имя хоста на CSR1kv. Сохраните и запустите вашу программу. Вы также можете просто закомментировать код из предыдущего шага, если хотите избежать повторного изменения имени хоста.

**Шаг 2. Используйте ncclient для создания нового интерфейса обратной петли на маршрутизаторе R1.**

1.  Создайте новую переменную \<config\> для хранения конфигурации нового интерфейса обратной петли. Добавьте следующее в свой сценарий **ncclient_netconf.py**.

    >   **Примечание**: Вы можете использовать любой **description**, какое захотите. Однако используйте только буквенно-цифровые символы, иначе вам нужно будет экранировать их обратной косой чертой (\\).

    ```python
    netconf_loopback = """
    <config>
    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
    <interface>
    <Loopback>
        <name>1</name>
        <description>My first NETCONF loopback</description>
        <ip>
        <address>
        <primary>
        <address>10.1.1.1</address>
        <mask>255.255.255.0</mask>
        </primary>
        </address>
        </ip>
    </Loopback>
    </interface>
    </native>
    </config>
    """
    ```

1.  Добавьте следующую функцию **edit_config()** в свой **ncclient_netconf.py**, чтобы отправить новую конфигурацию обратной связи на R1, а затем распечатать результаты.

    ```python
    netconf_reply = m.edit_config(target="running", config=netconf_loopback)
    print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())
    ```

1.  Сохраните и запустите вашу программу. Вы должны получить следующий результат:

    ```xml
    devasc@labvm:~/labs/devnet-src/netconf$ python3 ncclient-netconf.py 
    (output omitted)
    <?xml version="1.0" ?>
    <rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="urn:uuid:98437f47-7a93-4cac-9b9e-9bc8afc9dfa1">
            <ok/>
    </rpc-reply>

    devasc@labvm:~/labs/devnet-src/netconf$
    ```

1.  На CSR1kv убедитесь, что был создан новый интерфейс обратной петли.

    Открыть окно конфигурации

    ```
    CSR1kv>en
    CSR1kv# show ip interface brief
    Interface              IP-Address      OK? Method Status                Protocol
    GigabitEthernet1       192.168.56.101  YES DHCP   up                    up      
    Loopback1              10.1.1.1        YES other  up                    up      
    CSR1kv# show run | section interface Loopback1
    interface Loopback1
    description My first NETCONF loopback
    ip address 10.1.1.1 255.255.255.0
    CSR1kv#
    ```

    Закрыть окно конфигурации

**Шаг 3. Попытайтесь создать новый интерфейс обратной петли с тем же IPv4-адресом.**

1.  Создайте новую переменную **netconf_newloop**. Он будет содержать конфигурацию, которая создает новый интерфейс loopback 2, но с тем же адресом IPv4, что и в loopback 1: 10.1.1.1 / 24. В интерфейсе командной строки маршрутизатора это приведет к ошибке из-за попытки назначить дублирующийся IP-адрес интерфейсу.

    ```python
    netconf_newloop = """
    <config>
    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
    <interface>
    <Loopback>
        <name>2</name>
        <description>My second NETCONF loopback</description>
        <ip>
        <address>
        <primary>
        <address>10.1.1.1</address>
        <mask>255.255.255.0</mask>
        </primary>
        </address>
        </ip>
    </Loopback>
    </interface>
    </native>
    </config>
    """
    ```

1.  Добавьте следующую функцию **edit_config()** в свой **ncclient_netconf.py**, чтобы отправить новую конфигурацию обратной связи в CSR1kv. Для этого шага вам не нужен оператор печати.

    ```python
    netconf_reply = m.edit_config(target="running", config=netconf_newloop)
    ```

1.  Сохраните и запустите программу. Вы должны получить сообщение об ошибке, подобное приведенному ниже, с сообщением RPCError **Device refused one or more commands** (Устройство отклонило одну или несколько команд).

    ```
    devasc@labvm:~/labs/devnet-src/netconf$ python3 ncclient-netconf.py
    Traceback (most recent call last):
    File "ncclient-netconf.py", line 80, in <module>
        netconf_reply = m.edit_config(target="running", config=netconf_newloop)
    File "/home/devasc/.local/lib/python3.8/site-packages/ncclient/manager.py", line 231, in execute
        return cls(self._session,
    File "/home/devasc/.local/lib/python3.8/site-packages/ncclient/operations/edit.py", line 69, in request
        return self._request(node)
    File "/home/devasc/.local/lib/python3.8/site-packages/ncclient/operations/rpc.py", line 348, in _request
        raise self._reply.error
    ncclient.operations.rpc.RPCError: inconsistent value: Device refused one or more commands
    devasc@labvm:~/labs/devnet-src/netconf$
    ```

1.  NETCONF не будет применять никакую конфигурацию, отправленную, если одна или несколько команд будут отклонены. Чтобы проверить это, введите на маршрутизаторе R1 команду **show ip interface brief**. Обратите внимание, что ваш новый интерфейс не был создан.

    ```
    CSR1kv# show ip interface brief
    Interface              IP-Address      OK? Method Status                Protocol
    GigabitEthernet1       192.168.56.101  YES DHCP   up                    up      
    Loopback1              10.1.1.1        YES other  up                    up      
    ```

Закрыть окно конфигурации

### Часть 6. Задача: изменить программу, используемую в этой лабораторной работе

Ниже приведена полная программа, созданная в этой лабораторной работе, без каких-либо комментариев, поэтому вы можете запустить сценарий без ошибок. Ваш сценарий может выглядеть иначе. Практикуйте свои навыки Python, изменив программу для отправки различных команд проверки и настройки.

```python
from ncclient import manager
import xml.dom.minidom

m = manager.connect(
    host="192.168.56.101",
    port=830,
    username="cisco",
    password="cisco123!",
    hostkey_verify=False
    )

print("#Supported Capabilities (YANG models):")
for capability in m.server_capabilities:
    print(capability) 

netconf_reply = m.get_config(source="running")
print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())

netconf_filter = """
<filter>
    <native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native" />
</filter>
"""
netconf_reply = m.get_config(source="running", filter=netconf_filter)
print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())

netconf_hostname = """
<config>
<native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
    <hostname>CSR1kv</hostname>
</native>
</config>
"""
netconf_reply = m.edit_config(target="running", config=netconf_hostname)
print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())

netconf_loopback = """
<config>
<native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
<interface>
<Loopback>
    <name>1</name>
    <description>My NETCONF loopback</description>
    <ip>
    <address>
    <primary>
    <address>10.1.1.1</address>
    <mask>255.255.255.0</mask>
    </primary>
    </address>
    </ip>
</Loopback>
</interface>
</native>
</config>
"""
netconf_reply = m.edit_config(target="running", config=netconf_loopback)
print(xml.dom.minidom.parseString(netconf_reply.xml).toprettyxml())

netconf_newloop = """
<config>
<native xmlns="http://cisco.com/ns/yang/Cisco-IOS-XE-native">
<interface>
<Loopback>
    <name>2</name>
    <description>My second NETCONF loopback</description>
    <ip>
    <address>
    <primary>
    <address>10.1.1.1</address>
    <mask>255.255.255.0</mask>
    </primary>
    </address>
    </ip>
</Loopback>
</interface>
</native>
</config>
"""
netconf_reply = m.edit_config(target="running", config=netconf_newloop)
```
