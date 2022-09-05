## Цели

-   Часть 1: Постройте сеть и проверьте подключение
-   Часть 2: Настройка устройства IOS XE для доступа RESTCONF
-   Часть 3: Откройте и настройте Postman
-   Часть 4. Использование почтальона для отправки запросов GET
-   Часть 5. Использование почтальона для отправки запроса PUT
-   Часть 6. Использование скрипта Python для отправки запросов GET
-   Часть 7. Использование сценария Python для отправки запроса PUT

## Предпосылки / Сценарий

Протокол RESTCONF предоставляет упрощенное подмножество функций NETCONF через RESTful API. RESTCONF позволяет выполнять вызовы RESTful API на устройство IOS XE. Данные, возвращаемые API, могут быть отформатированы в XML или JSON. В первой половине этой лабораторной работы вы будете использовать программу Postman для создания и отправки запросов API к службе RESTCONF, работающей на CSR1kv. Во второй половине лабораторной работы вы создадите сценарии Python для выполнения тех же задач, что и ваша программа Postman.

## Необходимые ресурсы

-   1 ПК с операционной системой по вашему выбору
-   Virtual Box или VMWare
-   Виртуальная машина DEVASC
-   Виртуальная машина CSR1kv

## Инструкции

### Часть 1. Запустите виртуальные машины и проверьте подключение

В этой части вы запускаете две виртуальные машины для прохождения курса и проверяете возможность подключения. Затем вы установите соединение с защищенной оболочкой (SSH).

**Шаг 1. Запустите виртуальные машины**

Если вы еще не завершили **лабораторные работы «Установка виртуальной лабораторной среды»** и **«Установка ВМ CSR1kv»**, сделайте это сейчас. Если вы уже выполнили эти лабораторные работы, запустите виртуальную машину DEVASC и CSR1000v.

**Шаг 2. Проверьте подключение между виртуальными машинами.**

1.  На виртуальной машине CSR1kv нажмите Enter, чтобы получить командную строку, а затем используйте команду show ip interfacerief, чтобы убедиться, что адрес IPv4 - 192.168.56.101. Если адрес другой, запишите его.
1.  Откройте терминал в DEVASC VM.
1.  Проверьте связь с CSR1kv. Вы уже должны были сделать это в лабораторных условиях. Если вы не можете выполнить эхо-запрос, посетите лабораторные работы, перечисленные выше в части 1а.

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

1.  Введите cisco123! в качестве пароля, и теперь вы должны попасть в привилегированную командную строку EXEC для CSR1kv.

    ```
    Password: <cisco123!>

    CSR1kv#
    ```

1.  Оставьте сеанс SSH открытым для следующей части.

### Часть 2. Настройка устройства IOS XE для доступа RESTCONF

В этой части вы настроите виртуальную машину CSR1kv для приема сообщений RESTCONF. Вы также запустите службу HTTPS.

>   **Примечание**: Службы, обсуждаемые в этой части, могут уже работать на вашей виртуальной машине. Однако убедитесь, что вы знаете команды для просмотра запущенных служб и их включения.

**Шаг 1. Убедитесь, что демоны RESTCONF работают.**

RESTCONF уже должен быть запущен, поскольку он был частью конфигурации по умолчанию, предоставленной NetAcad. С терминала вы можете использовать команду **show platform software yang-management process**, чтобы увидеть, все ли демоны, связанные со службой RESTCONF, запущены. Демон NETCONF также может работать, но он не будет использоваться в этой лабораторной работе. Если один или несколько необходимых демонов не запущены, переходите к шагу 2.

Открыть окно конфигурации

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

>   **Примечание**: Назначение и функции всех демонов выходят за рамки этого курса.

**Шаг 2. Включите и проверьте службу RESTCONF.**

1.  Введите команду глобальной конфигурации restconf, чтобы включить службу RESTCONF на CSR1kv.

    ```
    CSR1kv#configure terminal
    CSR1kv(config)# restconf
    ```

1.  Убедитесь, что необходимые демоны RESTCONF запущены. Напомним, что **ncsshd** - это служба NETCONF, которая может работать на вашем устройстве. Для этой лаборатории он нам не нужен. Однако вам нужен **nginx**, который является HTTPS-сервером. Это позволит вам выполнять вызовы REST API к службе RESTCONF.

    ```
    CSR1kv(config)# exit
    CSR1kv# show platform software yang-management process
    confd            : Running
    nesd             : Running
    syncfd           : Running
    ncsshd           : Not Running
    dmiauthd         : Running
    nginx            : Not Running
    ndbmand          : Running
    pubd             : Running
    ```

**Шаг 3. Включите и проверьте службу HTTPS.**

1.  Введите следующие команды глобальной конфигурации, чтобы включить сервер HTTPS и указать, что аутентификация сервера должна использовать локальную базу данных.

    ```
    CSR1kv# configure terminal
    CSR1kv(config)# ip http secure-server
    CSR1kv(config)# ip http authentication local
    ```

1.  Убедитесь, что сервер HTTPS (nginx) теперь работает.

    ```
    CSR1kv(config)# exit
    CSR1kv# show platform software yang-management process
    confd            : Running
    nesd             : Running
    syncfd           : Running
    ncsshd           : Not Running
    dmiauthd         : Running
    nginx            : Running
    ndbmand          : Running
    pubd             : Running
    ```

Закрыть окно конфигурации

### Часть 3. Откройте и настройте Postman

В этой части вы откроете Postman, отключите сертификаты SSL и изучите пользовательский интерфейс.

**Шаг 1. Откройте Postman.**

1.  На виртуальной машине **DEVASC** откройте приложение Postman.
1.  Если вы впервые открываете Почтальон, он может попросить вас создать учетную запись или войти в систему. В нижней части окна вы также можете щелкнуть сообщение «Skip», чтобы пропустить вход. Вход в систему не требуется для используйте это приложение.

**Шаг 2. Отключить проверку сертификата SSL.**

По умолчанию в Postman включена проверка сертификата SSL. Вы не будете использовать сертификаты SSL с CSR1kv; следовательно, вам необходимо отключить эту функцию.

1.  Щелкните **File \> Settings**.
1.  На вкладке **General** установите для **SSL certificate verification** значение **OFF**.
1.  Закройте диалоговое окно **Settings**.

### Часть 4. Используйте почтальон для отправки запросов GET

В этой части вы будете использовать Postman для отправки запроса GET на CSR1kv, чтобы убедиться, что вы можете подключиться к службе RESTCONF.

**Шаг 1. Изучите пользовательский интерфейс Postman.**

1.  В центре вы увидите панель **Launchpad**. Вы можете исследовать эту область, если хотите.
1.  Щелкните знак плюса (+) рядом с вкладкой **Launchpad**, чтобы открыть запрос **GET Untitled Request**. В этом интерфейсе вы будете выполнять всю работу в этой лабораторной работе.

**Шаг 2. Введите URL-адрес CSR1kv.**

1.  Тип запроса уже установлен на GET. Оставьте для типа запроса значение GET.
1.  В поле «Введите URL-адрес запроса» введите URL-адрес, который будет использоваться для доступа к службе RESTCONF, запущенной на CSR1kv:

```
https://192.168.56.101/restconf/
```

**Шаг 3. Введите учетные данные для аутентификации.**

Под полем URL-адреса есть вкладки, перечисленные для **Params**, **Authorization**, **Headers**, **Body**, **Pre-request Script**, **Test** и **Settings**. В этой лабораторной работе вы будете использовать **Authorization**, **Headers** и **Body**.

1.  Щелкните вкладку **Authorization**.
1.  В разделе «Type» щелкните стрелку вниз рядом с «Inherit auth from parent (Наследовать аутентификацию от родителя)» и выберите **Basic Auth**.
1.  В поле **Username** и **Password** введите учетные данные локальной аутентификации для CSR1kv:

    Имя пользователя: **cisco**

    Пароль: **cisco123!**

1.  Щелкните **Headers**. Затем нажмите **7 hidden**. Вы можете проверить, что ключ авторизации имеет базовое значение, которое будет использоваться для аутентификации запроса при его отправке на CSR1kv.

**Шаг 4. Установите JSON в качестве типа данных для отправки и получения от CSR1kv.**

Вы можете отправлять и получать данные из CSR1kv в формате XML или JSON. В этой лабораторной работе вы будете использовать JSON.

1.  В области **Headers** щелкните первое пустое поле **Key** и введите **Content-Type** для типа ключа. В поле **Value** введите **application/yang-data+json**. Это говорит Postman отправлять данные JSON в CSR1kv.
1.  Под ключом **Content-Type** добавьте еще одну пару ключ/значение. Поле **Key** - **Accept**, а поле **Value** - **application/yang-data+json**.

    >   **Примечание**: Вы можете изменить **application/yang-data+json** на **application/yang-data+xml**, чтобы при необходимости отправлять и получать данные XML вместо данных JSON.

**Шаг 5. Отправьте запрос API на CSR1kv.**

Теперь у Postman есть вся информация, необходимая для отправки запроса GET. Щелкните **Send**. Под **Temporary Headers** вы должны увидеть следующий ответ JSON от CSR1kv. Если нет, убедитесь, что вы выполнили предыдущие шаги в этой части лабораторной работы и правильно настроили службы RESTCONF и HTTPS в части 2.

```json
{
    "ietf-restconf:restconf": {
        "data": {},
        "operations": {},
        "yang-library-version": "2016-06-21"
    }
}
```

Этот ответ JSON подтверждает, что Postman теперь может отправлять другие запросы REST API к CSR1kv.

**Шаг 6. Используйте запрос GET для сбора информации для всех интерфейсов CSR1kv.**

1.  Теперь, когда у вас есть успешный запрос GET, вы можете использовать его в качестве шаблона для дополнительных запросов. Вверху Postman, рядом с вкладкой **Launchpad**, щелкните правой кнопкой мыши вкладку **GET**, которую вы только что использовали, и выберите **Duplicate Tab**.
1.  Используйте модель YANG **ietf-interfaces** для сбора информации об интерфейсе. Для URL добавьте **data/ietf-interfaces:interfaces**:

    ```
    https://192.168.56.101/restconf/data/ietf-interfaces:interfaces
    ```

1.  Щелкните **Send**. Вы должны увидеть ответ JSON от CSR1kv, который похож на результат, показанный ниже. Ваш вывод может отличаться в зависимости от вашего конкретного маршрутизатора.

    ```json
    {
    "ietf-interfaces:interfaces": {
        "interface": [
        {
            "name": "GigabitEthernet1",
            "description": "VBox",
            "type": "iana-if-type:ethernetCsmacd",
            "enabled": true,
            "ietf-ip:ipv4": {},
            "ietf-ip:ipv6": {}
        }
        ]
    }
    }
    ```

**Шаг 7. Используйте запрос GET, чтобы собрать информацию для определенного интерфейса на CSR1kv.**

В этой лабораторной работе настраивается только интерфейс GigabitEthernet1. Чтобы указать только этот интерфейс, расширьте URL-адрес, чтобы запрашивать информацию только для этого интерфейса.

1.  Дублируйте ваш последний запрос GET.
1.  Добавьте параметр **interface =**, чтобы указать интерфейс, и введите имя интерфейса.

    ```
    https://192.168.56.101/restconf/data/ietf-interfaces:interfaces/interface=GigabitEthernet1
    ```

>   **Примечание**: Если вы запрашиваете информацию об интерфейсе от другого устройства Cisco с именами, в которых используются косые черты, например GigabitEthernet0/0/1, используйте HTML-код **%2F** для косых черт в имени интерфейса. Итак, **0/0/1** становится **0%2F0%2F1**.

1.  Щелкните **Send**. Вы должны увидеть ответ в формате JSON от CSR1kv, аналогичный приведенному ниже. Ваш вывод может отличаться в зависимости от вашего конкретного маршрутизатора. В настройке CSR1kv по умолчанию вы не увидите информацию об IP-адресации.

    ```json
    {
    "ietf-interfaces:interface": {
        "name": "GigabitEthernet1",
        "description": "VBox",
        "type": "iana-if-type:ethernetCsmacd",
        "enabled": true,
        "ietf-ip:ipv4": {},
        "ietf-ip:ipv6": {}
    }
    }
    ```

1.  Этот интерфейс получает адресацию из шаблона Virtual Box. Поэтому IPv4-адрес не отображается в **show running-config**. Вместо этого вы увидите команду **ip address dhcp**. Вы также можете увидеть это в кратких выходных данных **show ip interface brief**.

    ```
    CSR1kv# show ip interface brief
    Interface              IP-Address      OK? Method Status                Protocol
    GigabitEthernet1       192.168.56.101  YES DHCP   up                    up      
    CSR1kv#
    ```

1.  В следующей части вам нужно будет использовать ответ JSON из интерфейса, настроенного вручную. Откройте командный терминал с CSR1kv и вручную настройте интерфейс GigabitEthernet1 с тем же IPv4-адресом, который в настоящее время назначен ему Virtual Box.

    ```
    CSR1kv# conf t
    CSR1kv(config)# interface g1
    CSR1kv(config-if)# ip address 192.168.56.101 255.255.255.0
    CSR1kv(config-if)# end
    CSR1kv# show ip interface brief
    Interface              IP-Address      OK? Method Status                Protocol
    GigabitEthernet1       192.168.56.101  YES manual up                    up      
    CSR1kv#
    ```

1.  Вернитесь в Postman и снова отправьте запрос GET. Теперь вы должны увидеть информацию об адресации IPv4 в ответе JSON, как показано ниже. В следующей части вы скопируете этот формат JSON, чтобы создать новый интерфейс.

    ```json
    {
    "ietf-interfaces:interface": {
        "name": "GigabitEthernet1",
        "description": "VBox",
        "type": "iana-if-type:ethernetCsmacd",
        "enabled": true,
        "ietf-ip:ipv4": {
        "address": [
            {
            "ip": "192.168.56.101",
            "netmask": "255.255.255.0"
            }
        ]
        },
        "ietf-ip:ipv6": {}
    }
    }
    ```

### Часть 5. Используйте почтальон для отправки запроса PUT

В этой части вы настроите Postman для отправки запроса PUT на CSR1kv для создания нового интерфейса обратной связи.

>   **Примечание**: Если вы создали интерфейс обратной петли в другой лаборатории, либо удалите его сейчас, либо создайте новый, используя другой номер.

**Шаг 1. Скопируйте и измените последний запрос GET.**

1.  Дублируйте последний запрос GET.
1.  Для **Type** запроса щелкните стрелку вниз рядом с **GET** и выберите **PUT**.
1.  Для параметра **interface=** измените его на **=Loopback1**, чтобы указать новый интерфейс.

    ```
    https://192.168.56.101/restconf/data/ietf-interfaces:interfaces/interface=Loopback1
    ```

**Шаг 2. Настройте тело запроса, указав информацию для новой петли.**

1.  Чтобы отправить запрос PUT, вам необходимо предоставить информацию для тела запроса. Рядом с вкладкой **Headers** нажмите **Body**. Затем щелкните переключатель **Raw**. Поле в настоящее время пусто. Если вы нажмете **Send**, вы получите код ошибки **400 Bad Request**, поскольку Loopback1 еще не существует, и вы не предоставили достаточно информации для создания интерфейса.
1.  Заполните раздел **Body** необходимыми данными JSON, чтобы создать новый интерфейс Loopback1. Вы можете скопировать раздел Body из предыдущего запроса GET и изменить его. Или вы можете скопировать следующее в раздел Body вашего запроса PUT. Обратите внимание, что тип интерфейса должен быть установлен на **softwareLoopback**.

    ```json
    {
    "ietf-interfaces:interface": {
        "name": "Loopback1",
        "description": "My first RESTCONF loopback",
        "type": "iana-if-type:softwareLoopback",
        "enabled": true,
        "ietf-ip:ipv4": {
        "address": [
            {
            "ip": "10.1.1.1",
            "netmask": "255.255.255.0"
            }
        ]
        },
        "ietf-ip:ipv6": {}
    }
    }
    ```

1.  Щелкните **Send**, чтобы отправить запрос PUT в CSR1kv. Под разделом Body вы должны увидеть код ответа HTTP **Status: 201 Created**. Это означает, что ресурс был успешно создан.
1.  Вы можете убедиться, что интерфейс был создан. Вернитесь к сеансу SSH с CSR1kv и введите **show ip interface brief**. Вы также можете запустить вкладку Postman, содержащую запрос на получение информации об интерфейсах на CSR1kv, созданный в предыдущей части этой лабораторной работы.

    Открыть окно конфигурации

    ```
    CSR1kv# show ip interface brief
    Interface              IP-Address      OK? Method Status                Protocol
    GigabitEthernet1       192.168.56.101  YES manual up                    up      
    Loopback1              10.1.1.1        YES other  up                    up      
    CSR1kv#
    ```

Закрыть окно конфигурации

### Часть 6. Используйте скрипт Python для отправки запросов GET

В этой части вы создадите сценарий Python для отправки запросов GET к CSR1kv.

**Шаг 1. Создайте каталог RESTCONF и запустите сценарий.**

1.  Открытый код VS. Затем щелкните **File \> Open** **Folder...** и перейдите в каталог **devnet-src**. Щелкните ОК.
1.  Откройте окно терминала в VS Code: **Terminal \> New Terminal**.
1.  Создайте подкаталог с именем **restconf** в каталоге **/devnet-src**.

    ```
    devasc@labvm:~/labs/devnet-src$ mkdir restconf
    devasc@labvm:~/labs/devnet-src$
    ```

1.  На панели **EXPLORER** под **DEVNET-SRC** щелкните правой кнопкой мыши каталог **restconf** и выберите **New File**.
1.  Назовите файл **restconf-get.py**.
1.  Введите следующие команды, чтобы импортировать необходимые модули и отключить предупреждения сертификата SSL:

    ```python
    import json
    import requests
    requests.packages.urllib3.disable_warnings()
    ```

    Модуль **json** включает методы для преобразования данных JSON в объекты Python и наоборот. В модуле **requests** есть методы, которые позволят вам отправлять запросы REST по URL-адресу.

**Шаг 2. Создайте переменные, которые будут составляющими запроса.**

1.  Создайте переменную с именем **api_url** и назначьте ей URL-адрес, который будет обращаться к информации об интерфейсе на CSR1kv.

    ```python
    api_url = "https://192.168.56.101/restconf/data/ietf-interfaces:interfaces"
    ```

1.  Создайте словарную переменную с именем **headers**, которая имеет ключи для **Accept** и **Content-type**, и присвойте этим ключам значение **application/yang-data+json**.

    ```python
    headers = { "Accept": "application/yang-data+json", 
                "Content-type":"application/yang-data+json"
            }
    ```

1.  Создайте переменную кортежа Python с именем **basicauth**, у которой есть два ключа, необходимые для аутентификации, **username** и **password**.

    ```python
    basicauth = ("cisco", "cisco123!")
    ```

**Шаг 3. Создайте переменную для отправки запроса и сохраните ответ JSON.**

Используйте переменные, которые были созданы на предыдущем шаге, в качестве параметров для метода **requests.get()**. Этот метод отправляет HTTP-запрос GET в API RESTCONF на CSR1kv. Назначьте результат запроса переменной с именем **resp**. Эта переменная будет содержать ответ JSON от API. Если запрос будет успешным, JSON будет содержать возвращенную модель данных YANG.

1.  Введите следующее заявление:

    ```
    resp = requests.get(api_url, auth=basicauth, headers=headers, verify=False)
    ```

    В таблице ниже перечислены различные элементы этого утверждения:

    | **Элемент**     | **Объяснение**                                                       |
    |-----------------|----------------------------------------------------------------------|
    | resp            | Переменная для хранения ответа от API                                |
    | requests.get()  | Метод, который фактически делает запрос GET                          |
    | api_url         | Переменная, содержащая строку URL-адреса                             |
    | auth            | Переменная кортежа, созданная для хранения информации аутентификации |
    | headers=headers | Параметр, которому присвоена переменная заголовков                   |
    | verify=False    | Отключает проверку сертификата SSL при запросе                       |

1.  Чтобы увидеть код ответа HTTP, добавьте оператор печати.

    ```python
    print(resp)
    ```

1.  Сохраните и запустите ваш скрипт. Вы должны получить результат, показанный ниже. Если нет, проверьте все предыдущие шаги в этой части, а также конфигурацию SSH и RESTCONF для CSR1kv.

    ```
    devasc@labvm:~/labs/devnet-src$ cd restconf/
    devasc@labvm:~/labs/devnet-src/restconf$ python3 restconf-get.py 
    <Response [200]>
    devasc@labvm:~/labs/devnet-src/restconf$
    ```

**Шаг 4. Отформатируйте и отобразите данные JSON, полученные от CSR1kv.**

Теперь вы можете извлечь значения ответа модели YANG из ответа JSON.

1.  Ответ JSON несовместим со словарем Python и объектами списка, поэтому его необходимо преобразовать в формат Python. Создайте новую переменную с именем **response_json** и назначьте ей переменную **resp**. Добавьте метод **json()** для преобразования JSON. Заявление выглядит следующим образом:

    ```python
    response_json = resp.json()
    ```

1.  Добавьте оператор печати для отображения данных JSON.

    ```python
    print(response_json)
    ```

1.  Сохраните и запустите ваш скрипт. Вы должны получить следующий результат:

    ```
    devasc@labvm:~/labs/devnet-src/restconf$ python3 restconf-get.py 
    <Response [200]>
    {'ietf-interfaces:interfaces': {'interface': [{'name': 'GigabitEthernet1', 'description': 'VBox', 'type': 'iana-if-type:ethernetCsmacd', 'enabled': True, 'ietf-ip:ipv4': {'address': [{'ip': '192.168.56.101', 'netmask': '255.255.255.0'}]}, 'ietf-ip:ipv6': {}}, {'name': 'Loopback1', 'description': 'My first RESTCONF loopback', 'type': 'iana-if-type:softwareLoopback', 'enabled': True, 'ietf-ip:ipv4': {'address': [{'ip': '10.1.1.1', 'netmask': '255.255.255.0'}]}, 'ietf-ip:ipv6': {}}]}}
    devasc@labvm:~/labs/devnet-src/restconf$
    ```

1.  Чтобы сделать вывод более красивым, отредактируйте оператор печати, чтобы использовать функцию **json.dumps()** с параметром «indent»:

    ```python
    print(json.dumps(response_json, indent=4))
    ```

1.  Сохраните и запустите ваш скрипт. Вы должны получить результат, показанный ниже. Этот вывод практически идентичен выводу вашего первого запроса GET Postman.

    ```python
    devasc@labvm:~/labs/devnet-src/restconf$ python3 restconf-get.py 
    <Response [200]>
    {
        "ietf-interfaces:interfaces": {
            "interface": [
                {
                    "name": "GigabitEthernet1",
                    "description": "VBox",
                    "type": "iana-if-type:ethernetCsmacd",
                    "enabled": true,
                    "ietf-ip:ipv4": {
                        "address": [
                            {
                                "ip": "192.168.56.101",
                                "netmask": "255.255.255.0"
                            }
                        ]
                    },
                    "ietf-ip:ipv6": {}
                },
                {
                    "name": "Loopback1",
                    "description": "My first RESTCONF loopback",
                    "type": "iana-if-type:softwareLoopback",
                    "enabled": true,
                    "ietf-ip:ipv4": {
                        "address": [
                            {
                                "ip": "10.1.1.1",
                                "netmask": "255.255.255.0"
                            }
                        ]
                    },
                    "ietf-ip:ipv6": {}
                }
            ]
        }
    }
    devasc@labvm:~/labs/devnet-src/restconf$
    ```

### Часть 7. Используйте скрипт Python для отправки запроса PUT

В этой части вы создадите скрипт Python для отправки запроса PUT в CSR1kv. Как это было сделано в Postman, вы создадите новый интерфейс обратной петли.

**Шаг 1. Импортируйте модули и отключите предупреждения SSL.**

1.  На панели **EXPLORER** под **DEVNET-SRC** щелкните правой кнопкой мыши каталог restconf и выберите **New File**.
1.  Назовите файл **restconf-put.py**.
1.  Введите следующие команды, чтобы импортировать необходимые модули и отключить предупреждения сертификата SSL:

    ```python
    import json
    import requests
    requests.packages.urllib3.disable_warnings()
    ```

**Шаг 2. Создайте переменные, которые будут составляющими запроса.**

1.  Создайте переменную с именем **api_url** и назначьте ей URL-адрес, предназначенный для нового интерфейса Loopback2.

    >   **Примечание**: Эта спецификация переменной должна быть в одной строке вашего скрипта.

    ```python
    api_url = "https://192.168.56.101/restconf/data/ietf-interfaces:interfaces/interface=Loopback2"
    ```

1.  Создайте словарную переменную с именем **headers**, которая имеет ключи для **Accept** и **Content-type**, и присвойте этим ключам значение **application/yang-data+json**.

    ```python
    headers = { "Accept": "application/yang-data+json", 
                "Content-type":"application/yang-data+json"
            }
    ```

1.  Создайте переменную кортежа Python с именем **basicauth**, которая имеет два значения, необходимые для аутентификации: имя пользователя и пароль.

    ```python
    basicauth = ("cisco", "cisco123!")
    ```

1.  Создайте переменную словаря Python **yangConfig**, которая будет содержать данные YANG, необходимые для создания нового интерфейса Loopback2. Вы можете использовать тот же словарь, который вы использовали ранее в Postman. Однако измените номер и адрес интерфейса. Также имейте в виду, что логические значения в Python должны начинаться с заглавной буквы. Поэтому убедитесь, что буква **T** написана с заглавной буквы в паре ключ/значение для **«включено»: True**.

    ```python
    yangConfig = {
        "ietf-interfaces:interface": {
            "name": "Loopback2",
            "description": "My second RESTCONF loopback",
            "type": "iana-if-type:softwareLoopback",
            "enabled": True,
            "ietf-ip:ipv4": {
                "address": [
                    {
                        "ip": "10.2.1.1",
                        "netmask": "255.255.255.0"
                    }
                ]
            },
            "ietf-ip:ipv6": {}
        }
    }
    ```

**Шаг 3. Создайте переменную для отправки запроса и сохраните ответ JSON.**

Используйте переменные, созданные на предыдущем шаге, как параметры для метода **requests.put()**. Этот метод отправляет HTTP-запрос PUT в RESTCONF API. Назначьте результат запроса переменной с именем **resp**. Эта переменная будет содержать ответ JSON от API. Если запрос будет успешным, JSON будет содержать возвращенную модель данных YANG.

1.  Перед вводом операторов обратите внимание, что эта спецификация переменной должна быть только в одной строке вашего скрипта. Введите следующие утверждения:

    >   **Примечание**: Эта спецификация переменной должна быть в одной строке вашего скрипта.

    ```python
    resp = requests.put(api_url, data=json.dumps(yangConfig), auth=basicauth, headers=headers, verify=False)
    ```

1.  Введите приведенный ниже код, чтобы обработать ответ. Если ответом является одно из сообщений об успешном выполнении HTTP, будет напечатано первое сообщение. Любое другое значение кода считается ошибкой. Код ответа и сообщение об ошибке будут напечатаны в случае обнаружения ошибки.

    ```python
    if(resp.status_code >= 200 and resp.status_code <= 299):
        print("STATUS OK: {}".format(resp.status_code))
    else:
        print('Error. Status Code: {} \nError message: {}'.format(resp.status_code,resp.json()))
    ```

    В таблице ниже перечислены различные элементы этих утверждений:

    | **Элемент**      | **Объяснение**                                                         |
    |------------------|------------------------------------------------------------------------|
    | resp             | Переменная для хранения ответа от API.                                 |
    | requests.put()   | Метод, выполняющий запрос PUT.                                         |
    | api_url          | Переменная, содержащая строку URL-адреса.                              |
    | data             | Данные, которые будут отправлены в конечную точку API, в формате JSON. |
    | auth             | Переменная кортежа, созданная для хранения информации аутентификации.  |
    | headers=headers  | Параметр, которому присвоена переменная заголовков.                    |
    | verify=False     | Параметр, отключающий проверку сертификата SSL при выполнении запроса. |
    | resp.status_code | Код состояния HTTP в ответе на запрос API PUT.                         |

1.  Сохраните и запустите сценарий, чтобы отправить запрос PUT в CSR1kv. Вы должны получить сообщение **201 Status Created**. Если нет, проверьте свой код и конфигурацию CSR1kv.
1.  Вы можете убедиться, что интерфейс был создан, введя команду **show ip interface brief** на CSR1kv.

    Открыть окно конфигурации

    ```
    CSR1kv# show ip interface brief
    Interface              IP-Address      OK? Method Status                Protocol
    GigabitEthernet1       192.168.56.101  YES manual up                    up      
    Loopback1              10.1.1.1        YES other  up                    up      
    Loopback2              10.2.1.1        YES other  up                    up      
    CSR1kv#
    ```

    Закрыть окно конфигурации

## Программы, используемые в этой лабораторной работе

В этой лабораторной работе использовались следующие скрипты Python:

```python
#===================================================================
#resconf-get.py
import json
import requests
requests.packages.urllib3.disable_warnings()

api_url = "https://192.168.56.101/restconf/data/ietf-interfaces:interfaces"

headers = { "Accept": "application/yang-data+json", 
            "Content-type":"application/yang-data+json"
           }

basicauth = ("cisco", "cisco123!")

resp = requests.get(api_url, auth=basicauth, headers=headers, verify=False)

print(resp)

response_json = resp.json()
print(json.dumps(response_json, indent=4))

#end of file

#===================================================================
#resconf-put.py
import json
import requests
requests.packages.urllib3.disable_warnings()

api_url = "https://192.168.56.101/restconf/data/ietf-interfaces:interfaces/interface=Loopback2"

headers = { "Accept": "application/yang-data+json", 
            "Content-type":"application/yang-data+json"
           }

basicauth = ("cisco", "cisco123!")

yangConfig = {
    "ietf-interfaces:interface": {
        "name": "Loopback2",
        "description": "My second RESTCONF loopback",
        "type": "iana-if-type:softwareLoopback",
        "enabled": True,
        "ietf-ip:ipv4": {
            "address": [
                {
                    "ip": "10.2.1.1",
                    "netmask": "255.255.255.0"
                }
            ]
        },
        "ietf-ip:ipv6": {}
    }
}

resp = requests.put(api_url, data=json.dumps(yangConfig), auth=basicauth, headers=headers, verify=False)

if(resp.status_code >= 200 and resp.status_code <= 299):
    print("STATUS OK: {}".format(resp.status_code))
else:
    print('Error. Status Code: {} \nError message: {}'.format(resp.status_code,resp.json()))

#end of file
```
