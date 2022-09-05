## Цели

* **Часть 1. Запуск виртуальной машины DEVASC**
* **Часть 2: Инициализация Git**
* **Часть 3: Размещение и фиксация файла в репозитории Git**
* **Часть 4: Управление файлом и отслеживание изменений**
* **Часть 5: Ветви и слияние**
* **Часть 6: Обработка конфликтов слияния**
* **Часть 7: Интеграция Git с GitHub**

## Предпосылки / Сценарий

В этой лабораторной работе вы изучите основы распределенной системы управления версиями Git, включая большинство функций, которые вам необходимо знать для совместной работы над программным проектом. Вы также интегрируете свой локальный репозиторий Git с облачным репозиторием GitHub.

## Необходимые ресурсы

* 1 ПК с операционной системой по вашему выбору
* Virtual Box или VMWare
* Виртуальная машина DEVASC

## Инструкции

### Часть 1: Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC сейчас.

### Часть 2: Инициализация Git

В этой части вы инициализируете репозиторий Git.

**Шаг 1: Откройте терминал в DEVASC-LABVM.**

Дважды щелкните значок эмулятора терминала на рабочем столе.

**Шаг 2: Инициализировать репозиторий Git.**

1.	Используйте команду `ls` для отображения списка текущего каталога. Помните, что команды чувствительны к регистру.

    ```
    devasc@labvm:~$ ls
    Desktop    Downloads  Music     Public  Templates
    Documents  labs       Pictures  snap    Videos
    devasc@labvm:~$
    ```

2.	Затем настройте информацию о пользователе, которая будет использоваться для этого локального репозитория. Это свяжет вашу информацию с работой, которую вы вносите в локальный репозиторий. Используйте свое имя вместо "Sample User" вместо имени в кавычках "". Используйте **@example.com** в качестве адреса электронной почты.

    > Примечание: На данном этапе эти настройки могут быть любыми. Однако при сбросе этих глобальных значений в части 7 вы будете использовать имя пользователя для своей учетной записи GitHub. Если хотите, вы можете использовать свое имя пользователя GitHub сейчас.

    ```
    devasc@labvm:~$ git config --global user.name "SampleUser"
    devasc@labvm:~$ git config --global user.email sample@example.com
    ```

3.	В любой момент вы можете просмотреть эти настройки с помощью команды `git config –list`.

    ```
    devasc@labvm:~$ git config --list
    user.name=SampleUser
    user.email=sample@example.com
    devasc@labvm:~$
    ```

4.	Используйте команду `cd`, чтобы перейти к папке **devnet-src**:

    ```
    devasc@labvm:~$ cd labs/devnet-src/
    devasc@labvm:~/labs/devnet-src$
    ```

5.	Создайте каталог **git-intro** и смените в нем каталог:

    ```
    devasc@labvm:~/labs/devnet-src$ mkdir git-intro
    devasc@labvm:~/labs/devnet-src$ cd git-intro
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

6.	Используйте команду `git init`, чтобы инициализировать текущий каталог (git-intro) как репозиторий Git. Отображаемое сообщение указывает на то, что вы создали локальный репозиторий в своем проекте, который содержится в скрытом каталоге **.git**. Здесь находится вся ваша история изменений. Вы можете увидеть это с помощью команды `ls –a`.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git init
    Initialized empty Git repository in /home/devasc/labs/devnet-src/git-intro/.git/
    devasc@labvm:~/labs/devnet-src/git-intro$ ls -a
    .  ..  .git
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

7.	В процессе работы над проектом вы захотите проверить, какие файлы были изменены. Это полезно, когда вы фиксируете файлы в репо, и вы не хотите фиксировать их все. Команда `git status` отображает измененные файлы в рабочем каталоге, подготовленные для вашей следующей фиксации.

    Это сообщение показывает вам: 

    * Что вы на ветке master. (Ветки обсуждаются позже в этой лабораторной работе)
    * Сообщение фиксации (commit, коммит) - Initial commit.
    * Нет изменений для фиксации.

    Вы увидите, что статус вашего репо изменится, как только вы добавите файлы и начнете вносить изменения.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git status
    On branch master

    No commits yet

    nothing to commit (create/copy files and use "git add" to track)
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

### Часть 3: Размещение и фиксация файла в репозитории

В этой части вы создадите файл, обработаете его и зафиксируете в репозитории Git.

**Шаг 1: Создайте файл.**

1. Репозиторий git-intro создан, но пуст. Используя команду `echo`, создайте файл **DEVASC.txt** с информацией, содержащейся в кавычках.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ echo "I am on my way to passing the Cisco DEVASC exam" > DEVASC.txt
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

2. Используйте команду `ls –la`, чтобы убедиться, что файл и каталог **.git** находятся во вводном каталоге **git**. Затем используйте `cat` для отображения содержимого **DEVASC.txt**.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ ls -la
    total 16
    drwxrwxr-x 3 devasc devasc 4096 Apr 17 20:38 .
    drwxrwxr-x 5 devasc devasc 4096 Apr 17 19:50 ..
    -rw-rw-r-- 1 devasc devasc   48 Apr 17 20:38 DEVASC.txt
    drwxrwxr-x 7 devasc devasc 4096 Apr 17 19:57 .git
    evasc@labvm:~/src/git-intro$ cat DEVASC.txt
    I am on my way to passing the Cisco DEVASC exam
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

**Шаг 2: Изучите статус репозитория.**

Проверьте статус репозитория с помощью `git status`. Обратите внимание, что Git нашел новый файл в каталоге и знает, что он не отслеживается.

```
devasc@labvm:~/labs/devnet-src/git-intro$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	DEVASC.txt

nothing added to commit but untracked files present (use "git add" to track)
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 3: Размещение файла.**

1. Затем используйте команду `git add`, чтобы «поставить» файл **DEVASC.txt**. Постановка - это промежуточный этап перед фиксацией файла в репозитории с помощью команды `git commit`. Эта команда создает моментальный снимок содержимого файла на момент ввода этой команды. Любые изменения в файле требуют еще одной команды `git add` перед фиксацией файла.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git add DEVASC.txt
    ```

2. Снова используя команду `git status`, обратите внимание на поэтапные изменения, отображаемые как "new file: DEVASC.txt".

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git status
    On branch master

    No commits yet

    Changes to be committed:
      (use "git rm --cached <file>..." to unstage)
      new file:   DEVASC.txt

    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

**Шаг 4: Фиксация файла.**

Теперь, когда вы подготовили свои изменения, вам нужно будет зафиксировать их, чтобы Git знал, что вы хотите начать отслеживать эти изменения. Зафиксируйте свой поэтапный контент как новый снимок фиксации с помощью команды `git commit`. Ключ `-m *message*` позволяет вам добавить сообщение, объясняющее внесенные вами изменения. Обратите внимание на комбинацию цифр и букв, выделенную в выводе. Это идентификатор фиксации. Каждая фиксация идентифицируется уникальным хешем SHA1. Идентификатор фиксации - это первые 7 символов полного хэша фиксации. Ваш идентификатор фиксации будет отличаться от отображаемого.

```
devasc@labvm:~/labs/devnet-src/git-intro$ git commit -m "Committing DEVASC.txt to begin tracking changes"
[master (root-commit) b510f8e] Committing DEVASC.txt to begin tracking changes
 1 file changed, 1 insertion(+)
 create mode 100644 DEVASC.txt
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 5: Просмотр истории фиксации.**

Используйте команду `git log`, чтобы показать все коммиты в истории текущей ветки. По умолчанию все коммиты выполняются в главной ветке. (Ветви будут обсуждаться позже.) Первая строка - это хэш фиксации с идентификатором фиксации в качестве первых 7 символов. Файл передается в главную ветку. Далее следуют ваше имя и адрес электронной почты, дата фиксации и сообщение, которое вы включили в фиксацию.

```
devasc@labvm:~/labs/devnet-src/git-intro$ git log
commit b510f8e5f9f63c97432d108a0413567552c07356 (HEAD -> master)
Author: Sample User <sample@example.com>
Date:   Sat Apr 18 18:03:28 2020 +0000

    Committing DEVASC.txt to begin tracking changes
devasc@labvm:~/labs/devnet-src/git-intro$
```

### Часть 4: Изменение файла и отслеживание изменений

В этой части вы измените файл, подготовите файл, зафиксируете файл и проверите изменения в репозитории.

**Шаг 1: Измените файл.**

1. Внесите изменения в DEVASC.txt с помощью команды `echo`. Обязательно используйте `>>` для добавления в существующий файла. `>` перезапишет существующий файл. Используйте команду `cat` для просмотра измененного файла.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ echo "I am beginning to understand Git!" >> DEVASC.txt
    ```

2. Используйте команду `cat` для просмотра измененного файла.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ cat DEVASC.txt
    I am on my way to passing the Cisco DEVASC exam
    I am beginning to understand Git!
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

**Шаг 2: Проверьте изменение в репозитории.**

Проверьте изменение в репозитории с помощью команды `git status`.

```
devasc@labvm:~/labs/devnet-src/git-intro$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   DEVASC.txt

no changes added to commit (use "git add" and/or "git commit -a")
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 3: Подготовьте измененный файл.**

Измененный файл необходимо будет снова подготовить, прежде чем его можно будет зафиксировать с помощью команды `git add` еще раз. 

```
devasc@labvm:~/labs/devnet-src/git-intro$ git add DEVASC.txt
```

**Шаг 4: Зафиксируйте подготовленный файл.**

Зафиксируйте подготовленный файл с помощью команды `git commit`. Обратите внимание на новый идентификатор фиксации.

```
devasc@labvm:~/labs/devnet-src/git-intro$ git commit -m "Added additional line to file"
[master 9f5c4c5] Added additional line to file
 1 file changed, 1 insertion(+)
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 5: Проверьте изменения в репозитории.**

1. Снова используйте команду `git log`, чтобы показать все коммиты. Обратите внимание, что журнал содержит запись исходной фиксации вместе с записью только что выполненной фиксации. Сначала отображается последняя фиксация. В выводе выделяется идентификатор фиксации (первые 7 символов хэша SHA1), дата/время фиксации и сообщение фиксации для каждой записи.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ `git log`
    commit 9f5c4c5d630e88abe2a873fe48144e25ebe7bd6a (HEAD -> master)
    Author: Sample User <sample@example.com>
    Date:   Sat Apr 18 19:17:50 2020 +0000

        Added additional line to file

    commit b510f8e5f9f63c97432d108a0413567552c07356
    Author: Sample User <sample@example.com>
    Date:   Sat Apr 18 18:03:28 2020 +0000

        Committing DEVASC.txt to begin tracking changes
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

2. Если у вас есть несколько записей в журнале, вы можете сравнить эти две фиксации с помощью команды `git diff`, добавив сначала исходный идентификатор фиксации, а второй - последний: `git diff` *<commit ID original> <commit ID latest>*. Вам нужно будет использовать свои идентификаторы фиксации. Знак `+` в конце, за которым следует текст, указывает содержимое, добавленное к файлу.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git diff b510f8e 9f5c4c5
    diff --git a/DEVASC.txt b/DEVASC.txt
    index 93cd3fb..085273f 100644
    --- a/DEVASC.txt
    +++ b/DEVASC.txt
    @@ -1 +1,2 @@
    I am on my way to passing the Cisco DEVASC exam
    +I am beginning to understand Git!
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

### Часть 5: Ветки и слияние

Когда создается репозиторий, файлы автоматически помещаются в ветку под названием master. По возможности рекомендуется использовать ветки, а не напрямую обновлять ветку master. Ветвление используется для того, чтобы вы могли вносить изменения в другую область, не затрагивая ветку master. Это сделано для предотвращения случайных обновлений, которые могут перезаписать существующий код.

В этой части вы создадите новую ветку, проверите ветку, внесете изменения в ветку, поставите и зафиксируете ветку, объедините изменения ветки с основной веткой, а затем удалите ветку.

**Шаг 1: Создать новую ветку**

Создайте новую ветку с именем **feature** с помощью команды `git branch` *<branch-name>*.

```
devasc@labvm:~/labs/devnet-src/git-intro$ git branch feature
```

**Шаг 2: Проверить текущую ветку**

Используйте команду `git branch` без имени ветки, чтобы отобразить все ветки для этого репозитория. `*` Рядом с главной ветвью указывает, что это текущая ветка - ветка, которая в настоящее время «извлечена».

```
devasc@labvm:~/labs/devnet-src/git-intro$ git branch
  feature
* master
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 3: Переключиться на новую ветку**

Используйте команду `git checkout` *<branch-name>*, чтобы переключиться на ветку *feature*. 

```
devasc@labvm:~/labs/devnet-src/git-intro$ git checkout feature
```

**Шаг 4: Проверить текущую ветку**

1. Убедитесь, что вы перешли на ветку *feature*, используя команду `git branch`. Обратите внимание на `*` рядом с веткой *feature*. Теперь это *рабочая ветка*.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git branch
    * feature
      master
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

2. Добавьте новую строку текста в файл DEVASC.txt, снова используя команду `echo` со знаками `>>`.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ echo "This text was added originally while in the feature branch" >> DEVASC.txt
    ```

3. Убедитесь, что строка была добавлена к файлу с помощью команды `cat`.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ cat DEVASC.txt
    I am on my way to passing the Cisco DEVASC exam
    I am beginning to understand Git!
    This text was added originally while in the feature branch
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

**Шаг 5: Поместите измененный файл в ветку функции.**

1. Поместите обновленный файл в текущую ветку *feature*. 

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git add DEVASC.txt
    ```

2. Используйте команду `git status` и обратите внимание, что измененный файл **DEVASC.txt** помещен в ветку *feature*.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git status
    On branch feature
    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
      modified:   DEVASC.txt

    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

**Шаг 6: Зафиксируйте подготовленный файл в ветке функции.**

1. Зафиксируйте подготовленный файл с помощью команды `git commit`. Обратите внимание на новый идентификатор фиксации и ваше сообщение.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git commit -m "Added a third line in feature branch"
    [feature cd828a7] Added a third line in feature branch
    1 file changed, 1 insertion(+)
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

2. Используйте команду `git log`, чтобы показать все коммиты, включая только что сделанную вами фиксацию в ветке *feature*. Предыдущая фиксация была сделана в ветке *master*.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git log
    commit cd828a73102cf308981d6290113c358cbd387620 (HEAD -> feature)
    Author: Sample User <sample@example.com>
    Date:   Sat Apr 18 22:59:48 2020 +0000

        Added a third line in feature branch

    commit 9f5c4c5d630e88abe2a873fe48144e25ebe7bd6a (master)
    Author: Sample User <sample@example.com>
    Date:   Sat Apr 18 19:17:50 2020 +0000

        Added additional line to file

    commit b510f8e5f9f63c97432d108a0413567552c07356
    Author: Sample User <sample@example.com>
    Date:   Sat Apr 18 18:03:28 2020 +0000

        Committing DEVASC.txt to begin tracking changes
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

**Шаг 7: Переключиться на ветку master.**

Переключитесь на ветку master с помощью команды `git checkout` *master* и проверьте текущую рабочую ветку с помощью команды `git branch`.

```
devasc@labvm:~/labs/devnet-src/git-intro$ git checkout master
Switched to branch 'master'
devasc@labvm:~/labs/devnet-src/git-intro$ git branch
  feature
* master
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 8: Слияние содержимого файла из feature в ветку master.**

1. Ветки часто используются при внедрении новых функций или исправлений. Они могут быть отправлены на рассмотрение членам команды, а затем после проверки могут быть перенесены в основную кодовую базу – ветку master.

    Объедините содержимое (известное как история) из ветки feature в ветку master с помощью команды `git merge` *<имя-ветки>*. Имя-ветки - это ветка, из которой истории извлекаются в текущую ветку. Вывод показывает, что один файл был изменен с одной вставленной строкой.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git merge feature
    Updating 9f5c4c5..cd828a7
    Fast-forward
    DEVASC.txt | 1 +
    1 file changed, 1 insertion(+)
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

2. Проверьте добавленный контент в файл DEVASC.txt в главной ветке с помощью команды `cat`. 

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ cat DEVASC.txt
    I am on my way to passing the Cisco DEVASC exam
    I am beginning to understand Git!
    This text was added originally while in the feature branch
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

**Шаг 9: Удаление ветки.**

1. Убедитесь, что ветка `feature` все еще доступна, с помощью команды `git branch`. 

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git branch
      feature
    * master
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

2. Удалите ветку функции с помощью команды `git branch –d` *<branch-name>*.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git branch -d feature
    Deleted branch feature (was cd828a7).
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

3. Убедитесь, что функциональная ветка больше не доступна, с помощью команды `git branch`. 

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git branch
    * master
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

### Часть 6: Обработка конфликтов слияния

Иногда может возникнуть конфликт слияния. Это когда вы, возможно, внесли перекрывающиеся изменения в файл, и Git не может автоматически объединить изменения.

В этой части вы создадите тестовую ветвь, измените ее содержимое, подготовите и зафиксируете тестовую ветвь, переключитесь на ветку master, снова измените контент, подготовите и зафиксируете ветку master, попытаетесь объединить ветки, найдете и разрешите конфликт, выполните этап и снова зафиксируете ветку master, а также проверите свою фиксацию.

**Шаг 1: Создайте новую ветку test.**

Создайте новую ветку **test**. 

```
devasc@labvm:~/labs/devnet-src/git-intro$ git branch test
```

**Шаг 2: Оформить заказ на ветку test.**

1. Оформить заказ (переключиться на) ветку **test**.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git checkout test
    Switched to branch 'test'
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

2. Убедитесь, что рабочая ветвь является **test**. 

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git branch
      master
    * test
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

**Шаг 3: Проверьте текущее содержимое DEVASC.txt.**

Проверьте текущее содержимое файла **DEVASC.txt**. Обратите внимание, что первая строка включает слово «Cisco».

```
devasc@labvm:~/labs/devnet-src/git-intro$ cat DEVASC.txt
I am on my way to passing the Cisco DEVASC exam
I am beginning to understand Git!
This text was added originally while in the feature branch
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 4: Измените содержимое DEVASC.txt в тестовой ветке.**

Используйте команду `sed`, чтобы изменить слово «Cisco» на «NetAcad» в файле **DEVASC.txt**. 

```
devasc@labvm:~/labs/devnet-src/git-intro$ sed -i 's/Cisco/NetAcad/' DEVASC.txt
```

**Шаг 5: Проверьте содержимое измененного DEVASC.txt в тестовой ветке.**

Проверьте изменение файла **DEVASC.txt**. 

```
devasc@labvm:~/labs/devnet-src/git-intro$ cat DEVASC.txt
I am on my way to passing the NetAcad DEVASC exam
I am beginning to understand Git!
This text was added originally while in the feature branch
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 6: Подготовьте и зафиксируйте тестовую ветку.**

Подготовьте и зафиксируйте файл с помощью одной команды `git commit –a`. Параметр `-a` влияет только на файлы, которые были изменены и удалены. Это не влияет на новые файлы.

```
devasc@labvm:~/labs/devnet-src/git-intro$ git commit -a -m "Change Cisco to NetAcad"
[test b6130a6] Change Cisco to NetAcad
 1 file changed, 1 insertion(+), 1 deletion(-)
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 7: Переключитесь на ветку master.**

1. Оформить заказ (переключиться на) ветку **master**. 

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git checkout master
    Switched to branch 'master'
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

2. Убедитесь, что основная ветвь является вашей текущей рабочей веткой. 

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git branch
    * master
      test
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

**Шаг 8: Измените содержимое DEVASC.txt в главной ветке.**

Используйте команду `sed`, чтобы изменить слово «Cisco» на «DevNet» в файле DEVASC.txt. 

```
devasc@labvm:~/labs/devnet-src/git-intro$ sed -i 's/Cisco/DevNet/' DEVASC.txt
```

**Шаг 9: Проверьте содержимое измененного файла DEVASC.txt в главной ветви.**

Проверьте изменение в файле.

```
devasc@labvm:~/labs/devnet-src/git-intro$ cat DEVASC.txt
I am on my way to passing the DevNet DEVASC exam
I am beginning to understand Git!
This text was added originally while in the feature branch
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 10: Подготовьте и зафиксируйте основную ветку.**

Подготовьте и зафиксируйте файл с помощью команды `git commit –a`. 

```
devasc@labvm:~/labs/devnet-src/git-intro$ git commit -a -m "Changed Cisco to DevNet"
[master 72996c0] Changed Cisco to DevNet
 1 file changed, 1 insertion(+), 1 deletion(-)
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 11: Попытка объединить ветку test с веткой master.**

Попытка объединить историю ветки **test** с веткой **master**.

```
devasc@labvm:~/labs/devnet-src/git-intro$ git merge test
Auto-merging DEVASC.txt
CONFLICT (content): Merge conflict in DEVASC.txt
Automatic merge failed; fix conflicts and then commit the result.
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 12: Найдите конфликт.**

1. Используйте команду `git log` для просмотра коммитов. Обратите внимание, что версия HEAD является веткой master. Это будет полезно на следующем этапе.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ git log
    commit 72996c09fa0ac5dd0b8ab9ec9f8530ae2c5c4eb6 (HEAD -> master)
    Author: Sample User <sample@example.com>
    Date:   Sun Apr 19 00:36:05 2020 +0000

        Changed Cisco to DevNet

    <output omitted>
    ```

2. Используйте команду `cat` для просмотра содержимого файла **DEVASC.txt**. Теперь файл содержит информацию, которая поможет вам найти конфликт. Версия HEAD (основная ветвь), содержащая слово «DevNet», конфликтует с версией тестовой ветки и словом «NetAcad».

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ cat DEVASC.txt
    \<<<<<<< HEAD
    I am on my way to passing the DevNet DEVASC exam
    =======
    I am on my way to passing the NetAcad DEVASC exam
    >>>>>>> test
    I am beginning to understand Git!
    This text was added originally while in the feature branch
    devasc@labvm:~/labs/devnet-src/git-intro$
    ```

**Шаг 13: Вручную отредактируйте файл DEVASC.txt, чтобы удалить конфликтующий текст.**

1. Используйте команду `vim` для редактирования файла. 

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ vim DEVASC.txt
    ```

2. Используйте стрелки вверх и вниз, чтобы выбрать нужную строку текста. Нажмите `dd` (удалить) в следующих выделенных строках. `dd` удалит строку, на которой находится курсор.

    ```
    \<<<<<<< HEAD
    I am on my way to passing the DevNet DEVASC exam
    =======
    I am on my way to passing the NetAcad DEVASC exam
    >>>>>>> test
    I am beginning to understand Git!
    This text was added originally while in the feature branch
    ```

3. Сохраните изменения в vim, нажав **ESC** (клавиша выхода), а затем набрав `:` (двоеточие), затем `wq` и нажмите Enter.

    ```
    ESC
    :
    wq
    <Enter or Return>
    ```

**Шаг 14: Проверьте свои правки в DEVASC.txt в ветке master.**

Подтвердите внесенные вами изменения с помощью команды `cat`. 

```
devasc@labvm:~/labs/devnet-src/git-intro$ cat DEVASC.txt
I am on my way to passing the DevNet DEVASC exam
I am beginning to understand Git!
This text was added originally while in the feature branch
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 15: Подготовьте и зафиксируйте основную ветку.**

Подготовьте и зафиксируйте DEVASC.txt в главной ветке с помощью команды `git commit –a`. 

```
devasc@labvm:~/labs/devnet-src/git-intro$ git add DEVASC.txt
devasc@labvm:~/labs/devnet-src/git-intro$ git commit -a -m "Manually merged from test branch"
[master 22d3da4] Manually merged from test branch
devasc@labvm:~/labs/devnet-src/git-intro$
```

**Шаг 16: Проверьте фиксацию.**

Используйте команду `git log`, чтобы проверить фиксацию. При необходимости вы можете использовать `q` для выхода из отображения журнала **git**.

```
devasc@labvm:~/labs/devnet-src/git-intro$ git log
commit 22d3da41e00549ce69dc145a84884af6a1697734 (HEAD -> master)
Merge: 72996c0 b6130a6
Author: Sample User <sample@example.com>

Date:   Sun Apr 19 01:09:53 2020 +0000

    manually merged from branch test
<output omitted>
```

### Часть 7: Интеграция Git с GitHub 

Пока все изменения, которые вы внесли в свой файл, были сохранены на вашем локальном компьютере. Git работает локально и не требует центрального файлового сервера или облачного хостинга. Git позволяет пользователю локально хранить файлы и управлять ими.

Хотя Git полезен для одного пользователя, интеграция локального репозитория Git с облачным сервером, таким как GitHub, полезна при работе в команде. Каждый член команды хранит копию в репозитории на своем локальном компьютере и обновляет центральный облачный репозиторий, чтобы делиться любыми изменениями.

Существует довольно много популярных сервисов Git, включая GitHub, Stash от Atlassian и GitLab. Поскольку он легко доступен, в этих примерах вы будете использовать GitHub.

**Шаг 1: Создайте учетную запись GitHub.**

Если у вас этого не было раньше, перейдите на github.com и создайте учетную запись GitHub. Если у вас есть учетная запись GitHub, перейдите к шагу 2.

**Шаг 2: Войдите в свою учетную запись GitHub. Создайте репозиторий.**

Войдите в свою учетную запись GitHub.

**Шаг 3: Создайте репозиторий.**

1. Нажмите кнопку **New repository* или щелкните значок `+` в правом верхнем углу и выберите **New repository**.

2. Создайте репозиторий, используя следующую информацию:

    * Repository name: **devasc-study-team**
    * Description: **Working together to pass the DEVASC exam**
    * Public/Private: **Private**

3. Выберите: **Create repository**

**Шаг 4: Создайте новый каталог devasc-study-team.

1. Если вы еще не находитесь в каталоге **git-intro**, перейдите в него сейчас.

    ```
    devasc@labvm:~$ cd ~/labs/devnet-src/git-intro
    ```

2. Создайте новый каталог под названием **devasc-study-team**. Каталог не обязательно должен соответствовать имени репозитория.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro$ mkdir devasc-study-team
    ```

**Шаг 5: Перейдите в каталог devasc-study-team.**

Используйте команду `cd`, чтобы изменить каталоги на **devasc-study-team**. 

  ```
  devasc@labvm:~/labs/devnet-src/git-intro$ cd devasc-study-team 
  devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$
  ```

**Шаг 6: Скопируйте файл DEVASC.**

1. Используйте команду `cp`, чтобы скопировать **DEVASC.txt** из родительского каталога **git-intro** в подкаталог **devasc-study-team**. Две точки и косая черта перед именем файла указывают на родительский каталог. Пробел и точка после имени файла указывают на копирование файла в текущий каталог с тем же именем.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ cp ../DEVASC.txt .
    ```

2. Убедитесь, что файл был скопирован с помощью команды `ls`, а содержимое файла - с помощью команды `cat`. 

    ```
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ ls
    DEVASC.txt
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ cat DEVASC.txt
    I am on my way to passing the DevNet DEVASC exam
    I am beginning to understand Git!
    This text was added originally while in the feature branch
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$
    ```

**Шаг 7: Инициализируйте новый репозиторий Git.**

1. Используйте команду `git init`, чтобы инициализировать текущий каталог (**devasc-study-team**) как репозиторий Git. Отображаемое сообщение указывает на то, что вы создали локальный репозиторий в своем проекте, который содержится в скрытом каталоге **.git**. Здесь находится вся ваша история изменений.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ git init
    Initialized empty Git repository in /home/devasc/src/git-intro/devasc-study-team/.git/
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$
    ```

2. Затем проверьте глобальные переменные **git** с помощью команды `git config –list`. 

    ```
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ git config --list
    user.name=SampleUser
    user.email=sample@example.com
    core.repositoryformatversion=0
    core.filemode=true
    core.bare=false
    core.logallrefupdates=true
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$
    ```

3. Если переменные **user.name** и **user.email** не соответствуют вашим учетным данным GitHub, измените их сейчас.

    ```
    devasc@labvm:~$ git config --global user.name "GitHub username"
    devasc@labvm:~$ git config --global user.email GitHub-email-address
    ```

**Шаг 8: Укажите репозиторий Git на репозиторий GitHub.**

1. Используйте команду `git remote add`, чтобы добавить URL-адрес Git в качестве удаленного псевдонима. Значение origin указывает на вновь созданный репозиторий на GitHub. Используйте свое имя пользователя GitHub в пути URL-адреса для *github-username*.

    > Примечание: Ваше имя пользователя чувствительно к регистру.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ git remote add origin https://github.com/github-username/devasc-study-team.git
    ```

2. Убедитесь, что **remote** работает на github.com. 

    ```
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ git remote --verbose
    origin	https://github.com/username/devasc-study-team.git (fetch)
    origin	https://github.com/username/devasc-study-team.git (push)
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$
    ```

3. Просмотрите `git log`. Ошибка указывает на отсутствие коммитов.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ git log
    fatal: your current branch 'master' does not have any commits yet
    ```

**Шаг 9: Подготовьте и зафиксируйте файл DEVASC.txt.**

1. Используйте команду `git add` для создания файла DEVASC.txt.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ git add DEVASC.txt
    ```

2. Используйте команду `git commit`, чтобы зафиксировать файл DEVASC.txt. 

    ```
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ git commit -m "Add DEVASC.txt file to devasc-study-team"
    [master (root-commit) c60635f] Add DEVASC.txt file to devasc-study-team
    1 file changed, 3 insertions(+)
    create mode 100644 DEVASC.txt
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$
    ```

**Шаг 10: Проверьте фиксацию.**

1. Используйте команду `git log`, чтобы проверить фиксацию.
 
    ```
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ git log
    commit c60635fe4a1f85667641afb9373e7f49a287bdd6 (HEAD -> master)
    Author: username <user@example.com>
    Date:   Mon Apr 20 02:48:21 2020 +0000

        Add DEVASC.txt file to devasc-study-team
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$
    ```

2. Используйте команду `git status` для просмотра информации о статусе. Фраза «working tree clean» означает, что Git сравнил ваш список файлов с тем, что вы указали Git, и это чистый лист, в котором нет ничего нового, чтобы сообщить.

    ```
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ git status
    On branch master
    nothing to commit, working tree clean
    devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$
    ```

**Шаг 2: Отправьте (push) файл из Git в GitHub.**

Используйте команду `git push origin master`, чтобы отправить (отправить) файл в репозиторий GitHub. Вам будет предложено ввести имя пользователя и пароль, которые вы использовали для создания своей учетной записи GitHub.

```
devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$ git push origin master
Username for 'https://github.com': username
Password for 'https://username@github.com': password
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Delta compression using up to 2 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 347 bytes | 347.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/username/devasc-study-team.git
 * [new branch]      master -> master
devasc@labvm:~/labs/devnet-src/git-intro/devasc-study-team$
```

> Примечание: Если после ввода имени пользователя и пароля вы получите фатальную ошибку о том, что репозиторий не найден, скорее всего, вы отправили неверный URL. Вам нужно будет отменить команду `git add` с помощью команды `git remote rm origin`.

**Шаг 3: Проверьте файл на GitHub.**

1. Перейдите в свою учетную запись GitHub и в разделе «Repositories» выберите `username/devasc-study-team`. 
2. Вы должны увидеть, что файл DEVASC.txt был добавлен в этот репозиторий GitHub. Щелкните файл, чтобы просмотреть его содержимое.
