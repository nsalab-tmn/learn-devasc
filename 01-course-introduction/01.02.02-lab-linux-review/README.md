## Введение: Linux для DevNet

Linux получил широкое распространение в серверах, устройствах Интернета вещей (IoT), сетевом оборудовании, смартфонах и многих других устройствах, о которых вы даже не можете думать как о компьютерах. От домашнего маршрутизатора, обеспечивающего ваше интернет-соединение, до сервера, на котором размещен этот курс, вероятно, что несколько устройств под управлением Linux в настоящее время позволяют вам читать эту страницу.

Но зачем вам знать о Linux? Ну, для начала, помните ту виртуальную машину, которую вы только что скачали и установили в качестве среды разработки? Это виртуальная машина на базе Linux. Все лабораторные работы по кодированию в этом курсе выполняются на виртуальной машине на базе Linux.

## Цели

* **Часть 1: Запустите виртуальную машину DEVASC**
* **Часть 2. Обзор синтаксиса команд. Навигация**
* **Часть 3. Обзор управления файлами**
* **Часть 4. Обзор регулярных выражений**
* **Часть 5. Обзор системного администрирования**

## Предыстория/сценарий

В этой лабораторной работе вы проверите базовые навыки работы с Linux, включая навигацию по командам, управление файлами, регулярные выражения и системное администрирование. Эта лабораторная работа не предназначена для замены предыдущего опыта работы с Linux и не обязательно охватывает все навыки работы с Linux, необходимые для этого курса. Тем не менее, эта лабораторная работа должна служить хорошей мерой ваших навыков работы с Linux и подсказывать вам, где вам может понадобиться дополнительная проверка.

## Требуемые ресурсы

* 1 ПК с выбранной операционной системой
* Virtual Box или VMWare
* Виртуальная машина DEVASC

## Инструкции

### Часть 1: Запустите виртуальную машину DEVASC.

Если вы еще не завершили лабораторную работу — установите лабораторную среду виртуальной машины, сделайте это сейчас. Если вы уже завершили эту лабораторную работу, запустите виртуальную машину DEVASC сейчас.

### Часть 2: Обзор синтаксиса команд навигации

В этой части вы будете использовать команды `ls`, `pwd`, `cd` и `sudo`, чтобы просмотреть синтаксис базовых команд навигации.

**Шаг 1: Откройте терминал в DEVASC-LABVM.**

1.	Дважды щелкните значок эмулятора терминала на рабочем столе, чтобы открыть окно терминала.

**Шаг 2: Навигация по каталогам.**

1.	Используйте команду `ls` для отображения списка текущего каталога. Помните, что команды чувствительны к регистру.

    <pre><code>devasc@<strong>labvm:~$ ls</strong>
    Desktop    Downloads  Music     Public  Templates
    Documents  labs       Pictures  snap    Videos
    devasc@labvm:~$
    </code></pre>

2.	Используйте команду `ls` с аргументом `labs`, чтобы отобразить содержимое папки **labs**.

    <pre><code>devasc@labvm:~$ <strong>ls labs</strong>
    devnet-src
    devasc@labvm:~$
    </code></pre>

3.	Используйте команду `ls` с параметром `-l`, чтобы включить «длинное отображение» содержимого текущего каталога.

    <pre><code>devasc@labvm:~$ <strong>ls -l</strong>
    total 40
    drwxr-xr-x 2 devasc devasc 4096 Mar 30 21:25 Desktop
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Documents
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Downloads
    drwxr-xr-x 5 devasc devasc 4096 Mar 30 21:21 labs
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Music
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Pictures
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Public
    drwxr-xr-x 5 devasc devasc 4096 Mar 30 21:24 snap
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Templates
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Videos
    devasc@labvm:~$
    </code></pre>

4.	Используйте команду `ls` с параметром `-r`, чтобы отобразить содержимое текущего каталога в обратном алфавитном порядке.

    <pre><code>devasc@labvm:~$ <strong>ls -r</strong>
    Videos     snap    Pictures  labs       Documents
    Templates  Public  Music     Downloads  Desktop
    devasc@labvm:~$
    </code></pre>

5.	Одновременно можно использовать несколько вариантов. Используйте команду `ls` с параметрами `-l` и `-r` для отображения содержимого текущего каталога как в длинном, так и в обратном порядке.

    <pre><code>devasc@labvm:~$ <strong>ls -lr</strong>
    total 40
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Videos
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Templates
    drwxr-xr-x 5 devasc devasc 4096 Mar 30 21:24 snap
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Public
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Pictures
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Music
    drwxr-xr-x 5 devasc devasc 4096 Mar 30 21:21 labs
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Downloads
    drwxr-xr-x 2 devasc devasc 4096 Apr 15 19:09 Documents
    drwxr-xr-x 2 devasc devasc 4096 Mar 30 21:25 Desktop
    devasc@labvm:~$
    </code></pre>

6.	Есть много других вариантов использования команды `ls`. Используйте команду `man` с аргументом `ls` чтобы увидеть все возможности в руководстве. Команда `man` может использоваться для поиска любой команды в системе. Используйте пробел для перехода к последующим экранам. Нажмите **q** для выхода.

    <pre><code>devasc@labvm:~$ <strong>man ls</strong>
    <strong><em>(The command line disappears and the manual page for ls opens.)</em></strong>
    LS(1)                            User Commands                           LS(1)

    NAME
        ls - list directory contents

    SYNOPSIS
        ls [OPTION]... [FILE]...

    DESCRIPTION
        List  information  about  the FILEs (the current directory by default).
        Sort entries alphabetically if none of -cftuvSUX nor --sort  is  speci‐
        fied.

        Mandatory  arguments  to  long  options are mandatory for short options
        too.

        -a, --all
                do not ignore entries starting with .

        -A, --almost-all
                do not list implied . and ..

        --author
    Manual page ls(1) line 1 (press h for help or q to quit)
    </code></pre>

7.	Вы также можете использовать аргумент `--help` после большинства команд, чтобы увидеть краткую сводку всех доступных параметров команды.

    <pre><code>devasc@labvm:~$ <strong>ls --help</strong>
    Usage: ls [OPTION]... [FILE]...
    List information about the FILEs (the current directory by default).
    Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.

    Mandatory arguments to long options are mandatory for short options too.
    -a, --all                  do not ignore entries starting with .
    -A, --almost-all           do not list implied . and ..
    (Output Omitted)
    devasc@labvm:~$
    </code></pre>

8.	Используйте команду `pwd` для отображения текущего рабочего каталога.

    <pre><code>devasc@labvm:~$ <strong>pwd</strong>
    /home/devasc
    devasc@labvm:~$
    </code></pre>

9.	Используйте команду `cd`, чтобы изменить каталог на **/home/devasc/Documents**.

    <pre><code>devasc@labvm:~$ <strong>cd Documents</strong>
    devasc@labvm:~/Documents$
    </code></pre>

10.	Используйте команду `cd` с символом `/`, чтобы перейти в корневой каталог. Снова используйте `pwd`, чтобы убедиться, что вы находитесь в корневом каталоге.

    <pre><code>devasc@labvm:~/Documents$ <strong>cd /</strong>
    devasc@labvm:/$ <strong>pwd</strong>
    /
    devasc@labvm:/$
    </code></pre>

11.	Вернитесь в каталог **/home/devasc/Documents**. Совет: Вы можете перемещать каталоги по одному или полностью до места назначения. Чтобы быстро ввести команду, введите первые несколько букв имени каталога и нажмите **Tab**, чтобы система автоматически ввела остальную часть имени. Помните, что имена чувствительны к регистру.

    <pre><code>devasc@labvm:/$ <strong>cd /home/devasc/Documents/</strong>
    devasc@labvm:~/Documents$
    </code></pre>

12.	Используйте символы `..` для перемещения вверх по одному каталогу. Используйте `pwd` еще раз, чтобы увидеть, что вы вернулись в домашний каталог пользователя.

    <pre><code>devasc@labvm:~/Documents$ <strong>cd .. </strong>
    devasc@labvm:~$ <strong>pwd</strong>
    /home/devasc
    devasc@labvm:~$
    </code></pre>

**Шаг 3: Используйте команды суперпользователя для административного доступа.**

1.	Используйте команду `sudo`, чтобы выполнить одну команду от имени пользователя **root**. Новый терминал не будет создан. Используйте команду `sudo apt-get update`, чтобы обновить список доступных пакетов, установленных на виртуальной машине. Эта команда не будет работать без использования команды `sudo`.

    > **Примечание**: Ваш вывод, скорее всего, будет другим.

    <pre><code>devasc@labvm:~$ <strong>sudo apt-get update</strong>
    Get:1 http://security.ubuntu.com/ubuntu focal-security InRelease [97.9 kB]
    Get:2 http://us.archive.ubuntu.com/ubuntu focal InRelease [265 kB]
    Get:3 http://us.archive.ubuntu.com/ubuntu focal-updates InRelease [89.1 kB]    
    Get:4 http://us.archive.ubuntu.com/ubuntu focal-backports InRelease [89.2 kB]  
    Get:5 http://us.archive.ubuntu.com/ubuntu focal/main i386 Packages [723 kB]    
    Get:6 http://us.archive.ubuntu.com/ubuntu focal/main amd64 Packages [981 kB]   
    (Output Omitted)
    Fetched 677 kB in 2s (346 kB/s)                              
    Reading package lists... Done
    devasc@labvm:~$
    </code></pre>

### Часть 3: Обзор управления файлами

В этой части вы проверите права доступа к файлам, измените разрешения и владельца файла, переместите файлы, скопируйте файлы, удалите файлы и просмотрите файлы.

**Шаг 1: Проверьте права доступа к файлам.**

1.	Используйте команду `ls Desktop -l` для отображения содержимого папки рабочего стола.

    <pre><code>devasc@labvm:~$ <strong>ls Desktop -l</strong>
    total 28
    -rwxr-xr-x 1 devasc devasc 1095 Mar 30 21:24 chromium_chromium.desktop
    -rwxr-xr-x 1 devasc devasc  401 Mar 30 21:25 cisco-packet-tracer_cisco-pacet-tracer.desktop
    -rwxr-xr-x 1 devasc devasc  776 Mar 30 21:23 code.desktop
    -rwxr-xr-x 1 devasc devasc  373 Mar 30 21:25 drawio_drawio.desktop
    -rwxr-xr-x 1 devasc devasc  250 Mar 30 21:21 exo-terminal-emulator.desktop
    -rwxr-xr-x 1 devasc devasc   99 Mar 30 21:21 labs.desktop
    -rwxr-xr-x 1 devasc devasc  334 Mar 30 21:24 postman_postman.desktop
    devasc@labvm:~$
    </code></pre>

2.	Ответьте на следующие вопросы о выводе выше. При необходимости поищите в Интернете информацию о правах доступа к файлам Linux, показанную в выводе команды `ls`.

    * Что представляет собой начальный тире в информации о разрешении?
    * Что было бы на месте тире, если бы элемент был каталогом?
    * Что означают следующие три буквы или тире в информации о разрешении?
    * Что означают три средние буквы или тире в информации о разрешении?
    * Что означают последние три буквы или тире в информации о разрешении?
    * На что указывает первый экземпляр «devasc» в информации о разрешении?
    * На что указывает второй экземпляр «devasc» в информации о разрешении?
    * Что означает тип разрешения «r»?
    * Что означает тип разрешения «w»?
    * Что означает тип разрешения «x»?

**Шаг 2: Изменить права доступа и владельца файла.**

1.	Используйте команду `cd`, чтобы перейти в каталог **Documents**.

    <pre><code>devasc@labvm:~$ <strong>cd Documents/</strong>
    devasc@labvm:~/Documents$
    </code></pre>

2.	Используйте команду `echo` для создания файла сценария оболочки, который будет содержать команду `ls ../Desktop` внутри файла. Помните, что символ «больше» (`>`) перенаправляет вывод команды в файл.

    <pre><code>devasc@labvm:~/Documents$ <strong>echo "ls ../Desktop" > myfile.sh</strong>
    devasc@labvm:~/Documents$
    </code></pre>

3.	Сценарий **myfile.sh** хранится в каталоге **/Documents**. Используйте команду `cat` для просмотра единственной команды в сценарии. Этот файл будет использоваться в качестве примера для изменения разрешений и прав собственности.

    <pre><code>devasc@labvm:~/Documents$ <strong>cat myfile.sh </strong>
    ls ../Desktop
    devasc@labvm:~/Documents$
    </code></pre>

4.	Используйте команду **./myfile.sh** для запуска скрипта. Доступ запрещен, потому что вы должны установить разрешение исполняемого файла на файл.

    <pre><code>devasc@labvm:~/Documents$ <strong>./myfile.sh</strong>
    bash: ./myfile.sh: Permission denied
    </code></pre>

5.	Используйте команду `ls -l myfile.sh` для просмотра текущих прав доступа к файлу.

    <pre><code>devasc@labvm:~/Documents$ <strong>ls -l myfile.sh</strong>
    -rw-rw-r-- 1 devasc devasc 14 Apr 16 12:46 myfile.sh
    </code></pre>

6.	Используйте команду `chmod +x myfile.sh`, чтобы позволить вам выполнить файл.

    <pre><code>devasc@labvm:~/Documents$ <strong>chmod +x myfile.sh</strong>
    devasc@labvm:~/Documents$
    </code></pre>

7.	Используйте команду `./myfile.sh` для запуска скрипта.

    <pre><code>devasc@labvm:~/Documents$ <strong>./myfile.sh</strong>
    chromium_chromium.desktop                       exo-terminal-emulator.desktop
    cisco-packet-tracer_cisco-pacet-tracer.desktop  labs.desktop
    code.desktop                                    postman_postman.desktop
    drawio_drawio.desktop
    devasc@labvm:~/Documents$
    </code></pre>

8.	Используйте команду `sudo chown root myfile.sh`, чтобы изменить владельца файла на **root**.

    <pre><code>devasc@labvm:~/Documents$ <strong>sudo chown root myfile.sh </strong>
    devasc@labvm:~/Documents$
    </code></pre>

9.	Отобразите права доступа к файлу **myfile.sh**.

    <pre><code>devasc@labvm:~/Documents$ <strong>ls -l</strong>
    total 4
    -rwxrwxr-x 1 <strong><em>root</em></strong> devasc 14 Apr 16 21:28 myfile.sh
    devasc@labvm:~/Documents$
    </code></pre>

**Шаг 3: Используйте команду перемещения файлов.**

1.	Используйте команду mv, чтобы переместить файл **myfile.sh** на рабочий стол.

    <pre><code>devasc@labvm:~/Documents$ <strong>mv myfile.sh /home/devasc/Desktop/</strong>
    devasc@labvm:~/Documents$
    </code></pre>

2.	Отобразите содержимое папки **Desktop**.

    <pre><code>devasc@labvm:~/Documents$ <strong>ls ../Desktop/</strong>
    chromium_chromium.desktop                       exo-terminal-emulator.desktop
    cisco-packet-tracer_cisco-pacet-tracer.desktop  labs.desktop
    code.desktop                                    <strong><em>myfile.sh</em></strong>
    drawio_drawio.desktop                           postman_postman.desktop
    devasc@labvm:~/Documents$
    </code></pre>

3.	Верните файл в папку **Documents**.

    <pre><code>devasc@labvm:~/Documents$ <strong>mv ../Desktop/myfile.sh myfile.sh</strong>
    devasc@labvm:~/Documents$
    </code></pre>

4.	Используйте команду `mv`, чтобы переименовать **myfile.sh** в **myfile_renamed.sh**.

    <pre><code>devasc@labvm:~/Documents$ <strong>mv myfile.sh myfile_renamed.sh</strong>
    devasc@labvm:~/Documents$ <strong>ls</strong>
    myfile_renamed.sh
    devasc@labvm:~/Documents$
    </code></pre>

**Шаг 4: Используйте команду копирования файлов.**

1.	Используйте команду `cp`, чтобы сделать копию файла **myfile_renamed.sh**.

    <pre><code>devasc@labvm:~/Documents$ <strong>cp myfile_renamed.sh myfile_renamed_and_copied.sh</strong>

    devasc@labvm:~/Documents$ <strong>ls</strong>
    <strong><em>myfile_renamed_and_copied.sh</em></strong>  myfile_renamed.sh
    devasc@labvm:~/Documents$
    </code></pre>

**Шаг 5: Используйте команду удаления файлов.**

1. Используйте команду `rm` для удаления файла **myfile_renamed_and_copied.sh**.

    <pre><code>devasc@labvm:~/Documents$ <strong>rm myfile_renamed_and_copied.sh </strong>
    devasc@labvm:~/Documents$ <strong>ls</strong>
    mbr.img  myfile_renamed.sh
    devasc@labvm:~/Documents$
    </code></pre>

**Шаг 6: Используйте стандартное перенаправление вывода.**

1.	Используйте перенаправление (`>`), чтобы поместить текст в новый файл с именем **linux.txt**.

    <pre><code>devasc@labvm:~$ <strong>echo "Linux is AWESOME!" > linux.txt</strong>
    devasc@labvm:~$
    </code></pre>

2.	Используйте команду `cat`, чтобы перенаправить содержимое **linux.txt** в другой файл.

    <pre><code>devasc@labvm:~$ <strong>cat linux.txt > linux2.txt</strong>
    devasc@labvm:~$
    </code></pre>

3.	Используйте команду `cat` для просмотра содержимого **linux2.txt**.

    <pre><code>devasc@labvm:~$ <strong>cat linux2.txt</strong>
    Linux is AWESOME!
    devasc@labvm:~$
    </code></pre>

4.	Используйте команду `echo` для добавления текста в файл **linux2.txt**.

    <pre><code>devasc@labvm:~$ <strong>echo "I LOVE Linux!" >> linux2.txt</strong>
    devasc@labvm:~$
    </code></pre>

5.	Используйте команду `cat` для просмотра содержимого файла **linux2.txt**.

    <pre><code>devasc@labvm:~$ <strong>cat linux2.txt</strong>
    Linux is AWESOME!
    I LOVE Linux!
    devasc@labvm:~$
    </code></pre>

6.	Используйте команду `echo`, чтобы перезаписать содержимое файла с помощью одинарной угловой скобки.

    <pre><code>devasc@labvm:~$ <strong>echo "Linux is POWERFUL!" > linux.txt</strong>
    devasc@labvm:~$
    </code></pre>

7.	Используйте команду `cat` для просмотра содержимого файла **linux.txt**. Обратите внимание, что предыдущее состояние «Linux is AWESOME!» было перезаписано.

    <pre><code>devasc@labvm:~$ <strong>cat linux.txt</strong>
    Linux is POWERFUL!
    devasc@labvm:~$
    </code></pre>

**Шаг 7: Воспользуйтесь текстовым редактором vi.**

1.	Используйте следующую команду, чтобы запустить текстовый редактор **vi** и открыть текстовый файл.

    <pre><code>devasc@labvm:~$ <strong>vi linux2.txt</strong>
    </code></pre>

    В окне редактора отображается следующее содержимое:

    ```
    Linux is AWESOME!
    I LOVE Linux!
    ```

2.	Используйте текстовый редактор, чтобы изменить содержимое на следующее:

    ```
    Linux is Linux
    I am AWESOME!
    ```

    Клавиша **a** позволит вам войти в режим редактирования, добавляя после позиции курсора, а клавиша **i** позволит вам войти в режим редактирования, вставляя в позицию курсора. Вам нужно будет использовать клавишу **Esc**, чтобы войти в командный режим для перемещения. Помните, что **d** удалит (вырежет), **y** скопирует (скопирует), а **p** поместит (вставит) текущую строку с курсором.

3.	Сохраните текст в новый файл с именем **linux3.txt**. Помните, что вам нужно будет находиться в командном режиме и ввести двоеточие ( `:` ), чтобы войти в режим ex, чтобы вы могли записать (сохранить) документ ( `:w linux3.txt`). Затем вы можете использовать команду `quit` (выход) ( `:q` ) для выхода из редактора vi.

4.	Используйте команду `cat` для просмотра содержимого файла **linux3.txt**.

    <pre><code>devasc@labvm:~$ <strong>cat linux3.txt</strong>
    Linux is Linux
    I am AWESOME!
    devasc@labvm:~$
    </code></pre>

### Часть 4: Обзор регулярных выражений

В этой части вы используете команду `grep`, чтобы просмотреть, как вы можете использовать регулярные выражения для фильтрации.

> **Примечание**: ваши выходные данные могут отличаться от показанных ниже, так как состояние виртуальной машины основано на последней загруженной вами итерации, а также на любых внесенных вами изменениях. Однако вы должны получить некоторый вывод из файла **passwd**, но выделенный вами вывод будет отличаться.

1.	Используйте команду `grep` для фильтрации содержимого файла **passwd**, чтобы отобразить строку из файла **passwd**, содержащую **devasc**. Обратите внимание, что два экземпляра **devasc** выделены. Также обратите внимание, что команда `grep` чувствительна к регистру. Экземпляр DEVASC не выделен.

    <pre><code>devasc@labvm:~$ <strong>grep devasc /etc/passwd</strong>
    <strong><em>devasc</em></strong>:x:900:900:DEVASC,,,:/home/<strong><em>devasc</em></strong>:/bin/bash
    devasc@labvm:~$
    </code></pre>

2.	Используйте команду `grep`, чтобы показать, сколько раз **root** появляется в файле **passwd**. Обратите внимание, что все три экземпляра **root** выделены.

    <pre><code>devasc@labvm:~$ <strong>grep root /etc/passwd</strong>
    <strong><em>root</em></strong>:x:0:0:<strong><em>root</em></strong>:/<strong><em>root</em></strong>:/bin/bash
    devasc@labvm:~$
    </code></pre>

3.	Используйте команду `grep` с символом привязки `^`, чтобы найти слово, но только в начале строки. Обратите внимание, что выделено только слово в начале строки.

    <pre><code>devasc@labvm:~$ <strong>grep '^root' /etc/passwd</strong>
    <strong><em>root</em></strong>:x:0:0:root:/root:/bin/bash
    devasc@labvm:~$
    </code></pre>

4.	Используйте команду `grep` с символом привязки `$`, чтобы найти слово в конце строки.

    <pre><code>devasc@labvm:~$ <strong>grep 'false$' /etc/passwd</strong>
    tss:x:106:114:TPM software stack,,,:/var/lib/tpm:/bin/<strong><em>false</em></strong>
    lightdm:x:107:117:Light Display Manager:/var/lib/lightdm:/bin/<strong><em>false</em></strong>
    hplip:x:115:7:HPLIP system user,,,:/run/hplip:/bin/<strong><em>false</em></strong>
    devasc@labvm:~$
    </code></pre>

5.	Используйте команду `grep` с символом привязки `.` чтобы сопоставить слова определенной длины с разными буквами в них. Обратите внимание, что выделен не только **daem**, но и **dnsm**.

    <pre><code>devasc@labvm:~$ <strong>grep 'd..m' /etc/passwd</strong>
    <strong><em>daem</em></strong>on:x:1:1:<strong><em>daem</em></strong>on:/usr/sbin:/usr/sbin/nologin
    <strong><em>dnsm</em></strong>asq:x:109:65534:<strong><em>dnsm</em></strong>asq,,,:/var/lib/misc:/usr/sbin/nologin
    avahi-autoipd:x:110:121:Avahi autoip <strong><em>daem</em></strong>on,,,:/var/lib/avahi-autoipd:/usr/sbin/nologin
    usbmux:x:111:46:usbmux <strong><em>daem</em></strong>on,,,:/var/lib/usbmux:/usr/sbin/nologin
    avahi:x:113:122:Avahi mDNS <strong><em>daem</em></strong>on,,,:/var/run/avahi-<strong><em>daem</em></strong>on:/usr/sbin/nologin
    colord:x:116:125:colord colour management <strong><em>daem</em></strong>on,,,:/var/lib/colord:/usr/sbin/nologin
    pulse:x:117:126:PulseAudio <strong><em>daem</em></strong>on,,,:/var/run/pulse:/usr/sbin/nologin
    devasc@labvm:~$
    </code></pre>

6.	Используйте команду `grep`, чтобы найти строки, в которых присутствуют только числа 8 или 9. Обратите внимание, что возвращаются только строки, содержащие 8, 9 или и то, и другое.

    <pre><code>devasc@labvm:~$ <strong>grep '[8-9]' /etc/passwd</strong>
    mail:x:<strong><em>8</em></strong>:<strong><em>8</em></strong>:mail:/var/mail:/usr/sbin/nologin
    news:x:<strong><em>9</em></strong>:<strong><em>9</em></strong>:news:/var/spool/news:/usr/sbin/nologin
    list:x:3<strong><em>8</em></strong>:3<strong><em>8</em></strong>:Mailing List Manager:/var/list:/usr/sbin/nologin
    irc:x:3<strong><em>9</em></strong>:3<strong><em>9</em></strong>:ircd:/var/run/ircd:/usr/sbin/nologin
    uuidd:x:103:10<strong><em>9</em></strong>::/run/uuidd:/usr/sbin/nologin
    devasc:x:<strong><em>9</em></strong>00:<strong><em>9</em></strong>00:DEVASC,,,:/home/devasc:/bin/bash
    systemd-network:x:<strong><em>999</em></strong>:<strong><em>999</em></strong>:systemd Network Management:/:/usr/sbin/nologin
    systemd-resolve:x:<strong><em>998</em></strong>:<strong><em>998</em></strong>:systemd Resolver:/:/usr/sbin/nologin
    systemd-timesync:x:<strong><em>99</em></strong>7:<strong><em>99</em></strong>7:systemd Time Synchronization:/:/usr/sbin/nologin
    systemd-coredump:x:,em>99</em></strong>6:<strong><em>99</em></strong>6:systemd Core Dumper:/:/usr/sbin/nologin
    rtkit:x:10<strong><em>8</em></strong>:11<strong><em>9</em></strong>:RealtimeKit,,,:/proc:/usr/sbin/nologin
    dnsmasq:x:10<strong><em>9</em></strong>:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
    devasc@labvm:~$
    </code></pre>

7.	Используйте команду `grep` для поиска буквальных символов. Обратите внимание, что возвращаются только строки, содержащие запятую.

    <pre><code>devasc@labvm:~$ <strong>grep '[,]' /etc/passwd</strong>
    devasc:x:900:900:DEVASC<strong><em>,,,</em></strong>:/home/devasc:/bin/bash
    tss:x:106:114:TPM software stack<strong><em>,,,</em></strong>:/var/lib/tpm:/bin/false
    rtkit:x:108:119:RealtimeKit<strong><em>,,,</em></strong>:/proc:/usr/sbin/nologin
    dnsmasq:x:109:65534:dnsmasq<strong><em>,,,</em></strong>:/var/lib/misc:/usr/sbin/nologin
    avahi-autoipd:x:110:121:Avahi autoip daemon<strong><em>,,,</em></strong>:/var/lib/avahi-autoipd:/usr/sbin/nologin
    usbmux:x:111:46:usbmux daemon<strong><em>,,,</em></strong>:/var/lib/usbmux:/usr/sbin/nologin
    kernoops:x:112:65534:Kernel Oops Tracking Daemon<strong><em>,,,</em></strong>:/:/usr/sbin/nologin
    avahi:x:113:122:Avahi mDNS daemon<strong><em>,,,</em></strong>:/var/run/avahi-daemon:/usr/sbin/nologin
    hplip:x:115:7:HPLIP system user<strong><em>,,,</em></strong>:/run/hplip:/bin/false
    colord:x:116:125:colord colour management daemon<strong><em>,,,</em></strong>:/var/lib/colord:/usr/sbin/nologin
    pulse:x:117:126:PulseAudio daemon<strong><em>,,,</em></strong>:/var/run/pulse:/usr/sbin/nologin
    devasc@labvm:~$
    </code></pre>

8.	Используйте команду `grep`, чтобы найти вхождения нуля или более шаблона, предшествующего ему. Обратите внимание, что возвращаются только строки с **new** и **ne**.

    <pre><code>devasc@labvm:~$ <strong>grep 'new*' /etc/passwd</strong>
    <strong><em>new</em></strong>s:x:9:9:<strong><em>new</em></strong>s:/var/spool/<strong><em>new</em></strong>s:/usr/sbin/nologin
    nobody:x:65534:65534:nobody:/no<strong><em>ne</em></strong>xistent:/usr/sbin/nologin
    messagebus:x:100:103::/no<strong><em>ne</em></strong>xistent:/usr/sbin/nologin
    _apt:x:102:65534::/no<strong><em>ne</em></strong>xistent:/usr/sbin/nologin
    tcpdump:x:104:110::/no<strong><em>ne</em></strong>xistent:/usr/sbin/nologin
    systemd-<strong><em>ne</em></strong>twork:x:999:999:systemd Network Management:/:/usr/sbin/nologin
    kernoops:x:112:65534:Ker<strong><em>ne</em></strong>l Oops Tracking Daemon,,,:/:/usr/sbin/nologin
    sa<strong><em>ne</em></strong>d:x:114:124::/var/lib/saned:/usr/sbin/nologin
    devasc@labvm:~$
    </code></pre>

### Часть 5: Обзор системного администрирования

В этой части вы ознакомитесь с основными задачами системного администрирования Linux, включая выключение компьютера, просмотр и тестирование конфигурации сети, просмотр процессов, управление пакетами установки, обновление паролей пользователей, добавление содержимого в файлы и использование текстовых редакторов.

**Шаг 1: Выключить компьютер.**

1.	Используйте команду `shutdown now`, чтобы немедленно инициировать завершение работы ОС (и виртуальной машины). Вам не нужно выполнять это действие, так как виртуальная машина выключится, и вам нужно будет перезапустить ее вручную. Форматами этого аргумента времени могут быть слово сейчас, время суток в формате `чч:мм` или количество минут для задержки в формате `+минуты`.

    <pre><code>devasc@labvm:~$ <strong>shutdown now</strong>
    </code></pre>

2.	Используйте команду `date`, чтобы проверить установленную дату ОС.

    <pre><code>devasc@labvm:~$ <strong>date</strong>
    Fri 17 Apr 2020 08:53:20 PM UTC
    devasc@labvm:~$
    </code></pre>

3.	Используйте команду `shutdown +1 "Come back soon!"` для выключения ОС через 1 минуту и отображения сообщения "Come back soon!". Обязательно отмените, иначе ваша виртуальная машина выключится.

    <pre><code>devasc@labvm:~$ <strong>shutdown +1 "Come back soon!" </strong>
    Shutdown scheduled for Fri 2020-04-17 20:57:13 UTC, use 'shutdown -c' to cancel.
    devasc@labvm:~$ <strong>shutdown -c</strong>
    devasc@labvm:~$  
    </code></pre>

**Шаг 2: Просмотрите и проверьте конфигурацию сети.**

1.	Используйте команду `ip address` для отображения конфигурации сети. Вывод немного более подробный. Например, обратите внимание, что для интерфейса **dummy0** показаны пять адресов IPv4.

    <pre><code>devasc@labvm:~$ <strong>ip address</strong>
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        link/ether 08:00:27:ce:2b:8b brd ff:ff:ff:ff:ff:ff
        inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic enp0s3
        valid_lft 75746sec preferred_lft 75746sec
        inet6 fe80::a00:27ff:fece:2b8b/64 scope link 
        valid_lft forever preferred_lft forever
    3: dummy0: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
        link/ether 46:8b:41:b5:de:aa brd ff:ff:ff:ff:ff:ff
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
        inet6 fe80::448b:41ff:feb5:deaa/64 scope link 
        valid_lft forever preferred_lft forever
    devasc@labvm:~$
    </code></pre>

2.	Используйте команду `ping` с параметрами `-c 4`, чтобы пропинговать компьютер в локальной сети четыре раза. Вы должны использовать действительный IP-адрес устройства в вашей локальной сети. В следующем примере используется адрес 192.168.1.1, но ваша сеть, скорее всего, будет иметь другие адреса IPv4.

    <pre><code>devasc@labvm:~$ <strong>ping -c 4 192.168.1.1</strong>
    PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.
    64 bytes from 192.168.1.1: icmp_seq=1 ttl=63 time=1.13 ms
    64 bytes from 192.168.1.1: icmp_seq=2 ttl=63 time=2.30 ms
    64 bytes from 192.168.1.1: icmp_seq=3 ttl=63 time=1.31 ms
    64 bytes from 192.168.1.1: icmp_seq=4 ttl=63 time=2.49 ms

    --- 192.168.1.1 ping statistics ---
    4 packets transmitted, 4 received, 0% packet loss, time 3005ms
    rtt min/avg/max/mdev = 1.130/1.809/2.492/0.594 ms
    devasc@labvm:~$
    </code></pre>

3.	Вы также можете пропинговать имя, и система доменных имен (DNS) разрешит имя в IP-адрес. Например, пропингуйте веб-сайт Cisco. Ваша виртуальная машина сначала отправит DNS-запрос, чтобы получить IP-адрес, а затем отправит ping-пакеты. Процесс DNS не отображается в выводе `ping`.

    <pre><code>devasc@labvm:~$ <strong>ping -c 4 www.cisco.com</strong>

    PING e2867.dsca.akamaiedge.net (<strong><em>23.204.11.200</em></strong>) 56(84) bytes of data.
    64 bytes from a23-204-11-200.deploy.static.akamaitechnologies.com (23.204.11.200): icmp_seq=1 ttl=58 time=185 ms
    64 bytes from a23-204-11-200.deploy.static.akamaitechnologies.com (23.204.11.200): icmp_seq=2 ttl=58 time=28.8 ms
    64 bytes from a23-204-11-200.deploy.static.akamaitechnologies.com (23.204.11.200): icmp_seq=3 ttl=58 time=28.8 ms
    64 bytes from a23-204-11-200.deploy.static.akamaitechnologies.com (23.204.11.200): icmp_seq=4 ttl=58 time=26.4 ms

    --- e2867.dsca.akamaiedge.net ping statistics ---
    4 packets transmitted, 4 received, 0% packet loss, time 3007ms
    rtt min/avg/max/mdev = 26.443/67.339/185.363/68.147 ms
    devasc@labvm:~$
    </code></pre>

**Шаг 3: Просмотр процессов**

1.	Используйте команду `ps` для отображения процессов, запущенных в текущем терминале.

    <pre><code>devasc@labvm:~$ <strong>ps</strong>
        PID TTY          TIME CMD
    1416 pts/0    00:00:00 bash
    1453 pts/0    00:00:00 ps
    devasc@labvm:~$
    </code></pre>

2.	Используйте команду `ps` с параметром `-e`, чтобы отобразить все процессы, запущенные на компьютере.

    <pre><code>
    devasc@labvm:~$ <strong>ps -e</strong>
        PID TTY          TIME CMD
        1 ?        00:00:01 systemd
        2 ?        00:00:00 kthreadd
        3 ?        00:00:00 rcu_gp
        4 ?        00:00:00 rcu_par_gp
        6 ?        00:00:00 kworker/0:0H-kblockd
        7 ?        00:00:00 kworker/0:1-events
        9 ?        00:00:00 mm_percpu_wq
    (output omitted)
    </code></pre>

3.	Вы можете направить любой вывод команды на один экран за раз, добавив `| more`. Отображается один экран вывода с `--more--` внизу. Теперь вы можете использовать клавишу **Enter** для отображения одной строки за раз, **пробел** для отображения одного экрана за раз или Ctrl+C для выхода и возврата в командную строку.

    <pre><code>devasc@labvm:~$ <strong>ps -e | more</strong>
        PID TTY          TIME CMD
        1 ?        00:00:01 systemd
        2 ?        00:00:00 kthreadd
        3 ?        00:00:00 rcu_gp
        4 ?        00:00:00 rcu_par_gp
        6 ?        00:00:00 kworker/0:0H-kblockd
        9 ?        00:00:00 mm_percpu_wq
        10 ?        00:00:00 ksoftirqd/0
    <strong><em>--More--</em></strong>
    </code></pre>

4.	Используйте команду `ps` с параметром `-ef` для более подробного отображения всех процессов, запущенных на компьютере.

    <pre><code>devasc@labvm:~$ <strong>ps -ef</strong>
    UID          PID    PPID  C STIME TTY          TIME CMD
    root           1       0  0 20:57 ?        00:00:01 /sbin/init
    root           2       0  0 20:57 ?        00:00:00 [kthreadd]
    root           3       2  0 20:57 ?        00:00:00 [rcu_gp]
    root           4       2  0 20:57 ?        00:00:00 [rcu_par_gp]
    root           6       2  0 20:57 ?        00:00:00 [kworker/0:0H-kblockd]
    root           9       2  0 20:57 ?        00:00:00 [mm_percpu_wq]
    root          10       2  0 20:57 ?        00:00:00 [ksoftirqd/0]
    root          11       2  0 20:57 ?        00:00:01 [rcu_sched]
    (output omitted)
    </code></pre>

**Шаг 4: Управление пакетами.**

1.	Используйте команду `apt-get update`, чтобы обновить список доступных пакетов в ОС, как показано ранее в части 1 этой лабораторной работы. Для использования этой команды необходимо использовать разрешения административного уровня.

    <pre><code>devasc@labvm:~$ <strong>sudo apt-get update</strong>
    Hit:1 http://security.ubuntu.com/ubuntu focal-security InRelease
    Get:2 http://us.archive.ubuntu.com/ubuntu focal InRelease [265 kB]
    Hit:3 http://us.archive.ubuntu.com/ubuntu focal-updates InRelease
    Hit:4 http://us.archive.ubuntu.com/ubuntu focal-backports InRelease
    Get:5 http://us.archive.ubuntu.com/ubuntu focal/main i386 Packages [721 kB]
    Get:6 http://us.archive.ubuntu.com/ubuntu focal/main amd64 Packages [974 kB]
    Get:7 http://us.archive.ubuntu.com/ubuntu focal/main Translation-en [506 kB]
    (output omitted)
    </code></pre>

2.	Используйте команду `apt-cache search`, чтобы найти конкретный пакет.

    <pre><code>devasc@labvm:~$ <strong>apt-cache search speed test</strong>
    (output omitted)
    smalt-examples - Sequence Mapping and Alignment Tool (examples)
    speedtest-cli - Command line interface for testing internet bandwidth using speedtest.net
    sup - Software Upgrade Protocol implementation
    sysbench - multi-threaded benchmark tool for database systems
    tcpreplay - Tool to replay saved tcpdump files at arbitrary speeds (output omitted)
    </code></pre>

3.	Используйте команду `apt-get install` для установки пакета.

    <pre><code>devasc@labvm:~$ <strong>sudo apt-get install speedtest-cli</strong>
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    The following NEW packages will be installed:
    speedtest-cli
    0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
    Need to get 23.9 kB of archives.
    After this operation, 106 kB of additional disk space will be used.
    Get:1 http://archive.ubuntu.com/ubuntu focal/universe amd64 speedtest-cli all 2.1.2-2 [23.9 kB]
    Fetched 23.9 kB in 1s (43.9 kB/s)        
    Selecting previously unselected package speedtest-cli. 
    (output omitted)
    </code></pre>

4.	Теперь вы можете использовать команду `speedtest-cli` для проверки текущей скорости интернет-соединения.

    <pre><code>devasc@labvm:~$ <strong>speedtest-cli</strong>
    Retrieving speedtest.net configuration...
    Testing from Cable Company (192.168.100.21)...
    Retrieving speedtest.net server list...
    Selecting best server based on ping...
    Hosted by Comcast (Albuquerque, NM) [494.76 km]: 48.636 ms
    Testing download speed................................................................................
    Download: 90.87 Mbit/s
    Testing upload speed......................................................................................................
    Upload: 17.87 Mbit/s
    devasc@labvm:~$
    </code></pre>

5.	Используйте команду `apt-get upgrade`, чтобы обновить все пакеты и зависимости на компьютере.

    <pre><code>devasc@labvm:~$ <strong>sudo apt-get upgrade </strong>
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    Calculating upgrade... Done
    The following packages have been kept back:
    libnss-systemd libpam-systemd libsystemd0 libyelp0 linux-generic linux-headers-generic
    (output omitted)
    </code></pre>

6.	Используйте команду `apt-get purge`, чтобы полностью удалить пакет с компьютера.

    <pre><code>devasc@labvm:~$ <strong>sudo apt-get purge speedtest-cli</strong>
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    The following packages will be REMOVED:
    speedtest-cli*
    0 upgraded, 0 newly installed, 1 to remove and 0 not upgraded.
    After this operation, 106 kB disk space will be freed.
    Do you want to continue? [Y/n] 
    (Reading database ... 211937 files and directories currently installed.)
    Removing speedtest-cli (2.1.2-2) ... 
    (output omitted)
    </code></pre>

**Шаг 5: Обновление паролей**

1.	Используйте команду `passwd` для обновления пароля.

    > Примечание: Если вы действительно меняете пароль для своего пользователя `devasc`, убедитесь, что вы его помните.

    <pre><code>devasc@labvm:~$ <strong>passwd</strong>
    Changing password for devasc.
    Current password: 
    New password: 
    Retype new password: 
    passwd: password updated successfully
    devasc@labvm:~$
    </code></pre>

2.	Используйте команду `passwd` с параметром `-S` для просмотра статуса вашего пароля.

    <pre><code>devasc@labvm:~$ <strong>passwd -S</strong>
    devasc P 04/17/2020 0 99999 7 -1
    devasc@labvm:~$
    </code></pre>

3.	Используйте страницы руководства для команды `passwd` (`man passwd`), чтобы изучить параметр `-S` и найти ответы на следующие вопросы.

    * Каков текущий статус пароля?
    * Какое минимальное количество дней должно пройти, прежде чем пароль можно будет изменить?
    * Через сколько дней после истечения срока действия пароля учетная запись остается активной?


