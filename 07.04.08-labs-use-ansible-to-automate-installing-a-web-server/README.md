## Цели

-   **Часть 1: Запуск виртуальной машины DEVASC**
-   **Часть 2: Настройка Ansible**
-   **Часть 3: Проверка связи с локальным веб-сервером**
-   **Часть 4: Создание Ansible Playbooks для автоматизации установки веб-сервера**
-   **Часть 5: Добавьте параметры в свой Ansible Playbook для веб-серверов Apache**

## Предпосылки / Сценарий

В этой лабораторной работе вы сначала настроите Ansible, чтобы он мог взаимодействовать с приложением веб-сервера. Затем вы создадите playbook, который автоматизирует процесс установки Apache на веб-сервере. Вы также создадите настраиваемую книгу для установки Apache с конкретными инструкциями.

## Необходимые ресурсы

-   1 ПК с операционной системой по вашему выбору
-   Virtual Box или VMWare
-   Виртуальная машина DEVASC

## Инструкции

### Часть 1. Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC сейчас.

### Часть 2. Настроить Ansible

Виртуальная машина DEVASC поставляется с предварительно установленным набором фиктивных IPv4-адресов, которые можно использовать для различных сценариев и моделирования. В этой части вы настроите Ansible для использования одного из фиктивных IPv4-адресов для локального веб-сервера.

**Шаг 1. Откройте терминал в DEVASC-LABVM.**

**Шаг 2. Включите SSH-сервер.**

Сервер SSH отключен в DEVASC-LABVM вместе с другими службами, которые обычно не требуются. Запустите его с помощью следующей команды.

```
devasc@labvm:~$ sudo systemctl start ssh
devasc@labvm:~$
```

>   **Примечание**: SSH-сервер и утилита **sshpass** уже установлены на вашей виртуальной машине. Для справки, они устанавливаются с помощью следующих команд:

**Установить SSH**

```
devasc@labvm:~$ sudo apt-get install openssh-server
```

**Установить sshpass**

```
devasc@labvm:~$ sudo apt-get install sshpass
```

**Шаг 3. Откройте доступный каталог в VS Code.**

1.  Откройте **VS Code**.
1.  Щелкните **File \> Open Folder...** и перейдите в папку **/labs/devnet-src/ansible**.
1.  Щелкните **ОК**.
1.  Два подкаталога для лабораторий Ansible теперь загружены на панель VS Code **EXPLORER** для вашего удобства. В этой лабораторной работе вы будете работать с каталогом **ansible-apache**.

**Шаг 4. Отредактируйте файл инвентаря Ansible**

1.  Откройте файл **hosts** в каталоге **ansible-apache**.
1.  Добавьте следующие строки в файл **hosts** и сохраните.

    ```
    [webservers]
    192.0.2.3 ansible_ssh_user=devasc ansible_ssh_pass=Cisco123!
    ```

1.  Учетные данные **devasc** и **Cisco123!** являются учетными данными администратора для виртуальной машины DEVASC. В этой лабораторной работе вы будете использовать IPv4-адрес 192.0.2.3. Это статический IPv4-адрес на виртуальной машине под интерфейсом dummy0, как показано в выходных данных команды **ip addr**.

    ```
    devasc@labvm:~/labs/devnet-src/ansible$ ip addr
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        link/ether 08:00:27:97:ae:11 brd ff:ff:ff:ff:ff:ff
        inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic enp0s3
        valid_lft 45882sec preferred_lft 45882sec
        inet6 fe80::a00:27ff:fe97:ae11/64 scope link 
        valid_lft forever preferred_lft forever
    3: dummy0: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
        link/ether a6:44:a7:e8:6a:9e brd ff:ff:ff:ff:ff:ff
        inet 192.0.2.1/32 scope global dummy0
        valid_lft forever preferred_lft forever
        inet 192.0.2.2/32 scope global dummy0
        valid_lft forever preferred_lft forever
        inet 192.0.2.3/32 scope global dummy0
        valid_lft forever preferred_lft forever
        inet 192.0.2.4/32 scope global dummy0
        valid_lft forever preferred_lft forever
        inet 192.0.2.5/32 scope global dummy0
        valid_lft forever preferred_lft forever
        inet6 fe80::a444:a7ff:fee8:6a9e/64 scope link 
        valid_lft forever preferred_lft forever
    devasc@labvm:~/labs/devnet-src/ansible$
    ```

**Шаг 5. Отредактируйте файл ansible.cfg.**

1.  В подкаталоге **ansible-apache** откройте файл **ansible.cfg**.
1.  Вы можете удалить комментарий. Добавьте в файл следующие строки и сохраните его. Файл **ansible.cfg** сообщает Ansible, где найти файл инвентаризации, и устанавливает определенные параметры по умолчанию.

    ```
    [defaults]
    # Use local hosts file in this folder
    inventory=./hosts 
    # Don't worry about RSA Fingerprints
    host_key_checking = False 
    # Do not create retry files
    retry_files_enabled = False
    ```

### Часть 3. Проверьте связь с локальным веб-сервером

В этой части вы убедитесь, что Ansible может отправлять команды на локальный веб-сервер.

**Шаг 1. Используйте модуль ping, чтобы убедиться, что Ansible может пинговать веб-сервер.**

Используйте модуль Ansible **ping** для проверки связи с устройствами, перечисленными в группе **webservers** файла инвентаризации хостов.

```
devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$ ansible webservers -m ping
192.0.2.3 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$
```

Если несколько устройств были перечислены в группе **webservers** в вашем файле инвентаризации **hosts**, выходные данные будут содержать аналогичную информацию для каждого устройства.

**Шаг 2. Используйте командный модуль, чтобы убедиться, что Ansible может взаимодействовать с веб-сервером.**

Используйте модуль Ansible **command** для проверки связи с устройствами, перечисленными в группе **webservers** файла инвентаризации хостов. В этом примере вы отправляете аргумент -**a "/bin/echo hello world"**, чтобы попросить локальный веб-сервер ответить «hello world».

```
devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$ ansible webservers -m command -a "/bin/echo hello world"
192.0.2.3 | CHANGED | rc=0 >>
hello world
devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$
```

### Часть 4. Создавайте Ansible Playbooks для автоматизации установки веб-сервера

В этой части вы создадите два сценария Ansible. Первая инструкция автоматизирует эхо-тест, который вы выполнили в предыдущей части. Представьте, что вы подключаете сотню веб-серверов. Группа [webserver] в файле **hosts** будет перечислять всю необходимую информацию для каждого веб-сервера. Затем вы можете использовать простую инструкцию для проверки связи со всеми из них с помощью одной команды. Во втором сценарии вы создадите и автоматизируете установку программного обеспечения веб-сервера Apache.

**Шаг 1. Создайте свой Ansible playbook, чтобы протестировать свою группу веб-серверов.**

На этом этапе вы создадите сборник воспроизведения Ansible для выполнения той же команды **echo**.

1.  В VS Code создайте новый файл в каталоге **ansible-apache** со следующим именем:

    **test_apache_playbook.yaml**

1.  Добавьте в файл следующую информацию. Убедитесь, что вы используете правильный отступ YAML. Каждый пробел и тире значимы. Вы можете потерять часть форматирования, если скопируете и вставите.

    ```
    ---
    - hosts: webservers
    tasks:
        - name: run echo command
        command: /bin/echo hello world
    ```

**Шаг 2. Запустите Ansible playbook, чтобы проверить свою группу веб-серверов.**

Запустите Ansible playbook с помощью команды **ansible-playbook** с параметром **-v** verbose. Вы должны увидеть результат, подобный следующему.

```
devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$ ansible-playbook -v test_apache_playbook.yaml
Using /home/devasc/labs/ansible/ansible-apache/ansible.cfg as config file

PLAY [webservers] **************************************************************

TASK [Gathering Facts] *********************************************************
ok: [192.0.2.3]

TASK [run echo command] ********************************************************
changed: [192.0.2.3] => {"changed": true, "cmd": ["/bin/echo", "hello", "world"], "delta": "0:00:00.002062", "end": "2020-05-20 21:35:32.346595", "rc": 0, "start": "2020-05-20 21:35:32.344533", "stderr": "", "stderr_lines": [], "stdout": "hello world", "stdout_lines": ["hello world"]}

PLAY RECAP *********************************************************************
192.0.2.3                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$
```

**Шаг 3. Создайте свой Ansible playbook для установки Apache.**

1.  В VS Code создайте новый файл в каталоге **ansible-apache** со следующим именем:

    **install_apache_playbook.yaml**

1.  Добавьте в файл следующую информацию. Убедитесь, что вы используете правильный отступ YAML. Каждый пробел и тире значимы. Вы можете потерять часть форматирования, если скопируете и вставите. Выделенный текст поясняется на следующем шаге.

    ```
    ---
    - hosts: webservers
    become: yes
    tasks:
        - name: INSTALL APACHE2
        apt: name=apache2 update_cache=yes state=latest
    
        - name: ENABLED MOD_REWRITE
        apache2_module: name=rewrite state=present
        notify:
            - RESTART APACHE2
    
    handlers:
        - name: RESTART APACHE2
        service: name=apache2 state=restarted
    ```

**Шаг 4. Изучите свой сценарий Ansible.**

Ниже приводится объяснение некоторых важных строк в вашем учебнике:

-   **hosts: webservers** - Это относится к группе устройств **webservers** в файле инвентаризации **hosts**. Этот сценарий будет запущен для всех устройств с этой группой.
-   **become: yes** - Ключевое слово **become** активирует выполнение команды **sudo**, что позволяет выполнять такие задачи, как установка приложений.
-   **apt:** - Модуль **apt** используется для управления пакетами и установками приложений в Linux.
-   **handlers:**- Обработчики похожи на задачу, но не запускаются автоматически. Их вызывает задача. Обратите внимание, что задача **ENABLED MOD_REWRITE** вызывает обработчик **RESTART APACHE2**.

**Шаг 5. Запустите резервное копирование Ansible, чтобы установить Apache.**

Запустите Ansible playbook с помощью команды **ansible-playbook** с параметром **-v** verbose. При первой установке Apache на вашу виртуальную машину задача **INSTALL APACHE2** займет от 30 секунд до нескольких минут в зависимости от скорости вашего интернета.

```
devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$ ansible-playbook -v install_apache_playbook.yaml
Using /home/devasc/labs/ansible/ansible-apache/ansible.cfg as config file

PLAY [webservers] **************************************************************

TASK [Gathering Facts] *********************************************************
ok: [192.0.2.3]

TASK [INSTALL APACHE2] *********************************************************
ok: [192.0.2.3] => {"cache_update_time": 1590010855, "cache_updated": true, "changed": false}

TASK [ENABLED MOD_REWRITE] *****************************************************
ok: [192.0.2.3] => {"changed": false, "result": "Module rewrite enabled"}

PLAY RECAP *********************************************************************
192.0.2.3   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
=
devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$
```

PLAY RECAP должен отображать **ok** и **failed = 0**, указывая на успешное выполнение playbook.

**Шаг 6. Убедитесь, что Apache установлен.**

1.  Используйте следующую команду, чтобы убедиться, что Apache теперь установлен. Нажмите «q», чтобы выйти.

    ```
    devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$ sudo systemctl status apache2
    ● apache2.service - The Apache HTTP Server
        Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor prese>
        Active: active (running) since Wed 2020-05-20 03:48:49 UTC; 10min ago
        Docs: https://httpd.apache.org/docs/2.4/
        Process: 8201 ExecStart=/usr/sbin/apachectl start (code=exited, status=0/SU>
    Main PID: 8225 (apache2)
        Tasks: 55 (limit: 4654)
        Memory: 5.3M
        CGroup: /system.slice/apache2.service
                ├─8225 /usr/sbin/apache2 -k start
                ├─8229 /usr/sbin/apache2 -k start
                └─8230 /usr/sbin/apache2 -k start
    devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$
    ```

1.  Откройте веб-браузер Chromium и введите IPv4-адрес вашего нового сервера, 192.0.2.3, чтобы увидеть веб-страницу Apache2 по умолчанию.

### Часть 5. Добавьте параметры в свой Ansible Playbook для веб-серверов Apache

В производственной среде установка Apache2 по умолчанию обычно настраивается для конкретных функций, необходимых организации. Плейбук Ansible также может помочь автоматизировать эти задачи настройки. В этой части вы настроите свой playbook, указав, что сервер Apache использует другой номер порта.

**Шаг 1. Создайте свой Ansible playbook для установки Apache.**

1.  В VS Code создайте новый файл в каталоге **ansible-apache** со следующим именем:

    **install_apache_options_playbook.yaml**

1.  Добавьте в файл следующую информацию. Убедитесь, что вы используете правильный отступ YAML. Каждый пробел и тире значимы. Вы можете потерять часть форматирования, если скопируете и вставите.

    ```
    ---
    - hosts: webservers
    become: yes
    tasks:
    - name: INSTALL APACHE2
        apt: name=apache2 update_cache=yes state=latest
    
    - name: ENABLED MOD_REWRITE
        apache2_module: name=rewrite state=present
        notify:
        - RESTART APACHE2
    
    - name: APACHE2 LISTEN ON PORT 8081
        lineinfile: dest=/etc/apache2/ports.conf regexp="^Listen 80" line="Listen 8081" state=present
        notify:
        - RESTART APACHE2
    
    - name: APACHE2 VIRTUALHOST ON PORT 8081
        lineinfile: dest=/etc/apache2/sites-available/000-default.conf regexp="^<VirtualHost \*:80>" line="<VirtualHost *:8081>" state=present
        notify:
        - RESTART APACHE2
    
    handlers:
    - name: RESTART APACHE2
        service: name=apache2 state=restarted
    ```

Этот список воспроизведения очень похож на предыдущий с добавлением двух задач, которые заставляют веб-серверы прослушивать порт 8081 вместо порта 80.

Модуль **lineinfile** используется для замены существующих строк в файлах /etc/apache2/ports.conf и /etc/apache2/sites-available/000-default.conf. Вы можете поискать в документации Ansible дополнительную информацию о модуле **lineinfile**.

**Шаг 2. Изучите два файла, которые будут изменены программой воспроизведения.**

Отобразите файлы **/etc/apache2/ports.conf** и **/etc/apache2/sites-available/000-default.conf**. Обратите внимание, что веб-сервер в настоящее время прослушивает порт 80.

    ```
    devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$ cat /etc/apache2/ports.conf
    # If you just change the port or add more ports here, you will likely also
    # have to change the VirtualHost statement in
    # /etc/apache2/sites-enabled/000-default.conf

    Listen 80

    <IfModule ssl_module>
            Listen 443
    <output omitted>

    devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$ cat /etc/apache2/sites-available/000-default.conf
    <VirtualHost *:80>
            # The ServerName directive sets the request scheme, hostname and port that
            # the server uses to identify itself. This is used when creating
            # redirection URLs. In the context of virtual hosts, the ServerName
    <output omitted>
    devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$
    ```

**Шаг 3. Запустите Ansible Playbook.**

1.  Запустите Ansible playbook с помощью команды **ansible-playbook**.

    ```
    devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$ ansible-playbook install_apache_options_playbook.yaml

    PLAY [webservers] **************************************************************

    TASK [Gathering Facts] *********************************************************
    ok: [192.0.2.3]

    TASK [INSTALL APACHE2] *********************************************************
    ok: [192.0.2.3]

    TASK [ENABLED MOD_REWRITE] *****************************************************
    ok: [192.0.2.3]

    TASK [APACHE2 LISTEN ON PORT 8081] *********************************************
    ok: [192.0.2.3]

    TASK [APACHE2 VIRTUALHOST ON PORT 8081] ****************************************
    ok: [192.0.2.3]

    PLAY RECAP *********************************************************************
    192.0.2.3                  : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

    devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$
    ```

**Шаг 4. Убедитесь, что Apache установлен.**

1.  Снова просмотрите файлы **/etc/apache2/ports.conf** и **/etc/apache2/sites-available/000-default.conf**. Обратите внимание, что плейбук изменил эти файлы для прослушивания порта 8081.

    ```
    devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$ cat /etc/apache2/ports.conf
    # If you just change the port or add more ports here, you will likely also
    # have to change the VirtualHost statement in
    # /etc/apache2/sites-enabled/000-default.conf

    Listen 8081

    <IfModule ssl_module>
            Listen 443
    </IfModule>

    <IfModule mod_gnutls.c>
            Listen 443
    </IfModule>

    # vim: syntax=apache ts=4 sw=4 sts=4 sr noet
    devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$ 

    devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$ cat /etc/apache2/sites-available/000-default.conf
    <VirtualHost *:8081>
            # The ServerName directive sets the request scheme, hostname and port that
            # the server uses to identify itself. This is used when creating
            # redirection URLs. In the context of virtual hosts, the ServerName
    <output omitted>
    devasc@labvm:~/labs/devnet-src/ansible/ansible-apache$
    ```

1.  Откройте веб-браузер Chromium и введите IPv4-адрес вашего нового сервера. Но на этот раз укажите 8081 в качестве номера порта, **192.0.2.3:8081**, чтобы увидеть веб-страницу Apache2 по умолчанию.

    >   **Примечание**: Хотя вы можете видеть в файле **ports.conf**, что Apache2 также прослушивает порт 443, это для безопасного HTTP. Вы еще не настроили Apache2 для безопасного доступа. Это, конечно, будет добавлено в вашу книгу сценариев Ansible, но выходит за рамки этого курса.
