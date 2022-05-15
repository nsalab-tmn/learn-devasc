## Цели

-   **Часть 1. Запуск виртуальной машины DEVASC и CSR1000v**
-   **Часть 2: Настройка Ansible**
-   **Часть 3: Использование Ansible для резервного копирования конфигурации**
-   **Часть 4: Использование Ansible для настройки устройства**

## Предпосылки / Сценарий

В этой лабораторной работе вы изучите основы использования Ansible для автоматизации некоторых основных задач управления устройствами. Сначала вы настроите Ansible на своей виртуальной машине DEVASC. Затем вы будете использовать Ansible для подключения к CSR1000v и резервного копирования его конфигурации. Наконец, вы настроите CSR1000v с адресацией IPv6.

## Необходимые ресурсы

-   1 ПК с операционной системой по вашему выбору
-   Virtual Box или VMWare
-   Виртуальная машина DEVASC
-   Виртуальная машина CSR1000v

## Инструкции

### Часть 1. Запустите виртуальную машину DEVASC и CSR1000v.

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC сейчас.

Если вы еще не завершили **лабораторную работу «Установка ВМ CSR1000v»,** сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину CSR1000v сейчас.

### Часть 2. Настроить Ansible

В этой части вы настроите Ansible для запуска из определенного каталога.

**Шаг 1. Откройте каталог Ansible в VS Code.**

1.  Откройте **VS Code**.
1.  Щелкните **File \> Open Folder...** и перейдите в папку **/labs/devnet-src/ansible**.
1.  Щелкните **ОК**.
1.  Два подкаталога для лабораторий Ansible теперь загружены на панель VS Code **EXPLORER** для вашего удобства. В этой лабораторной работе вы будете работать с каталогом **ansible-csr1000v**.

**Шаг 2. Отредактируйте файл инвентаря Ansible.**

Ansible использует файл инвентаризации под названием **hosts**, который содержит информацию об устройстве, используемую Ansible playbooks. Расположение по умолчанию для файла инвентаризации Ansible - **/etc/ansible/hosts**, как указано в файле **ansible.cfg** по умолчанию в том же каталоге **/etc/ansible**. Эти файлы по умолчанию используются при глобальном запуске Ansible. Однако в этой лабораторной работе вы будете запускать Ansible из каталога **ansible-csr1000v**. Поэтому для каждой лаборатории нужны отдельные файлы **hosts** и **ansible.cfg**.

>   **Примечание**: Термины файл **hosts** и файл **inventory** являются синонимами и будут использоваться как синонимы в лабораторных работах Ansible.

Файл инвентаризации Ansible определяет устройства и группы устройств, которые используются в плейбуке Ansible. Файл может быть в одном из многих форматов, включая YAML и INI, в зависимости от вашей среды Ansible. В файле инвентаризации могут быть перечислены устройства по IP-адресу или полному доменному имени (FQDN), а также могут быть указаны параметры хоста.

1.  Откройте файл **hosts** в каталоге **ansible-csr1000v**.
1.  Добавьте следующие строки в файл hosts и сохраните.

    ```
    # Enter the hosts or devices for Ansible playbooks
    CSR1kv ansible_user=cisco ansible_password=cisco123! ansible_host=192.168.56.101
    ```

    После комментария (\#) файл **hosts** начинается с псевдонима **CSR1kv**. Псевдоним используется из книги воспроизведения Ansible для ссылки на устройство. После псевдонима в файле **hosts** указываются три переменные, которые будут использоваться Ansible playbook для доступа к устройству. Это учетные данные SSH, необходимые Ansible для безопасного доступа к виртуальной машине CSR1000v.

    -   **ansible_user**- это переменная, содержащая имя пользователя, используемое для подключения к удаленному устройству. Без этого будет использоваться пользователь, который запускает ansible-playbook.
    -   **ansible_password**- это переменная, содержащая соответствующий пароль для ansible_user. Если не указан, будет использоваться ключ SSH.
    -   **ansible_host** - переменная, содержащая IP-адрес или полное доменное имя устройства.

**Шаг 3. Отобразите версию Ansible и расположение по умолчанию ansible.cfg.**

1.  Чтобы увидеть, где Ansible хранит файл ansible.cfg по умолчанию, откройте окно терминала и перейдите на один каталог вверх к родительскому каталогу ansible.

    ```
    devasc@labvm:~/labs/devnet-src/ansible/ansible-csr1000v$ cd ..
    devasc@labvm:~/labs/devnet-src/ansible$
    ```

1.  Введите **ansible**, чтобы получить список доступных команд. Обратите внимание на параметр   
    **--version**.

    ```
    devasc@labvm:~/labs/devnet-src/ansible$ ansible
    usage: ansible [-h] [--version] [-v] [-b] [--become-method BECOME_METHOD]
                [--become-user BECOME_USER] [-K] [-i INVENTORY] [--list-hosts]
                [-l SUBSET] [-P POLL_INTERVAL] [-B SECONDS] [-o] [-t TREE] [-k]
                [--private-key PRIVATE_KEY_FILE] [-u REMOTE_USER]
                [-c CONNECTION] [-T TIMEOUT]
                [--ssh-common-args SSH_COMMON_ARGS]
    <output omitted>
    devasc@labvm:~/labs/devnet-src/ansible$
    ```

1.  Используйте команду **ansible --version** для отображения информации о версии. Обратите внимание, что в этой лабораторной работе используется версия 2.9.6. Ansible включает определенные файлы по умолчанию, в том числе файл конфигурации по умолчанию **ansible.cfg**.

    ```
    devasc@labvm:~/labs/devnet-src/ansible$ ansible --version
    ansible 2.9.6
    config file = /etc/ansible/ansible.cfg
    configured module search path = ['/home/devasc/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
    ansible python module location = /usr/lib/python3/dist-packages/ansible
    executable location = /usr/bin/ansible
    python version = 3.8.2 (default, Apr 27 2020, 15:53:34) [GCC 9.3.0]
    devasc@labvm:~/labs/devnet-src/ansible$
    ```

**Шаг 4. Отобразите файл ansible.cfg по умолчанию.**

Файл **ansible.cfg** используется Ansible для установки определенных значений по умолчанию. Эти значения можно изменить.

Используя путь по умолчанию, отображаемый в команде **ansible --version**, отобразите файл конфигурации по умолчанию. Обратите внимание, это очень длинный файл. Вы можете передать вывод команды **cat** по конвейеру **more**, чтобы отображалась одна страница за раз. Выделены записи, которые будут в вашем файле **ansible.cfg** для этой лабораторной работы.

```
devasc@labvm:~/labs/devnet-src/ansible$ cat /etc/ansible/ansible.cfg | more
# config file for ansible -- https://ansible.com/
# ===============================================

# nearly all parameters can be overridden in ansible-playbook
# or with command line flags. ansible will read ANSIBLE_CONFIG,
# ansible.cfg in the current working directory, .ansible.cfg in
# the home directory or /etc/ansible/ansible.cfg, whichever it
# finds first

[defaults]

# some basic default values...

#inventory      = /etc/ansible/hosts
<output omitted>

# uncomment this to disable SSH key host checking
#host_key_checking = False
<output omitted>

# retry files
# When a playbook fails a .retry file can be created that will be placed in ~/
# You can enable this feature by setting retry_files_enabled to True
# and you can change the location of the files by setting retry_files_save_path

#retry_files_enabled = False
<output omitted>
```

Обратите внимание, что Ansible показывает, что файл **hosts** инвентаризации, который он будет использовать по умолчанию, - это **/etc/ansible/hosts**. На предыдущем шаге вы отредактировали файл инвентарных **hosts** в каталоге **ansible-csr1000v directory**. На следующем шаге вы отредактируете новый файл **ansible.cfg**, который использует созданный вами файл инвентаризации **hosts**.

**Шаг 5. Измените расположение файла ansible.cfg.**

1.  Ansible будет использовать файл конфигурации, расположенный в **/etc/ansible/ansible.cfg**, если в текущем каталоге нет файла **ansible.cfg**. Вернитесь в каталог **ansible-csr1000v**. В этом каталоге уже есть файл-заполнитель **ansible.cfg**. Отобразите текущее расположение **ansible.cfg** с помощью команды **ansible --version**.

    ```
    devasc@labvm:~/labs/devnet-src/ansible$ cd ansible-csr1000v/
    devasc@labvm:~/labs/devnet-src/ansible/ansible-csr1000v$ ansible --version
    ansible 2.9.6
    config file = /home/devasc/labs/devnet-src/ansible/ansible-csr1000v/ansible.cfg
    <output omitted>
    devasc@labvm:~/labs/devnet-src/ansible/ansible-csr1000v$
    ```

1.  Отобразите файл, чтобы убедиться, что он пуст, за исключением комментария. Вы отредактируете этот файл на следующем шаге.

    ```
    devasc@labvm:~/labs/devnet-src/ansible/ansible-csr1000v$ cat ansible.cfg 
    # Add to this file for the Ansible lab
    devasc@labvm:~/labs/devnet-src/ansible/ansible-csr1000v$
    ```

**Шаг 6. Отредактируйте файл ansible.cfg.**

Теперь вам нужно отредактировать файл **/ansible-csr1000v/ansible.cfg**, включив в него расположение файла инвентаризации **hosts**. Помните, что конфигурационный файл по умолчанию в **/etc/ansible/ansible.cfg** использует файл инвентаризации в /etc/ansible/hosts.

1.  Откройте файл **/ansible-csr1000v/ansible.cfg** в VS Code.
1.  Вы можете удалить комментарий. Добавьте в файл следующие строки и сохраните его.

    ```
    # config file for ansible-csr1000v
    [defaults]
    # Use local hosts file in this folder
    inventory=./hosts 
    host_key_checking = False # Don't worry about RSA Fingerprints
    retry_files_enabled = False # Do not create them
    deprecation_warnings = False # Do not show warnings
    ```

    Как и Python, символ \# используется для комментариев в файле **ansible.cfg**. Если запись относится к имени файла, например **inventory=./hosts**, комментарий не может идти после записи. Ansible рассматривает \# и следующий за ним комментарий как часть имени файла. Поэтому в этих случаях комментарий \# должен быть на отдельной строке. Однако переменные могут иметь комментарий в той же строке, что и для **host_key_checking** и **retry_files_enabled**.

    Файл **ansible.cfg** сообщает Ansible, где найти файл инвентаризации, и устанавливает определенные параметры по умолчанию. Информация, которую вы ввели в свой файл **ansible.cfg**:

    -   `inventory=./hosts` - Ваш инвентарный файл - это файл **hosts** в текущем каталоге.
    -   `host_key_checking = False` - В локальной среде разработки не настроены ключи SSH. Вы установили для **host_key_checking** значение **False**, которое используется по умолчанию. В производственной сети для **host_key_checking** будет установлено значение **True**.
    -   `retry_files_enabled = False` - имя хоста в файл в текущем каталоге с расширением **.retry**. Чтобы избежать беспорядка, этот параметр обычно отключают.
    -   `deprecation_warnings=False` - Предупреждения об устаревании указывают на использование устаревших функций, которые планируется удалить в будущих выпусках Ansible. Вы отключили это предупреждение.

**Шаг 7. РЕЗЮМЕ: ваши файлы конфигурации Ansible.**

В этой части вы настроили Ansible для работы в каталоге **ansible-csr1000v**. По умолчанию Ansible использует файлы в каталоге **/etc/ansible**. Файл по умолчанию **/etc/ansible/ansible.cfg** указывает, что файл инвентаризации по умолчанию - **/etc/ansible/hosts**.

Однако для этой лабораторной работы вам понадобятся файлы **hosts** и **ansible.cfg** в каталоге **ansible-csr1000v**.

-   Вы отредактировали файл **hosts**, чтобы он содержал информацию о логине и IP-адресе для маршрутизатора CSR1000v.
-   Вы отредактировали файл **ansible.cfg**, чтобы использовать файл локальных хостов в качестве файла инвентаризации (**inventory=./hosts**).

    В следующей части вы создадите учебник, чтобы сообщить Ansible, что делать.

### Часть 3. Используйте Ansible для резервного копирования конфигурации

В этой части вы создадите сценарий Ansible, который автоматизирует процесс резервного копирования конфигурации CSR1000v. В основе Ansible - учебники. Если вы хотите, чтобы Ansible получил информацию или выполнил действие на устройстве или группе устройств, вы запускаете playbook, чтобы выполнить задание.

Плейбук Ansible - это файл YAML, содержащий одно или несколько воспроизведений. Каждый спектакль - это набор заданий.

-   **Play** - это набор задач, соответствующих устройству или группе устройств.
-   **Task** - это отдельное действие, которое ссылается на **модуль** для запуска вместе с любыми входными аргументами и действиями. Эти задачи могут быть простыми или сложными в зависимости от необходимости в разрешениях, порядка выполнения задач и т. Д.

**Плейбук (playbook)** также может содержать роли. **Роль (role)** - это механизм, позволяющий разбить книгу воспроизведения на несколько компонентов или файлов, упрощая ее и упрощая ее повторное использование. Например, роль **common** используется для хранения задач, которые можно использовать во всех ваших сценариях. Роли выходят за рамки этой лабораторной работы.

Плейбук Ansible YAML включает **объекты**, **списки** и **модули**.

-   Объект YAML - это одна или несколько пар ключ-значение. Пары ключ-значение разделяются двоеточием без использования кавычек, например, **hosts: CSR1kv**.
-   Объект может содержать другие объекты, например, список. YAML использует списки или массивы. Значок «-» используется для каждого элемента в списке.
-   Ansible поставляется с рядом модулей (называемых библиотекой модулей), которые могут выполняться непосредственно на удаленных хостах или через playbook. Примером может служить модуль **ios_command**, используемый для отправки команд на устройство IOS и возврата результатов. Каждая задача обычно состоит из одного или нескольких модулей Ansible.

Вы запускаете Ansible playbook с помощью команды **ansible-playbook**, например:

```
ansible-playbook backup_cisco_router_playbook.yaml -i hosts
```

Команда **ansible-playbook** использует параметры для указания:

-   Плейбук, который вы хотите запустить (**backup_cisco_router_playbook.yaml**)
-   Файл инвентаризации и его расположение (**-i hosts**).

**Шаг 1. Создайте свой плейбук для Ansible.**

Плейбук Ansible - это файл YAML. Убедитесь, что вы используете правильный отступ YAML. Каждый пробел и тире значимы. Вы можете потерять часть форматирования, если скопируете и вставите код в этой лабораторной работе.

1.  В VS Code создайте новый файл в каталоге **ansible-csr1000v** со следующим именем: **backup_cisco_router_playbook.yaml**
1.  Добавьте в файл следующую информацию.

    ```yaml
    ---
    - name: AUTOMATIC BACKUP OF RUNNING-CONFIG
    hosts: CSR1kv
    gather_facts: false
    connection: local
    
    tasks:
    - name: DISPLAYING THE RUNNING-CONFIG
        ios_command:
        commands:
            - show running-config  
        register: config
    
    - name: SAVE OUTPUT TO ./backups/
        copy:
        content: "{{ config.stdout[0] }}"
        dest: "backups/show_run_{{ inventory_hostname }}.txt"
    ```

**Шаг 2. Изучите свой сценарий Ansible.**

Созданная вами книга содержит одну пьесу с двумя задачами. Ниже приводится объяснение вашей пьесы:

-   `---` Это в начале каждого файла YAML, что указывает YAML, что это отдельный документ. Каждый файл может содержать несколько документов, разделенных знаком `---`
-   `name: AUTOMATIC BACKUP OF RUNNING-CONFIG` - Это название пьесы.
-   `hosts: CSR1kv` - Это псевдоним ранее настроенного файла **hosts**. Ссылаясь на этот псевдоним в вашей книге воспроизведения, книга воспроизведения может использовать все параметры, связанные с этой записью файла инвентаризации, включая имя пользователя, пароль и IP-адрес устройства.
-   `gather_facts: false` - Изначально Ansible был разработан для работы с серверами Linux, копируя модули Python на серверы для автоматизации задач. В этом нет необходимости при работе с сетевыми устройствами.
-   `connection: local` - Указывает, что вы не используете SSH, поэтому соединение является локальным.
-   `tasks`**:** - Это ключевое слово указывает на одну или несколько задач, которые необходимо выполнить.

Первая задача - отобразить файл running-config.

-   `- name`: DISPLAYING THE RUNNING-CONFIG - Название задачи.
-   `ios_command`**:**- Это **модуль** Ansible, который используется для отправки команд устройству IOS и возврата результатов, считанных с устройства. Однако он не поддерживает команды конфигурации. Для этой цели используется модуль **ios_config**, как вы увидите в следующей части этой лабораторной работы.

>   **Примечание**: В терминале Linux вы можете использовать команду **ansible-doc** module_name для просмотра страниц руководства для любого **модуля** и параметров, связанных с этим модулем. (например, **ansible-doc ios_command**)

-   `commands`**:**- Этот параметр связан с модулем **ios_command**. Он используется для перечисления команд IOS в playbook, которые должны быть отправлены на удаленное устройство IOS. Результат команды возвращается.
-   `- show running-config` - Это команда Cisco IOS, отправленная с помощью модуля **ios_command**.
-   `register: config` - Ansible включает регистры, используемые для записи вывода задачи в переменную. Эта запись указывает, что выходные данные предыдущей команды **show running-config** будут сохранены в переменной **config**.

Вторая задача - сохранить вывод:

-   `- name: SAVE OUTPUT TO ./backups/` - Название задачи
-   `copy:`- Это **модуль** Ansible, используемый для копирования файлов в удаленное место. С этим модулем связаны два параметра:
-   `content: "{{ config.stdout[0] }}"` - указанное значение для этого параметра - это данные, хранящиеся в переменной **config**, регистровой переменной Ansible, использованной в предыдущей задаче. Стандартный вывод (**stdout**) - это дескриптор файла по умолчанию, куда процесс может записывать вывод, используемый в Unix-подобных операционных системах, таких как Linux и Mac OS X.
-   `dest: "backups/show_run_{{ inventory_hostname }}.txt"` - Это путь и имя файла, куда следует скопировать файл. Переменная **inventory_hostname** - это «волшебная переменная» Ansible, которая автоматически получает имя хоста, настроенное в файле **hosts**. В вашем случае напомним, что это **CSR1kv**. Этот параметр приводит к созданию файла **show_run_CSR1kv.txt**, хранящегося в каталоге резервных копий. Этот файл будет содержать вывод команды **show running-config**. На следующем шаге вы создадите каталог резервных копий.

**Шаг 3. Запустите Ansible Backup Playbook.**

1.  В части 1 вы запустили виртуальную машину CSR1000v. Пингуйте его, чтобы убедиться, что у вас есть к нему доступ. Введите **Ctrl + C**, чтобы прервать проверку связи.

    ```
    devasc@labvm:~/labs/devnet-src/ansible$ ping 192.168.56.101
    PING 192.168.56.101 (192.168.56.101) 56(84) bytes of data.
    64 bytes from 192.168.56.101: icmp_seq=1 ttl=63 time=0.913 ms
    64 bytes from 192.168.56.101: icmp_seq=2 ttl=63 time=0.875 ms
    ^C
    --- 192.168.56.101 ping statistics ---
    2 packets transmitted, 2 received, 0% packet loss, time 1000ms
    rtt min/avg/max/mdev = 0.875/0.894/0.913/0.019 ms
    devasc@labvm:~/labs/devnet-src/ansible$
    ```

1.  Создайте каталог **backups**. Как указано в последней строке вашей книги, это каталог, в котором будет храниться файл конфигурации резервной копии.

    ```
    devasc@labvm:~/labs/devnet-src/ansible$ mkdir backups
    ```

1.  Теперь вы можете запустить Ansible playbook с помощью команды **ansible-playbook**:

    ```
    devasc@labvm:~/labs/devnet-src/ansible$ ansible-playbook backup_cisco_router_playbook.yaml 

    PLAY [AUTOMATIC BACKUP OF RUNNING CONFIG] *****************************************

    TASK [DISPLAYING THE RUNNING-CONFIG] **********************************************
    ok: [CSR1kv]

    TASK [SAVE OUTPUT TO ./backups/] **************************************************
    changed: [CSR1kv]

    PLAY RECAP ************************************************************************
    CSR1kv : ok=2  changed=1  unreachable=0  failed=0 skipped=0 rescued=0  ignored=0 

    devasc@labvm:~/labs/devnet-src/ansible$
    ```

    >   **Примечание**: Во многих примерах вы увидите, что playbook запускается с параметром **-i** inventory-filename. Например:

    ```
    devasc@labvm:~/labs/devnet-src/ansible$ ansible-playbook backup_cisco_router_playbook.yaml -i hosts
    ```

    Эта опция сообщает Ansible расположение и имя файла инвентаризации, а также список устройств, которые будет использовать playbook. В этом параметре нет необходимости, поскольку вы настроили имя и расположение файла инвентаризации в локальном файле **ansible.cfg**: `inventory=./hosts`. Вы можете использовать параметр **-i** *inventory-filename*, чтобы переопределить информацию в файле **ansible.cfg**.

    **PLAY RECAP** должен отображать **ok = 2 changed = 1**, указывая на успешное выполнение playbook.

Если ваш сценарий воспроизведения Ansible не работает, обратите внимание на следующие моменты:

-   Убедитесь, что ваши файлы **hosts** и **ansible.cfg** верны.
-   Убедитесь, что отступ YAML правильный.
-   Убедитесь, что ваша команда IOS верна.
-   Проверьте весь синтаксис Ansible playbook.
-   Убедитесь, что вы можете пропинговать CSR1000v.

    Если проблемы не исчезнут:

-   Попробуйте набирать по одной строке и каждый раз запускать playbook.
-   Сравните свой файл со сборником решений в каталоге **ansible_solutions**.

**Шаг 4. Убедитесь, что файл резервной копии был создан.**

В VS Code откройте папку резервных копий и откройте файл **show_run_CSR1kv.txt**. Вы также можете использовать окно терминала, чтобы перехватить файл с помощью **cat backups/show_run_CSR1kv.txt**. Теперь у вас есть резервная копия конфигурации CSR1000v.

```
devasc@labvm:~/labs/devnet-src/ansible/backups$ cat show_run_CSR1kv.txt
Building configuration...

Current configuration : 4004 bytes
!
! Last configuration change at 23:57:14 UTC Sun May 17 2020
!
version 16.9
service timestamps debug datetime msec
service timestamps log datetime msec
platform qfp utilization monitor load 80
no platform punt-keepalive disable-kernel-core
platform console virtual
!
hostname CSR1kv
!
<output omitted>
```

### Часть 4. Используйте Ansible для настройки устройства

В этой части вы создадите еще одну книгу сценариев Ansible для настройки адресации IPv6 на маршрутизаторе CSR1000v.

**Шаг 1. Просмотрите файл инвентаризации хостов.**

1.  Еще раз проверьте файл инвентаризации **hosts**. Напоминаем, что этот файл содержит псевдоним **CSR1kv** и три инвентарные переменные для имени пользователя, пароля и IP-адреса хоста. В сборнике пьес для этой части также будут использоваться этот файл и файл **ansible.cfg**, который вы создали в начале лабораторной работы.

    ```
    devasc@labvm:~/labs/devnet-src/ansible$ cat hosts
    # Enter the hosts or devices for Ansible playbooks
    CSR1kv ansible_user=cisco ansible_password=cisco123! ansible_host=192.168.56.101
    devasc@labvm:~/labs/devnet-src/ansible$
    ```

**Шаг 2. Создайте новый плейбук.**

1.  В VS Code создайте новый файл в каталоге **ansible-csr1000v** со следующим именем:  
    **cisco_router_ipv6_config_playbook.yaml**
1.  Добавьте в файл следующую информацию. Убедитесь, что вы используете правильный отступ YAML. Каждый пробел и тире значимы. Вы можете потерять часть форматирования, если скопируете и вставите.

    ```yaml
    ---
    - name: CONFIGURE IPv6 ADDRESSING
    hosts: CSR1kv
    gather_facts: false
    connection: local

    tasks:
    - name: SET IPv6 ADDRESS 
        ios_config:
        parents: "interface GigabitEthernet1"
        lines:
            - description IPv6 ADDRESS 
            - ipv6 address 2001:db8:acad:1::1/64
            - ipv6 address fe80::1:1 link-local

    - name: SHOW IPv6 INTERFACE BRIEF 
        ios_command:
        commands:
            - show ipv6 interface brief
        register: output

    - name: SAVE OUTPUT ./ios_configurations/
        copy: 
        content: "{{ output.stdout[0] }}"
        dest: "ios_configurations/IPv6_output_{{ inventory_hostname }}.txt"
    ```

**Шаг 3. Изучите свой сценарий Ansible.**

Большая часть этого учебника аналогична учебнику, который вы создали в предыдущей части. Основное отличие состоит в том, что первая задача ЗАДАТЬ АДРЕС IPv6.

Ниже приводится краткое описание элементов задачи:

-   `ios_config`**:**- Это модуль Ansible, используемый для настройки устройства IOS. Вы можете использовать команду **ansible-doc ios_config**, чтобы просмотреть подробную информацию о параметрах **parents** и **lines**, используемых в этом плейбуке.
-   `parents: "interface GigabitEthernet1"` - Этот параметр указывает режим конфигурации интерфейса IOS.
-   `lines`**:** - В этом разделе настраивается упорядоченный набор команд IOS, в котором указывается информация об адресации IPv6 для интерфейса GigabitEthernet1.

В остальном учебник аналогичен задачам из предыдущей части. Вторая задача использует модуль **ios_command** и команду **show ipv6 interface brief** для отображения вывода и отправки его на вывод регистра.

Последняя задача сохраняет информацию в выходном регистре в файл **IPv6_output_CSR1kv.txt** в подкаталоге **ios_configurations**.

**Шаг 4. Запустите Ansible playbook, чтобы настроить адресацию IPv6 на виртуальной машине CSR1000v.**

1.  В части 1 вы запустили виртуальную машину CSR1000v. Пингуйте его, чтобы убедиться, что у вас есть к нему доступ. Введите **Ctrl + C**, чтобы прервать проверку связи.

    ```
    devasc@labvm:~/labs/devnet-src/ansible$ ping 192.168.56.101
    PING 192.168.56.101 (192.168.56.101) 56(84) bytes of data.
    64 bytes from 192.168.56.101: icmp_seq=1 ttl=63 time=0.913 ms
    64 bytes from 192.168.56.101: icmp_seq=2 ttl=63 time=0.875 ms
    ^C
    --- 192.168.56.101 ping statistics ---
    2 packets transmitted, 2 received, 0% packet loss, time 1000ms
    rtt min/avg/max/mdev = 0.875/0.894/0.913/0.019 ms
    devasc@labvm:~/labs/devnet-src/ansible$
    ```

1.  Создайте каталог **ios_configurations**. Как указано в последней строке вашего playbook, это каталог, в котором будут храниться выходные данные команды **show ipv6 interface brief**.

    ```
    devasc@labvm:~/labs/devnet-src/ansible$ mkdir ios_configurations
    ```

1.  Теперь вы можете запустить Ansible playbook с помощью команды **ansible-playbook**. Параметр -**v** (verbose) можно использовать для отображения задач, выполняемых в playbook.

    ```
    devasc@labvm:~/labs/devnet-src/ansible$ ansible-playbook -v cisco_router_ipv6_config_playbook.yaml
    Using /home/devasc/labs/ansible-csr1000v/ansible.cfg as config file

    PLAY [CONFIGURE IPv6 ADDRESSING] ***********************************************

    TASK [SET IPv6 ADDRESS] ********************************************************
    changed: [CSR1kv] => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"}, "banners": {}, "changed": true, "commands": ["interface GigabitEthernet1", "description IPv6 ADDRESS", "ipv6 address 2001:db8:acad:1::1/64", "ipv6 address fe80::1:1 link-local"], "updates": ["interface GigabitEthernet1", "description IPv6 ADDRESS", "ipv6 address 2001:db8:acad:1::1/64", "ipv6 address fe80::1:1 link-local"]}

    TASK [SHOW IPv6 INTERFACE BRIEF] ***********************************************
    ok: [CSR1kv] => {"changed": false, "stdout": ["GigabitEthernet1       [up/up]\n    FE80::1:1\n    2001:DB8:ACAD:1::1"], "stdout_lines": [["GigabitEthernet1       [up/up]", "    FE80::1:1", "    2001:DB8:ACAD:1::1"]]}

    TASK [SAVE OUTPUT ./ios_configurations/] ***************************************
    ok: [CSR1kv] => {"changed": false, "checksum": "60784fbaae4bd825b7d4f121c450effe529b553c", "dest": "ios_configurations/IPv6_output_CSR1kv.txt", "gid": 900, "group": "devasc", "mode": "0664", "owner": "devasc", "path": "ios_configurations/IPv6_output_CSR1kv.txt", "size": 67, "state": "file", "uid": 900}

    PLAY RECAP *********************************************************************
    CSR1kv                     : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
    devasc@labvm:~/labs/devnet-src/ansible$
    ```

При первом запуске playbook в **PLAY RECAP** должно отображаться **ok = 3 changed = 2** и **failed = 0**, указывая на успешное выполнение. Эти значения могут быть другими, если вы снова запустите playbook.

**Шаг 5. Убедитесь, что файл вывода был создан.**

В VS Code откройте папку **ios_configurations** и щелкните файл **IPv6_output_CSR1kv.txt**. Вы также можете использовать окно терминала для просмотра файла с **cat ios_configurations/IPv6_output_CSR1kv.txt**. Теперь у вас есть резервная копия конфигурации CSR1000v.

```
devasc@labvm:~/labs/devnet-src/ansible/ansible-csr1000v$ cat ios_configurations/IPv6_output_CSR1kv.txt 
GigabitEthernet1       [up/up]
    FE80::1:1
    2001:DB8:ACAD:1::1
devasc@labvm:~/labs/ansible-csr1000v/ios_configurations$
```
