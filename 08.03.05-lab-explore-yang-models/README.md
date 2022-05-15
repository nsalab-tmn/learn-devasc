## Цели

-   Часть 1. Запуск виртуальной машины DEVASC
-   Часть 2: Изучите модель YANG на GitHub
-   Часть 3: Изучение модели YANG с помощью pyang

## Предпосылки / Сценарий

Модели YANG определяют точную структуру, типы данных, синтаксис и правила проверки содержания сообщений, которыми обмениваются управляемое устройство и другая система, взаимодействующая с устройством. Работа с файлами на языке YANG может быть немного сложной из-за уровня детализации этих файлов.

В этой лабораторной работе вы узнаете, как использовать инструмент **pyang** с открытым исходным кодом для преобразования моделей данных YANG из файлов с использованием языка YANG в гораздо более удобный для чтения формат. Используя преобразование «древовидного» представления, вы определите ключевые элементы модели YANG ietf-интерфейсов.

## Необходимые ресурсы

-   1 ПК с операционной системой по вашему выбору
-   Virtual Box или VMWare
-   Виртуальная машина DEVASC

## Инструкции

### Часть 1. Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC сейчас.

### Часть 2. Изучите модель YANG на GitHub

В этой части вы установите модуль pyang в виртуальную машину DEVASC и изучите, как он преобразует файлы YANG. Pyang упрощает работу с файлами YANG. Модуль поставляется с исполняемым файлом командной строки pyang, который преобразует файлы YANG в более удобный для чтения формат.

**Шаг 1. Изучите модели Cisco IOS XE YANG в репозитории GitHub.**

1.  Откройте Chromium и перейдите по адресу https://github.com/YangModels/yang.
1.  В основной ветви перейдите к моделям YANG для Cisco IOS XE версии 16.9.3, щелкнув следующие каталоги: **vendor\> cisco\> xe\> 1693**.
1.  Прокрутите вниз под всеми моделями Cisco YANG и найдите, где начинаются модели IETF. Ищите **ietf-interfaces.yang**.
1.  Щелкните **ietf-interfaces.yang** и просмотрите все узлы контейнера, листовые узлы и узлы списка. Если вы знакомы с выводом команды show interfaces в IOS, то вам следует распознать некоторые или все узлы. Например, около строки 221 вы увидите, что лист включен.

    ```yang
        leaf enabled {
            type boolean;
            default "true";
            description
            "This leaf contains the configured, desired state of the
            interface.
            Systems that implement the IF-MIB use the value of this
            leaf in the 'running' datastore to set
            IF-MIB.ifAdminStatus to 'up' or 'down' after an ifEntry
            has been initialized, as described in RFC 2863.
            Changes in this leaf in the 'running' datastore are
            reflected in ifAdminStatus, but if ifAdminStatus is
            changed over SNMP, this leaf is not affected.";
            reference
            "RFC 2863: The Interfaces Group MIB - ifAdminStatus";
        }
    ```

**Шаг 2. Скопируйте модель ietf-interfaces.yang в папку на вашей виртуальной машине.**

1.  Открытый код VS.
1.  Щелкните **File \> Open** **Folder...** и перейдите в каталог **devnet-src**.
1.  Щелкните **ОК**.
1.  Откройте окно терминала в VS Code: **Terminal\> New Terminal**.
2.  Создайте подкаталог с именем **pyang** в каталоге **/devnet-src**.

    ```
    devasc@labvm:~/labs/devnet-src$ mkdir pyang
    devasc@labvm:~/labs/devnet-src$
    ```

1.  Вернитесь на вкладку Chromium, где модель **ietf-interfaces.yang** все еще открыта. При необходимости прокрутите назад вверх и нажмите Raw, чтобы отобразить только данные модели YANG.
1.  Выберите и скопируйте URL-адрес.
1.  В терминале перейдите в папку pyang.
1.  Используйте **wget**, чтобы сохранить необработанный файл **ietf-interfaces.yang**.

    ```
    devasc@labvm:~/labs/devnet-src/pyang$ wget https://raw.githubusercontent.com/YangModels/yang/master/vendor/cisco/xe/1693/ietf-interfaces.yang
    --2020-06-22 20:42:20--  https://raw.githubusercontent.com/YangModels/yang/master/vendor/cisco/xe/1693/ietf-interfaces.yang
    Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 151.101.0.133, 151.101.192.133, 151.101.128.133, ...
    Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|151.101.0.133|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 24248 (24K) [text/plain]
    Saving to: ‘ietf-interfaces.yang’

    ietf-interfac 100%  23.68K  --.-KB/s    in 0.05s    

    2020-06-22 20:42:21 (439 KB/s) - ‘ietf-interfaces.yang’ saved [24248/24248]

    devasc@labvm:~/labs/devnet-src/pyang$
    ```

Теперь у вас есть локальная версия модели **ietf-interfaces.yang**, которой вы можете управлять с помощью **pyang**.

### Часть 3. Изучите модель YANG с помощью pyang

В этой части вы установите модуль **pyang** в свою виртуальную машину DEVASC и исследуете, как он преобразует модель YANG, которую вы скопировали с GitHub. Pyang упрощает работу с файлами YANG. Модуль поставляется с исполняемым файлом командной строки **pyang**, который преобразует файлы YANG в более удобный для чтения формат.

**Шаг 1. Убедитесь, что pyang установлен и обновлен.**

1.  В VS Code откройте окно терминала.
1.  Убедитесь, что pyang уже установлен с помощью команды **pyang -v**. Номер вашей версии может отличаться от указанного здесь. Вы также можете

    ```
    devasc@labvm:~/labs/devnet-src$ pyang -v
    pyang 2.2.1
    devasc@labvm:~/labs/devnet-src$
    ```

1.  (Необязательно) Вы можете убедиться, что у вас есть последние обновления pyang, используя следующую команду **pip3**. Все обновления после написания этой лабораторной работы будут загружены и установлены.

    ```
    devasc@labvm:~/labs/devnet-src$ pip3 install pyang --upgrade
    Requirement already up-to-date: pyang in ./.local/lib/python3.8/site-packages (2.2.1)
    Requirement already satisfied, skipping upgrade: lxml in ./.local/lib/python3.8/site-packages (from pyang) (4.5.0)
    devasc@labvm:~/labs/devnet-src$
    ```

**Шаг 2. Преобразуйте модель ietf-interfaces.yang.**

1.  Перейдите в каталог **pyang**.

    ```
    devasc@labvm:~/labs/devnet-src$ cd pyang
    devasc@labvm:~/labs/devnet-src/pyang$
    ```

1.  Введите **pyang -h \| more**, чтобы изучить варианты преобразования модели YANG. Найдите параметр -f, как показано ниже. Вы будете использовать вариант форматирования **дерева**.

    ```
    devasc@labvm:~/labs/devnet-src/pyang$ pyang -h | more
    Usage: pyang [options] [<filename>...]

    Validates the YANG module in <filename> (or stdin), and all its dependencies.

    Options:
    -h, --help            Show this help message and exit
    -v, --version         Show version number and exit
    <output omitted>
    -f FORMAT, --format=FORMAT
                            Convert to FORMAT.  Supported formats are: yang, yin,
                            dsdl, jstree, jsonxsl, capability, identifiers, jtox,
                            uml, name, omni, tree, depend, sample-xml-skeleton
    <output omitted>
    devasc@labvm:~/labs/devnet-src/pyang$
    ```

1.  Преобразуйте модель **ietf-interfaces.yang** в формат дерева с помощью следующей команды. Обратите внимание, что **leaf enabled** намного легче найти и прочитать в этом формате.

    ```
    devasc@labvm:~/labs/devnet-src/pyang$ pyang -f tree ietf-interfaces.yang 
    ietf-interfaces.yang:6: error: module "ietf-yang-types" not found in search path
    module: ietf-interfaces
    +--rw interfaces
    |  +--rw interface* [name]
    |     +--rw name                        string
    |     +--rw description?                string
    |     +--rw type                        identityref
    |     +--rw enabled?                    boolean
    |     +--rw link-up-down-trap-enable?   enumeration {if-mib}?
    +--ro interfaces-state
        +--ro interface* [name]
            +--ro name               string
            +--ro type               identityref
            +--ro admin-status       enumeration {if-mib}?
            +--ro oper-status        enumeration
            +--ro last-change?       yang:date-and-time
            +--ro if-index           int32 {if-mib}?
            +--ro phys-address?      yang:phys-address
            +--ro higher-layer-if*   interface-state-ref
            +--ro lower-layer-if*    interface-state-ref
            +--ro speed?             yang:gauge64
            +--ro statistics
            +--ro discontinuity-time    yang:date-and-time
            +--ro in-octets?            yang:counter64
            +--ro in-unicast-pkts?      yang:counter64
            +--ro in-broadcast-pkts?    yang:counter64
            +--ro in-multicast-pkts?    yang:counter64
            +--ro in-discards?          yang:counter32
            +--ro in-errors?            yang:counter32
            +--ro in-unknown-protos?    yang:counter32
            +--ro out-octets?           yang:counter64
            +--ro out-unicast-pkts?     yang:counter64
            +--ro out-broadcast-pkts?   yang:counter64
            +--ro out-multicast-pkts?   yang:counter64
            +--ro out-discards?         yang:counter32
            +--ro out-errors?           yang:counter32
    devasc@labvm:~/labs/devnet-src/pyang$
    ```
