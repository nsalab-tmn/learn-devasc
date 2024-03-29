## Цели

-   **Часть 1: Установите виртуальную машину CSR1000v на VirtualBox**
-   **Часть 2: Проверка связи с ВМ CSR1000v**

## Предпосылки / Сценарий

В этой лабораторной работе вы установите маршрутизатор облачных служб CSR1000v. Это программный виртуальный маршрутизатор Cisco, развернутый на экземпляре виртуальной машины (ВМ) на серверном оборудовании x86. Когда Cisco CSR 1000v развертывается на виртуальной машине, программное обеспечение Cisco IOS XE функционирует так, как если бы оно было развернуто на традиционной аппаратной платформе Cisco. В зависимости от образа программного обеспечения Cisco IOS XE можно настроить различные функции.

>   **Примечание**: Имя CSR1000v будет сокращено до CSR1kv в будущих лабораторных работах. Однако для этой лабораторной работы мы используем CSR1000v.

## Необходимые ресурсы

-   Хост-компьютер с как минимум 4 ГБ ОЗУ и 2 ГБ свободного дискового пространства
-   Virtual Box или VMware
-   Виртуальная машина DEVASC

## Инструкции

### Часть 1. Установите виртуальную машину CSR1000v

Установите виртуальную машину CSR1000v прямо на свой компьютер x86. Требуется как минимум 4 ГБ ОЗУ.

**Шаг 1. Только для инструктора: Загрузите файл ISO CSR1000v.**

>   **Примечание**: Только инструктор или кто-либо с соответствующими учетными данными может загружать файлы ISO с Cisco. Этот шаг должен быть выполнен инструктором.

1.  Чтобы загрузить ISO CSR1000v, перейдите по ссылке: <https://software.cisco.com/download/home/284364978/type/282046477/release/Fuji-16.9.5>

    >   **Примечание**: Чтобы загрузить программное обеспечение с cisco.com, вы должны быть активным инструктором NetAcad и иметь учетную запись CCO с активированным [контрактом Netacad Maintenance](https://www.netacad.com/portal/resources/equipment-information).

1.  Щелкните значок загрузки справа от следующего файла:

    **Cisco CSR1000V IOS XE Universal - CRYPTO ISO**

    csr1000v-universalk9.16.09.05.iso

2.  Инструктор теперь может распространять этот ISO среди студентов для использования только в курсе DevNet Associate.

**Шаг 2. Загрузите файл CSR1000v VM OVA.**

1.  Перейдите на страницу [DevNet Associate Virtual Machines (VMs)](https://www.netacad.com/portal/content/devnet-associate-virtual-machines-vms) на netacad.com.
2.  Загрузите **DEVASC_CSR1000v.zip** и запомните расположение файла.
3.  Этот файл содержит файлы шаблонов VirtualBox и VMware. Разархивируйте файл, чтобы получить доступ к виртуальным машинам.

**Шаг 3. Установите виртуальную машину CSR1000v.**

1.  Откройте Oracle VirtualBox Manager или VMware Workstation.
2.  Выберите **Файл\> Импорт** в VirtualBox или **File \> Open** в VMware. В VMware Workstation Player выберите **Player\> File\> Open**.
3.  В VirtualBox выберите файл **CSR1000v_for_VirtualBox.ova** и нажмите **Открыть** и **Импорт**.
4.  В VMware выберите файл **CSR1000v_for_VMware.ova** и нажмите **Open**.
5.  Щелкните **Import**.
6.  Теперь вы увидите, что виртуальная машина **CSR1000v** добавлена в VirtualBox или VMware.

**Шаг 4. Обновите место установки ISO.**

1.  Выберите **Настройки виртуальной машины** или щелкните **Edit virtual machine settings**.
2.  В настройках виртуальной машины перейдите к самой первой настройке привода компакт-дисков.
    -   В VirtualBox: **Настройки ВМ\> Хранилище\> Устройство компакт-дисков** (первый дисковод компакт-дисков в списке)
    -   В VMware: **VM Settings** \> первый привод **CD/DVD (IDE)** в списке.
1.  В первых настройках привода компакт-дисков замените текущий используемый файл ISO
    -   В VirtualBox: **Атрибуты\> значок компакт-диска\> Выберите файл на диске ...**
    -   В VMware: рядом с **Use ISO image file** нажмите **Browse**.
1.  Выберите файл ISO, полученный от инструктора на шаге 1.
2.  На момент написания этой лабораторной работы имя файла было **csr1000v-universalalk9.16.09.05.iso**. Однако версия (16.09.05) могла измениться, изменив имя файла.
3.  **НЕ** меняйте настройки второго привода компакт-дисков. Он используется для начальной настройки маршрутизатора.
4.  Нажмите **ОК**, чтобы сохранить настройки виртуальной машины.

**Шаг 5. Для VirtualBox на Mac и Linux убедитесь, что CSR1000v использует адаптер только для хоста.**

1.  В VirtualBox щелкните **Файл\> Диспетчер сети хоста…**
2.  Убедитесь, что существует хост-адаптер с таким именем, как **vxboxnet0**, с адресом/маской IPv4 192.168.56.1/24. Важно, чтобы ваш адаптер только для хоста находился в сети 192.168.56.0/24.
3.  Если хост-адаптер не существует, нажмите **Создать**.
4.  Если IPv4-адрес не находится на 192.168.56.1/24, выберите адаптер и нажмите **Свойства**. Измените адрес IPv4 и нажмите **Закрыть**.

**Шаг 6. Запустите CSR1000v VM.**

1.  Выберите виртуальную машину **CSR1000v** и нажмите **Пуск** или **Включение**.
2.  Если вы получили следующее сообщение, продолжайте этот шаг, в противном случае перейдите к следующему шагу:

    Не удалось запустить машину DEVASC-CSR1000v, потому что не были найдены следующие физические сетевые интерфейсы:

    VirtualBox Host-Only Ethernet Adapter (адаптер 1)

    Вы можете изменить сетевые настройки устройства или остановить его.

1.  Проверьте следующее:

    Адаптер 1 - Подключен к: **адаптеру хоста (Host only)**

1.  Щелкните **ОК**.

**Шаг 7. Процесс загрузки ВМ.**

В процессе загрузки вы увидите несколько сообщений, включая сообщение **Press any key to continue**. Вы можете игнорировать все эти сообщения.

Появится экран выбора, на котором вы можете выбрать различные параметры конфигурации. Не выбирайте никаких параметров конфигурации. Установка продолжится сама по себе.

Подождите, пока завершится процесс первоначальной установки. Это займет несколько минут при первой загрузке CSR1000v.

Вы начнете видеть сообщения IOS от различных процессов, включая %SSH, %PKI и %CRYPTO_ENGINE. Через несколько минут сообщения прекратятся.

1.  В окне виртуальной машины нажмите **Enter**.
2.  Теперь вы должны увидеть приглашение: **CSR1kv\>**.
3.  Введите `enable` для привилегированного приглашения EXEC. Нет пароля для `enable`.

    ```
    CSR1kv> enable
    CSR1kv#
    ```

### Часть 2. Проверьте связь с ВМ CSR1000v.

В этой части вы проверите связь между устройствами:

**Шаг 1. Проверьте IPv4-адрес CSR1000v.**

На виртуальной машине CSR1000v используйте команду **show ip interface brief**, чтобы проверить IPv4-адрес интерфейса GigabitEthernet1. CSR1000v, скорее всего, использует адрес IPv4 из адресного пространства **192.168.x.y**.

```
CSR1kv# show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet1       192.168.56.101  YES DHCP   up                    up      
CSR1kv#
```

**Запишите этот адрес** и используйте его в оставшейся части этой лабораторной работы, а также в других лабораторных занятиях этого курса вместо значения по умолчанию **192.168.56.101**.

**Шаг 2. Запустите виртуальную машину DEVASC.**

Если вы еще не завершили лабораторную работу **1.1.2 Лабораторная работа - установка виртуальной лабораторной среды**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC сейчас.

**Шаг 3. Отправьте эхо-запрос на виртуальную машину CSR1000v с виртуальной машины DEVASC.**

1.  Выберите виртуальную машину **DEVASC VM**.
2.  Откройте окно **Terminal**.
3.  Проверьте связь с виртуальной машиной CSR1000v по ее IPv4-адресу.

    ```
    devasc@labvm:~$ ping 192.168.56.101
    PING 192.168.56.101 (192.168.56.101) 56(84) bytes of data.
    64 bytes from 192.168.56.101: icmp_seq=1 ttl=63 time=8.04 ms
    64 bytes from 192.168.56.101: icmp_seq=2 ttl=63 time=3.08 ms
    64 bytes from 192.168.56.101: icmp_seq=3 ttl=63 time=1.04 ms
    64 bytes from 192.168.56.101: icmp_seq=4 ttl=63 time=1.08 ms
    ^C
    --- 192.168.56.101 ping statistics ---
    4 packets transmitted, 4 received, 0% packet loss, time 3010ms
    rtt min/avg/max/mdev = 1.038/3.310/8.043/2.854 ms
    devasc@labvm:~$
    ```

**Шаг 4. Установите сеанс защищенной оболочки (SSH) с CSR1000v.**

Для некоторых задач, которые вы выполните в следующих лабораторных работах, потребуется сеанс SSH с CSR1000v.

1.  Подключитесь к CSR1000v с помощью SSH от виртуальной машины DEVASC. Используйте пароль **cisco123!** для аутентификации. Обратите внимание, что вы автоматически переходите в привилегированный режим EXEC. Введите **exit**, чтобы завершить сеанс SSH.

    ```
    devasc@labvm:~$ ssh cisco@192.168.56.101
    Password: 

    *                                           *
    **                                         **
    ***                                       ***
    ***  Cisco Networking Academy             ***
    ***                                       ***
    ***  This software is provided for        ***
    ***   Educational Purposes                ***
    ***    Only in Networking Academies       ***
    ***                                       ***
    **                                         **
    *                                           *



    CSR1kv# exit
    Connection to 192.168.56.101 closed by remote host.
    Connection to 192.168.56.101 closed.
    devasc@labvm:~$
    ```

**Шаг 5. Из виртуальной машины DEVASC войдите в CSR1000v WebUI (веб-интерфейс пользователя).**

1.  Откройте веб-браузер на виртуальной машине DEVASC.
2.  В качестве URL-адреса введите: **https://192.168.56.101**.

    >   **Примечание**: Обязательно используйте https

1.  Если в вашем браузере отображается предупреждение, подобное "Your connection is not private":
    1.  Щелкните **Advanced**.
    1.  Щелкните **Proceed to 192.168.56.101 (unsafe)**.
2.  Теперь вы увидите экран LOGIN. Введите следующее:
    1.  Имя пользователя: **cisco**
    1.  Пароль: **cisco123!**
    1.  Нажмите **LOGIN NOW**.

Теперь вы увидите панель управления CSR1000v. Теперь вы получаете доступ к веб-интерфейсу CSR1000v с виртуальной машины DEVASC.

**Шаг 6. С локального компьютера войдите в CSR1000v WebUI (веб-интерфейс пользователя).**

1.  Откройте веб-браузер на локальном компьютере, а не на **DEVASC VM**.
2.  В качестве URL-адреса введите: **https://192.168.56.101**.

    >   **Примечание:** Обязательно используйте https.

1.  Если в вашем браузере отображается предупреждение, подобное «Ваше соединение не защищено»:
    1.  Щелкните **Advanced**.
    1.  Щелкните **Proceed to 192.168.56.101 (unsafe)**.
2.  Теперь вы увидите экран LOGIN. Введите следующее:
    1.  Имя пользователя: **cisco**
    1.  Пароль: **cisco123!**
    1.  Нажмите **LOGIN NOW**.

Теперь вы увидите панель управления CSR1000v. Теперь вы получаете доступ к веб-интерфейсу CSR1000v со своего локального компьютера.
