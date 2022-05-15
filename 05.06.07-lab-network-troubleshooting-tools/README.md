## Цели

-   **Часть 1: Запуск виртуальной машины DEVASC**
-   **Часть 2: Изучите инструмент устранения неполадок ifconfig**
-   **Часть 3: Изучите инструмент устранения неполадок ping**
-   **Часть 4: Изучите инструмент устранения неполадок traceroute**
-   **Часть 5: Изучите инструмент устранения неполадок nslookup**

## Предпосылки / Сценарий

Чтобы исправить проблемы с сетевым подключением, разработчику важно понимать, как использовать основные инструменты устранения неполадок сети. Эти инструменты используются, чтобы определить, в чем может быть проблема с подключением.

## Необходимые ресурсы

-   1 ПК с операционной системой по вашему выбору
-   Virtual Box или VMWare
-   Виртуальная машина DEVASC

## Инструкции

### Часть 1. Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC.

### Часть 2. Изучите инструмент устранения неполадок ifconfig

Инструмент **ifconfig** - это приложение для использования с операционными системами на основе UNIX, такими как Linux. Аналогичная утилита доступна в Windows под названием ipconfig. Эти приложения используются для управления сетевыми интерфейсами из командной строки. Вы можете использовать ifconfig для выполнения следующих задач:

-   Настройте IP-адрес и маску подсети для интерфейса.
-   Получить статус сетевых интерфейсов.
-   Включение или отключение сетевых интерфейсов.
-   Измените MAC-адрес сетевого интерфейса.

**Шаг 1. Просмотрите параметры ifconfig.**

Инструмент **ifconfig** имеет множество различных параметров, которые можно добавить в команду для выполнения определенных задач.

1.  Откройте окно терминала прямо с рабочего стола или в VS Code.
2.  Введите **ifconfig --help**, чтобы увидеть все доступные параметры для команды.

    ```
    devasc@labvm:~$ ifconfig --help
    Usage:
    ifconfig [-a] [-v] [-s] <interface> [[<AF>] <address>]
    [add <address>[/<prefixlen>]]
    [del <address>[/<prefixlen>]]
    [[-]broadcast [<address>]]  [[-]pointopoint [<address>]]
    [netmask <address>]  [dstaddr <address>]  [tunnel <address>]
    [outfill <NN>] [keepalive <NN>]
    [hw <HW> <address>]  [mtu <NN>]
    [[-]trailers]  [[-]arp]  [[-]allmulti]
    [multicast]  [[-]promisc]
    [mem_start <NN>]  [io_addr <NN>]  [irq <NN>]  [media <type>]
    [txqueuelen <NN>]
    [[-]dynamic]
    [up|down] ...
    ```

Это обзор некоторых из наиболее широко используемых опций;

-   **add** или **del** - этот параметр позволяет добавлять или удалять IP-адреса и их маску подсети (длину префикса).
-   **hw ether**- Используется для изменения физического MAC-адреса. Это может быть полезно, например, чтобы изменить его на легко узнаваемое имя, чтобы оно выделялось в журналах для устранения неполадок.
-   **up** и **down** - эти параметры используются для включения и отключения интерфейсов. Убедитесь, какой интерфейс вы отключаете. Если это тот, который вы используете для удаленного подключения к устройству, вы будете отключены!

**Шаг 2. Посмотрите статус всех интерфейсов.**

1.  Отобразите состояние всех используемых сетевых интерфейсов, выполнив отдельную команду **ip addr**.

    ```
    devasc@labvm:~$ ip addr
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        link/ether 08:00:27:e9:3d:e6 brd ff:ff:ff:ff:ff:ff
        inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic enp0s3
        valid_lft 85901sec preferred_lft 85901sec
        inet6 fe80::a00:27ff:fee9:3de6/64 scope link 
        valid_lft forever preferred_lft forever
    3: dummy0: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
        link/ether e2:2b:24:96:98:b8 brd ff:ff:ff:ff:ff:ff
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
        inet6 fe80::e02b:24ff:fe96:98b8/64 scope link 
        valid_lft forever preferred_lft forever
    devasc@labvm:~$
    ```

    Из этого вывода мы можем многое рассказать об интерфейсах виртуальных машин:

    -   Есть 3 интерфейса: интерфейс обратной петли (**lo**), **enp0s3** и **dummy0**.
    -   **ether** показывает MAC-адрес и что Ethernet является инкапсуляцией канала.
    -   **inet** - IP-адрес, маска подсети отображается в виде косой черты, а **brd** - широковещательный адрес.
    -   **UP** указывает, что интерфейс включен.
    -   **mtu** - максимальная единица передачи, определяющая максимальное количество байтов, которое кадр может быть передан на этом носителе до фрагментации.

### Часть 3. Изучите инструмент устранения неполадок ping

Инструмент **ping** - это приложение, которое используется для проверки сетевого подключения между устройствами. ping использует протокол управляющих сообщений Интернета (ICMP) для отправки пакетов на устройство в сети и ожидает ответа от устройства. ping сообщает о сетевых ошибках, потере пакетов и времени жизни (TTL), а также другой статистике.

**Шаг 1. Просмотрите параметры ping.**

Ping доступен только в окне терминала или в командной строке.

1.  Введите **ping -help**, чтобы увидеть все доступные параметры команды.

    ```
    devasc@labvm:~$ ping -help

    Usage
    ping [options] <destination>

    Options:
    <destination>      dns name or ip address
    -a                 use audible ping
    -A                 use adaptive ping
    -B                 sticky source address
    -c <count>         stop after <count> replies
    -D                 print timestamps
    -d                 use SO_DEBUG socket option
    -f                 flood ping
    -h                 print help and exit
    -I <interface>     either interface name or address
    -i <interval>      seconds between sending each packet
    -L                 suppress loopback of multicast packets
    -l <preload>       send <preload> number of packages while waiting replies
    -m <mark>          tag the packets going out
    -M <pmtud opt>     define mtu discovery, can be one of <do|dont|want>
    -n                 no dns name resolution
    -O                 report outstanding replies
    -p <pattern>       contents of padding byte
    -q                 quiet output
    -Q <tclass>        use quality of service <tclass> bits
    -s <size>          use <size> as number of data bytes to be sent
    -S <size>          use <size> as SO_SNDBUF socket option value
    -t <ttl>           define time to live
    -U                 print user-to-user latency
    -v                 verbose output
    -V                 print version and exit
    -w <deadline>      reply wait <deadline> in seconds
    -W <timeout>       time to wait for response

    IPv4 options:
    -4                 use IPv4
    -b                 allow pinging broadcast
    -R                 record route
    -T <timestamp>     define timestamp, can be one of <tsonly|tsandaddr|tsprespec>

    IPv6 options:
    -6                 use IPv6
    -F <flowlabel>     define flow label, default is random
    -N <nodeinfo opt>  use icmp6 node info query, try <help> as argument

    For more details see ping(8).
    devasc@labvm:~$
    ```

**Шаг 2. Пингуйте хост.**

Инструмент **ping** имеет множество различных опций, которые можно выбрать, чтобы настроить, как должно происходить общение. Некоторые из параметров, которые вы можете указать, включают:

-   Укажите, сколько эхо-запросов ICMP вы хотите отправить.
-   Определите исходный IP-адрес, если на устройстве несколько интерфейсов.
-   Укажите время ожидания ответа.
-   Размер пакета, если вы хотите отправлять пакеты большего размера, чем 64 байта по умолчанию. Это может помочь определить максимальный размер передаваемого блока данных (MTU).
1.  ping www.cisco.com, чтобы узнать, доступен ли он.

    ```
    devasc@labvm:~$ ping -c 5 www.cisco.com
    PING e2867.dsca.akamaiedge.net (23.66.161.25) 56(84) bytes of data.
    64 bytes from a23-66-161-25.deploy.static.akamaitechnologies.com (23.66.161.25): icmp_seq=1 ttl=49 time=58.4 ms
    64 bytes from a23-66-161-25.deploy.static.akamaitechnologies.com (23.66.161.25): icmp_seq=2 ttl=49 time=63.1 ms
    64 bytes from a23-66-161-25.deploy.static.akamaitechnologies.com (23.66.161.25): icmp_seq=3 ttl=49 time=61.2 ms
    64 bytes from a23-66-161-25.deploy.static.akamaitechnologies.com (23.66.161.25): icmp_seq=4 ttl=49 time=57.7 ms
    64 bytes from a23-66-161-25.deploy.static.akamaitechnologies.com (23.66.161.25): icmp_seq=5 ttl=49 time=57.6 ms

    --- e2867.dsca.akamaiedge.net ping statistics ---
    5 packets transmitted, 5 received, 0% packet loss, time 8153ms
    rtt min/avg/max/mdev = 57.597/59.605/63.145/2.205 ms
    devasc@labvm:~$
    ```

    Этот эхо-запрос указал количество пакетов равным 5.

Инструмент **ping** автоматически выполняет разрешение DNS, возвращая 23.66.161.25 (ваш возвращенный IP-адрес может быть другим). Также отображается время жизни (TTL) для полученных эхо-ответов и время приема-передачи. Окончательная статистика подтверждает, что 5 пакетов эхо-запроса ICMP было передано и 5 пакетов эхо-ответа ICMP было получено, при этом потеря пакетов составила 0%. Также отображается статистика о минимальном, среднем, максимальном и стандартном отклонении времени, которое потребовалось пакетам, чтобы добраться до места назначения и обратно.

Если вы не получаете никаких ответов от места назначения, это не обязательно означает, что хост отключен или недоступен. Это может означать, что пакеты ICMP блокируются брандмауэром. Рекомендуется предоставлять только те сервисы, которые должны быть доступны на хостах в сети.

Для IPv6 существует аналогичная утилита, которая называется ping6 и также доступна в большинстве операционных систем.

### Часть 4. Изучите инструмент устранения неполадок traceroute

Инструмент **traceroute** отображает маршрут, по которому пакеты идут к месту назначения. Альтернатива Microsoft Windows называется **tracert**. Наблюдение за маршрутом сетевого трафика от источника до пункта назначения важно для устранения неполадок, поскольку петли маршрутизации и неоптимальные пути могут быть обнаружены и исправлены.

Команда traceroute использует пакеты ICMP для определения пути к месту назначения. Поле Time to Live (TTL) в заголовке IP-пакета используется для предотвращения бесконечных петель в сети. Для каждого перехода или маршрутизатора, через который проходит IP-пакет, поле TTL уменьшается на единицу. Когда значение поля TTL достигает 0, пакет отбрасывается, избегая бесконечных циклов. Обычно поле TTL устанавливается на максимальное значение 255 в источнике трафика, потому что хост пытается максимизировать шансы того, что этот пакет попадет в пункт назначения. traceroute меняет эту логику и постепенно увеличивает значение TTL с 1 и продолжает добавлять 1 в поле TTL для следующего пакета и так далее. Установка значения TTL 1 для первого пакета означает, что пакет будет отброшен на первом маршрутизаторе. По умолчанию большинство маршрутизаторов отправить обратно источнику трафика пакет ICMP Time Exceeded, информируя его о том, что пакет достиг значения TTL, равного 0, и должен быть отброшен. traceroute использует информацию, полученную от маршрутизатора, для определения его IP-адреса и имени хоста, а также времени приема-передачи.

Для IPv6 существует альтернатива под названием traceroute6 для операционных систем на базе UNIX и tracert6 для операционных систем на базе Microsoft Windows.

**Шаг 1. Просмотрите параметры трассировки.**

1.  Введите **traceroute --help**, чтобы увидеть все доступные параметры команды.

    ```
    devasc@labvm:~$ traceroute --help
    Usage: traceroute [OPTION...] HOST
    Print the route packets trace to network host.

    -f, --first-hop=NUM        set initial hop distance, i.e., time-to-live
    -g, --gateways=GATES       list of gateways for loose source routing
    -I, --icmp                 use ICMP ECHO as probe
    -m, --max-hop=NUM          set maximal hop count (default: 64)
    -M, --type=METHOD          use METHOD (`icmp' or `udp') for traceroute
                                operations, defaulting to `udp'
    -p, --port=PORT            use destination PORT port (default: 33434)
    -q, --tries=NUM            send NUM probe packets per hop (default: 3)
        --resolve-hostnames    resolve hostnames
    -t, --tos=NUM              set type of service (TOS) to NUM
    -w, --wait=NUM             wait NUM seconds for response (default: 3)
    -?, --help                 give this help list
        --usage                give a short usage message
    -V, --version              print program version

    Mandatory or optional arguments to long options are also mandatory or optional
    for any corresponding short options.

    Report bugs to <bug-inetutils@gnu.org>.
    devasc@labvm:~$
    ```

В traceroute доступно несколько опций, включая:

-   Указание значения TTL для первого отправленного пакета, по умолчанию 1.
-   Указание максимального значения TTL. По умолчанию он увеличивает значение TTL до 64 или до тех пор, пока не будет достигнута цель.
-   Указание адреса источника, если на устройстве несколько интерфейсов.
-   Указание значения качества обслуживания (QoS) в заголовке IP.
-   Указание длины пакета.

**Шаг 2. Использование traceroute для поиска пути к веб-серверу.**

Из-за того, как Virtual Box реализует сеть NAT, вы не можете отслеживать за пределами вашей виртуальной машины. Вам нужно будет изменить подключение виртуальной машины на сетевой мост. Но тогда вы не сможете общаться с CSR1000v в других лабораторных работах. Поэтому мы рекомендуем оставить виртуальную машину в режиме NAT.

Однако вы должны иметь возможность использовать команду **traceroute** на локальном хосте. Для хостов Mac и Linux используйте команду **traceroute**. Для хостов Windows используйте команду **tracert**, как показано ниже. Откройте командную строку на локальном хосте и проследите маршрут до www.netacad.com, чтобы узнать, сколько переходов и сколько времени требуется для его достижения. Ваш результат будет другим.

```
C:\> tracert www.netacad.com

Tracing route to e7792.dsca.akamaiedge.net [2600:1406:22:183::1e70]
over a maximum of 30 hops:

1    43 ms    38 ms    36 ms  hsrp-2001-420-c0c8-1.cisco.com [2001:420:c0c8::1]
2    48 ms    54 ms    40 ms  sjc05-sbb-gw1-twe1-0-13.cisco.com [2001:420:280:1aa::]
3    39 ms    37 ms    38 ms  sjc05-rbb-gw1-por20.cisco.com [2001:420:41:116::]
4    37 ms    38 ms    38 ms  sjc12-corp-gw1-ten1-3-0.cisco.com [2001:420:41:11c::1]
5    39 ms    39 ms    45 ms  sjc12-dmzbb-gw1-vla777.cisco.com [2001:420:82:2::d]
6    51 ms    39 ms    37 ms  sjc5-cbb-gw1-be92.cisco.com [2001:420:82:4e::]
7    39 ms    39 ms    38 ms  sjc12-isp-gw2-ten0-0-0.cisco.com [2001:420:82:f::]
8    78 ms    57 ms    65 ms  2001:1890:c00:6c01::eee7:a12
9    44 ms    42 ms    47 ms  sj2ca81crs.ipv6.att.net [2001:1890:ff:ffff:12:122:110:62]
10    46 ms    46 ms    47 ms  2001:1890:ff:ffff:12:122:149:225
11    43 ms    41 ms    43 ms  scaca401cts.ipv6.att.net [2001:1890:ff:ffff:12:122:137:245]
12    43 ms    43 ms    44 ms  2001:1890:fff:2180:12:120:13:178
13    53 ms    54 ms    45 ms  2001:1890:1ff:2a80:12:120:183:64
14    52 ms    42 ms    42 ms  g2600-1406-0022-0183-0000-0000-0000-1e70.deploy.static.akamaitechnologies.com [2600:1406:22:183::1e70]

Trace complete.

devasc@labvm:~$
```

Выходные данные показывают, что на пути 14 переходов. Также отображается время прохождения туда и обратно.

### Часть 5. Изучение инструмента устранения неполадок nslookup

Инструмент **nslookup**, используемый для запроса системы доменных имен (DNS) для получения доменного имени для сопоставления IP-адресов. Этот инструмент полезен, чтобы определить, разрешает ли DNS-сервер, настроенный на определенном хосте, имена хостов в IP-адреса.

**Шаг 1. Запрос домена.**

Чтобы использовать nslookup, вам нужно ввести имя хоста, которое вы пытаетесь преобразовать в IP-адрес. Это будет использовать настроенный DNS-сервер для поиска IP-адреса. Вы также можете указать используемый DNS-сервер.

Использование: nslookup [ХОСТ] [СЕРВЕР]

1.  Введите **nslookup www.cisco.com**, чтобы определить IP-адрес домена.

    ```
    devasc@labvm:~$ nslookup www.cisco.com
    Server:           127.0.0.53
    Address:          127.0.0.53#53

    Non-authoritative answer:
    www.cisco.com	canonical name = origin-www.cisco.com.
    Name:    origin-www.cisco.com
    Address: 173.37.145.84
    Name:    origin-www.cisco.com
    Address: 2001:420:1101:1::a

    devasc@labvm:~$
    ```

    Команда возвращает неавторизованный ответ, а также имя и адрес IPv4 и IPv6. Неавторизованный ответ означает, что сервер не содержит исходных записей зоны домена, скорее, ответ создан на основе предыдущих запросов DNS.

**Примечание**: Ваш результат, скорее всего, будет другим. Однако вы должны увидеть адреса IPv4 и IPv6.

**Шаг 2. Запросить IP-адрес.**

Вы также можете искать IP-адреса, чтобы обнаружить связанный с ним домен.

1.  Запросить у DNS-сервера IP-адрес 8.8.8.8.

    ```
    devasc@labvm:~$ nslookup 8.8.8.8
    8.8.8.8.in-addr.arpa	name = dns.google.

    Authoritative answers can be found from:

    devasc@labvm:~$
    ```

**Шаг 3. Запросите домен, используя определенный DNS-сервер.**

1.  Введите **nslookup www.cisco.com 8.8.8.8**, чтобы определить IP-адрес домена в соответствии с DNS Google.

    ```
    devasc@labvm:~$ nslookup www.cisco.com 8.8.8.8
    Server:           8.8.8.8
    Address:          8.8.8.8#53

    Non-authoritative answer:
    www.cisco.com	canonical name = www.cisco.com.akadns.net.
    www.cisco.com.akadns.net	canonical name = wwwds.cisco.com.edgekey.net.
    wwwds.cisco.com.edgekey.net	canonical name = wwwds.cisco.com.edgekey.net.globalredir.akadns.net.
    wwwds.cisco.com.edgekey.net.globalredir.akadns.net	canonical name = e2867.dsca.akamaiedge.net.
    Name:	e2867.dsca.akamaiedge.net
    Address: 23.205.37.210
    Name:	e2867.dsca.akamaiedge.net
    Address: 2600:1406:22:182::b33
    Name:	e2867.dsca.akamaiedge.net
    Address: 2600:1406:22:19c::b33

    devasc@labvm:~$
    ```

    Обратите внимание, что с помощью этого метода сервер преобразовал адрес в три разных IP-адреса, все из которых отличаются от предыдущего запроса DNS. Эти серверы имеют другой кэш DNS-запросов, чем www.cisco.com.

