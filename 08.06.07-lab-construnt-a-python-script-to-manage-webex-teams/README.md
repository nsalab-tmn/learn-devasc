## Цели

-   **Часть 1. Запуск виртуальной машины DEVASC**
-   **Часть 2: Получите свой токен доступа Webex Teams**
-   **Часть 3: Проверьте свой токен доступа**
-   **Часть 4. Управление людьми в Webex Teams**
-   **Часть 5: Управление помещениями в Webex Teams**
-   **Часть 6: Управление членством в Webex Teams**
-   **Часть 7. Управление сообщениями в Webex Teams**

## Предпосылки / Сценарий

В этой лабораторной работе вы будете использовать API Webex Teams для проверки подлинности, управления людьми, управления комнатами, управления членством в комнатах и отправки сообщений.

## Необходимые ресурсы

-   1 компьютер с операционной системой по вашему выбору, на которой установлен Webex Teams
-   Virtual Box или VMWare
-   Виртуальная машина DEVASC

## Инструкции

### Часть 1. Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC сейчас.

>  **Примечание**: Часть **лабораторной работы «Установка виртуальной лабораторной среды»**. Лабораторная среда предназначена для установки приложения Webex Teams и добавления как минимум еще одного человека в список контактов. Это необходимо выполнить, прежде чем продолжить эту лабораторную работу.

### Часть 2. Получите свой токен доступа Webex Teams

В этой части вы регистрируетесь или входите в свою учетную запись Webex, изучаете документацию по API, получаете свой токен доступа, а затем тестируете свой токен доступа, который вы будете использовать в вызовах API.

**Шаг 1. Войти в Webex**

1.  Откройте веб-браузер Chromium.
1.  Перейдите на веб-сайт разработчика Webex: https://developer.webex.com/
1.  Войдите, если у вас уже есть аккаунт. Если нет, пройдите процесс регистрации.

**Шаг 2. Изучите документацию по API.**

1.  Щелкните **Documentation**.
1.  Webex имеет множество API, которые вы можете использовать в своих приложениях. Щелкните **API Reference**, чтобы развернуть подменю.
1.  Изучите все разновидности вызовов API. В этой лабораторной работе вы будете использовать документацию API для **People**, **Rooms**, **Membership** и **Message**.

**Шаг 3. Получите свой токен доступа.**

1.  По-прежнему находясь в **Documentation**, при необходимости прокрутите страницу вверх и нажмите **Getting Started** в разделе **REST API**.
1.  Обратите внимание, что в разделе **Accounts and Authentication** Webex поддерживает персональный токен доступа. Токен аутентификации требуется для всех вызовов REST API. Щелкните значок Копировать под своим токеном личного доступа.

    > **Примечание**: Токен личного доступа предоставляет доступ к вашей учетной записи любому, кто его знает. Обязательно держите это в секрете.

    > **Примечание**: Вы получите сообщение о том, что токен действителен в течение определенного периода времени, который составлял 12 часов на момент написания этой лабораторной работы. Вам нужно будет получить новый токен и обновить скрипты Python, если вы вернетесь в эту лабораторную работу после истечения срока действия токена.

1.  Скопируйте свой личный токен доступа в текстовый файл для дальнейшего использования в этой лабораторной работе.

### Часть 3. Проверьте свой токен доступа

Вы можете проверить свой токен доступа в документации OpenAPI на сайте разработчика. Однако вы будете использовать свой токен в скриптах Python. Следовательно, вы также должны проверить, работает ли он в сценарии.

**Шаг 1. Протестируйте свой токен доступа на сайте разработчика.**

Вы можете проверить свой токен доступа в документации OpenAPI по адресу https://developer.webex.com.

1.  Вернитесь в браузер и при необходимости нажмите **Documentation**.
1.  В разделе **API Reference** щелкните **People**, а затем **Get My Own Details**.
1.  Обратите внимание на то, что на панели **Try it** справа, ваш токен уже заполнен.
1.  Вы можете нажать **Try it** или **Run**, чтобы проверить свой доступ. Вы увидите **Response** с вашей личной информацией.
2.  Нажмите **Request**, чтобы увидеть полный URL-адрес, использованный для отправки запроса GET. Вы будете использовать этот URL-адрес на следующем этапе своего скрипта Python.
3.  В среднем разделе вы можете просмотреть всю документацию по **Response Properties**.

**Шаг 2. Используйте сценарий Python для проверки вашего токена доступа.**

1.  Открытый код VS. Затем щелкните **File \> Open** **Folder...** и перейдите в каталог **devnet-src/webex-teams**. Щелкните **ОК**.
1.  На панели **EXPLORER** вы должны теперь увидеть все файлы-заполнители .py, которые вы будете использовать в этой лабораторной работе. Щелкните файл **authentication.py**.
1.  Поместите в файл следующий код. Обязательно замените **your_token_here** своим личным токеном доступа, который вы скопировали на предыдущем шаге.

    ```python
    import requests
    import json

    access_token = 'your_token_here'
    url = 'https://webexapis.com/v1/people/me'
    headers = {
        'Authorization': 'Bearer {}'.format(access_token)
    }
    res = requests.get(url, headers=headers)
    print(json.dumps(res.json(), indent=4))
    ```

1.  Сохраните и запустите файл. Вы должны получить тот же результат, что и в документации OpenAPI.

    > **Примечание**: Значения некоторых ключей были усечены в выходных данных ниже.

    ```json
    devasc@labvm:~/labs/devnet-src/webex-teams$ python3 authentication.py 
    {
        "id": "Y2lz...UyM2U",
        "emails": [
            "your-email@example.com"
        ],
        "phoneNumbers": [],
        "displayName": "Your-First-Name Your-Last-Name",
        "nickName": "Your-Nick-Name",
        "firstName": " Your-First-Name",
        "lastName": "Your-Last-Name",
        "avatar": "https://9643-417f-9974...6baa4~1600",
        "orgId": "Y2lzY2...UxMGY",
        "created": "2012-06-15T20:23:12.529Z",
        "lastActivity": "2020-06-02T20:16:52.111Z",
        "status": "active",
        "type": "person"
    }
    devasc@labvm:~/labs/devnet-src/webex-teams$
    ```

### **Часть 4. Управление людьми в Webex Teams**

В Webex Teams люди - это зарегистрированные пользователи. Через API вы можете получить список людей, вы можете создать человека, получить данные об отдельном человеке, обновить человека и удалить человека.

**Шаг 1. Найдите в документации по API список сведений о зарегистрированном пользователе Webex Teams.**

1.  Вернитесь на сайт developer.webex.com. В разделе **API Reference** \> **People** выберите **Method** для **List People**.
1.  В разделе **Query Parameters** найдите параметр **email**. Это параметр, который вы будете использовать для поиска конкретного пользователя в вашей организации. В качестве альтернативы вы можете использовать параметр **displayName**, если знаете точное имя. Вы можете использовать функцию **Try it**.

**Шаг 2. Используйте сценарий Python для вывода сведений о зарегистрированном пользователе Webex Teams.**

1.  В VS Code щелкните файл **list-people.py**.
1.  Поместите в файл следующий код. Обязательно замените **your_token_here** своим личным токеном доступа, а **user@example.com** - фактическим зарегистрированным пользователем Webex Team в вашей организации.

    ```python
    import requests
    import json

    access_token = 'your_token_here'
    url = 'https://webexapis.com/v1/people'
    headers = {
        'Authorization': 'Bearer {}'.format(access_token),
        'Content-Type': 'application/json'
    }
    params = {
        'email': 'user@example.com'
    }
    res = requests.get(url, headers=headers, params=params)
    print(json.dumps(res.json(), indent=4))
    ```

1.  Сохраните и запустите сценарий. Вы должны получить тот же результат, что и следующий. Если вы получили сообщение типа **{'message': 'Invalid email address.'...**, это означает, что вы не заменили пустой параметр электронной почты допустимым адресом электронной почты для зарегистрированного пользователя Webex Teams. Значение ключа **id** будет использовано в следующем вызове API.

    > **Примечание**: Значения некоторых ключей были усечены в выходных данных ниже.

    ```json
    devasc@labvm:~/labs/devnet-src/webex-teams$ python3 list-people.py 
    {
        "notFoundIds": null,
        "items": [
            {
                "id": "Y2l...2I", # You will use this value in the next step
                "emails": [
                    "user@example.com"
                ],
                "phoneNumbers": [
                    {
                        "type": "mobile",
                        "value": "+1234567690"
                    }
                ],
                "displayName": "displayName",
                "nickName": "nickName",
                "firstName": "firstName",
                "lastName": "lastName",
                "avatar": "https://9643-417f-9974...6baa4~1600",
                "orgId": "Y2lzY...UxMGY",
                "created": "2012-06-15T20:39:19.726Z",
                "lastActivity": "2020-06-04T13:57:01.688Z",
                "status": "active",
                "type": "person"
            }
        ]
    }
    devasc@labvm:~/labs/devnet-src/webex-teams$
    ```

**Шаг 3. Перечислите дополнительные административные данные для человека.**

1.  Если вы являетесь администратором Webex Teams, вы можете получить дополнительную информацию о человеке, используя значение ключа **id** человека в вызове API. Добавьте следующий код в свой скрипт **list-people.py**. Замените **previous_id_here** значением **id** из предыдущего вызова API.

    ```python
    person_id = 'previous_id_here'
    url = 'https://webexapis.com/v1/people/{}'.format(person_id)
    headers = {
        'Authorization': 'Bearer {}'.format(access_token),
        'Content-Type': 'application/json'
    }
    res = requests.get(url, headers=headers)
    print(json.dumps(res.json(), indent=4))
    ```

1.  Сохраните файл и запустите его. Как неадминистратор, вы получите информацию, очень похожую на предыдущую.

    ```json
    devasc@labvm:~/labs/devnet-src/webex-teams$ python3 list-people.py 
    {
        <first API call output omitted>
    }
    {
        "id": "Y2l...2I",
        "emails": [
            "user@example.com"
        ],
        "phoneNumbers": [
            {
                "type": "mobile",
                "value": "+1234567890"
            }
        ],
        "displayName": "displayName",
        "nickName": "nickName",
        "firstName": "firstName",
        "lastName": "lastName",
        "avatar": "https://9643-417f-9974...6baa4~1600",
        "orgId": "Y2l...MGY",
        "created": "2012-06-15T20:39:19.726Z",
        "lastActivity": "2020-06-04T14:39:36.535Z",
        "status": "active",
        "type": "person"
    }
    devasc@labvm:~/labs/devnet-src/webex-teams$
    ```

### Часть 5. Управление комнатами в Webex Teams

Комнаты, также называемые пространствами в пользовательском интерфейсе, позволяют людям отправлять сообщения и файлы для совместной работы в местах коллективных встреч. В этой части вы составите список комнат, создадите комнату и получите подробную информацию о ней.

**Шаг 1. Найдите и изучите документацию по API для комнат.**

1.  Вернитесь на сайт developer.webex.com. В разделе **API Reference** щелкните **Rooms**.
1.  Изучите различные вызовы API, которые вы можете выполнять с помощью **Rooms** API.
1.  Щелкните запрос GET для **List Rooms** и изучите **Query Parameters**.

**Шаг 2. Используйте сценарий Python, чтобы перечислить все комнаты для аутентифицированного пользователя.**

1.  Для этого шага вы должны быть участником как минимум одной комнаты. Разговор с еще одним человеком считается комнатой в Webex Teams.
1.  В VS Code щелкните файл **list-rooms.py**.
1.  Поместите в файл следующий код. Обязательно замените **your_token_here** своим личным токеном доступа.

    ```python
    import requests

    access_token = 'your_token_here'  
    url = 'https://webexapis.com/v1/rooms'
    headers = {
        'Authorization': 'Bearer {}'.format(access_token),
        'Content-Type': 'application/json'
    }
    params={'max': '100'}
    res = requests.get(url, headers=headers, params=params)
    print(res.json())
    ```

1.  Сохраните и запустите файл. Ваш результат будет отличаться от следующего. Здесь указан только один номер. Значения идентификаторов были усечены.

    ```python
    devasc@labvm:~/labs/devnet-src/webex-teams$ python3 list-rooms.py 
    {'items': [{'id': 'Y2l...ZTE0', 'title': 'User Name', 'type': 'direct', 'isLocked': False, 'lastActivity': '2020-06-01T16:34:56.536Z', 'creatorId': 'Y2lz...yM2U', 'created': '2020-06-01T16:30:21.816Z', 'ownerId': 'Y2lz...xMGY'}
    # additional rooms displayed up to 'max' value.
    ]}
    devasc@labvm:~/labs/devnet-src/webex-teams$
    ```

**Шаг 3. Найдите и изучите документацию по API для публикации в Rooms API.**

1.  Вернитесь на сайт developer.webex.com. В разделе **API Reference** щелкните **Rooms**, если необходимо.
1.  В API комнат есть один метод POST для **Create a Room**. Щелкните ссылку, чтобы просмотреть доступные **Query Parameters**. В вашем скрипте вы будете использовать обязательный параметр **title**.

**Шаг 4. Используйте скрипт Python для создания комнаты.**

1.  В VS Code щелкните файл **create-rooms.py**.
1.  Поместите в файл следующий код. Обязательно замените **your_token_here** своим личным токеном доступа. Обратите внимание, что это запрос POST и использует параметр **title**.

    ```python
    import requests

    access_token = 'your_token_here'
    url = 'https://webexapis.com/v1/rooms'
    headers = {
        'Authorization': 'Bearer {}'.format(access_token),
        'Content-Type': 'application/json'
    }
    params={'title': 'DevNet Associate Training!'}
    res = requests.post(url, headers=headers, json=params)
    print(res.json())
    ```

1.  Сохраните и запустите файл. Вы должны получить ответ, подобный следующему. Значения идентификаторов были усечены. Выделены **id** комнаты и **title**. Скопируйте **room ID** и сохраните его в текстовом файле для использования в оставшейся части этой лабораторной работы.

    ```python
    devasc@labvm:~/labs/devnet-src/webex-teams$ python3 create-rooms.py 
    {'id': 'Y2l...GNm', 'title': 'DevNet Associate Training!', 'type': 'group', 'isLocked': False, 'lastActivity': '2020-06-04T16:50:19.371Z', 'creatorId': 'Y2l...M2U', 'created': '2020-06-04T16:50:19.371Z', 'ownerId': 'Y2l...MGY'}
    devasc@labvm:~/labs/devnet-src/webex-teams$
    ```

1.  Убедитесь, что в приложении Webex Teams вы просматриваете курс **DevNet Associate Training!** комната. В настоящее время вы единственный участник.

**Шаг 5. Используйте скрипт Python, чтобы получить подробную информацию о комнате.**

Что, если вы хотите начать встречу в своей новой комнате? Вы можете выполнить еще один вызов GET, чтобы получить адрес протокола инициации сеанса (SIP), URL-адрес собрания и телефонные номера для дозвона.

1.  В VS Code щелкните файл **get-room-details.py**.
1.  Поместите в файл следующий код. Замените **your_token_here** своим личным токеном доступа. Замените **your_room_id** значением, полученным на предыдущем шаге.

    ```python
    import requests

    access_token = 'your_token_here'
    room_id = 'your_room_id'
    url = 'https://webexapis.com/v1/rooms/{}/meetingInfo'.format(room_id)
    headers = {
        'Authorization': 'Bearer {}'.format(access_token),
        'Content-Type': 'application/json'
    }
    res = requests.get(url, headers=headers)
    print(res.json())
    ```

1.  Сохраните и запустите файл. Вы должны получить ответ, подобный следующему. Значения были усечены.

    ```python
    devasc@labvm:~/labs/devnet-src/webex-teams$ python3 get-room-details.py 
    {'roomId': 'Y2l...GNm', 'meetingLink': 'https://cisco.webex.com/m/3272...a837', 'sipAddress': '162...468@cisco.webex.com', 'meetingNumber': '162...0468', 'callInTollFreeNumber': '+1-866-...-9903', 'callInTollNumber': '+1-408-...-6800'}
    devasc@labvm:~/labs/devnet-src/webex-teams$
    ```

### Часть 6. Управление членством в Webex Teams

В этой части вы будете использовать API членства, чтобы добавить кого-нибудь в свою комнату.

**Шаг 1. Найдите и изучите документацию по API для членства.**

1.  Вернитесь на сайт developer.webex.com. В разделе **API Reference** щелкните **Memberships**.
1.  Изучите различные вызовы API, которые можно выполнить с помощью API **Memberships**.
1.  Щелкните запрос GET для **List Memberships** и изучите **Query Parameters**.

**Шаг 2. Используйте скрипт Python для составления списка участников комнаты.**

1.  В VS Code щелкните файл **list-memberships.py**.
1.  Поместите в файл следующий код. Замените **your_token_here** своим личным токеном доступа. Замените **your_room_id** значением, полученным в предыдущей части.

    ```python
    import requests

    access_token = 'your_token_here'
    room_id = 'your_room_id'
    url = 'https://webexapis.com/v1/memberships'
    headers = {
        'Authorization': 'Bearer {}'.format(access_token),
        'Content-Type': 'application/json'
    }
    params = {'roomId': room_id}
    res = requests.get(url, headers=headers, params=params)
    print(res.json())
    ```

1.  Сохраните и запустите файл. Вы должны получить ответ, подобный следующему. Вы должны быть единственным участником, если вы не добавили кого-то в приложение Webex Teams. Значения идентификаторов были усечены.

    ```python
    devasc@labvm:~/labs/devnet-src/webex-teams$ python3 list-memberships.py 
    {'items': [{'id': 'Y2l...RjZg', 'roomId': 'Y2l...GNm', 'personId': 'Y2l...M2U', 'personEmail': 'user@example.com', 'personDisplayName': 'personDisplayName', 'personOrgId': 'Y2l...MGY', 'isModerator': False, 'isMonitor': False, 'created': '2020-06-04T16:50:19.819Z'}]}
    devasc@labvm:~/labs/devnet-src/webex-teams$
    ```

**Шаг 3. Найдите и изучите документацию по API для публикации в API членства.**

1.  Вернитесь на сайт developer.webex.com. В разделе **API Reference** щелкните **Memberships**, если необходимо.
1.  API членства имеет один метод POST для **Create a Membership**. Щелкните ссылку, чтобы просмотреть доступные **Query Parameters**. В вашем скрипте вы будете использовать необходимые параметры **roomID** и **personEmail**.

**Шаг 4. Используйте сценарий Python для создания членства, добавляя кого-то в комнату.**

Для этого шага вам понадобится адрес электронной почты другого человека, который является зарегистрированным пользователем Webex Teams в вашей организации. Вы можете использовать тот же адрес электронной почты, который вы использовали ранее, чтобы перечислить подробности о человеке.

1.  В VS Code щелкните файл **create-membership.py**.
1.  Поместите в файл следующий код. Замените **your_token_here** своим личным токеном доступа. Замените **your_room_id** значением, полученным в предыдущей части. Замените **new-user@example.com** на адрес электронной почты человека, которого вы хотите добавить в комнату.

    ```python
    import requests

    access_token = 'your_token_here'
    room_id = 'your_room_id'
    person_email = 'new-user@example.com'
    url = 'https://webexapis.com/v1/memberships'
    headers = {
        'Authorization': 'Bearer {}'.format(access_token),
        'Content-Type': 'application/json'
    }
    params = {'roomId': room_id, 'personEmail': person_email}
    res = requests.post(url, headers=headers, json=params)
    print(res.json())
    ```

1.  Сохраните и запустите файл. Вы должны получить ответ, подобный следующему. Вы должны быть единственным участником, если вы не добавили кого-то в приложение Webex Teams. Значения идентификаторов были усечены.

    ```python
    devasc@labvm:~/labs/devnet-src/webex-teams$ python3 list-memberships.py 
    {'items': [{'id': 'Y2l...RjZg', 'roomId': 'Y2l...GNm', 'personId': 'Y2l...M2U', 'personEmail': 'user@example.com', 'personDisplayName': 'personDisplayName', 'personOrgId': 'Y2l...MGY', 'isModerator': False, 'isMonitor': False, 'created': '2020-06-04T16:50:19.819Z'}]}
    devasc@labvm:~/labs/devnet-src/webex-teams$
    ```

### Часть 7. Управление сообщениями в Webex Teams

В Webex Teams сообщение может содержать обычный текст, Markdown или вложение файла. Каждое сообщение отображается в отдельной строке вместе с отметкой времени и информацией об отправителе. Вы можете использовать этот API сообщений для составления списка, создания и удаления сообщений. В этой части вы отправите сообщение в комнату, которую вы создали в этой лабораторной работе.

**Шаг 1. айдите и изучите документацию API на наличие сообщений.**

1.  Вернитесь на сайт developer.webex.com. В разделе **API Reference** щелкните **Messages**.
1.  Изучите различные вызовы API, которые можно выполнять с помощью API **Messages**.
1.  Щелкните запрос POST для **Create a Message** и изучите **Query Parameters**. Обратите внимание, что для простого текстового сообщения вы можете использовать параметр **text** или **markdown**. На этом шаге вы укажете сообщение с форматированием Markdown. Поищите в Интернете информацию о Markdown.

**Шаг 2. Используйте сценарий Python для отправки сообщения в комнату Webex.**

1.  В VS Code щелкните файл **creat-markdown-message.py**.
1.  Поместите в файл следующий код. Замените **your_token_here** своим личным токеном доступа. Замените **your_room_id** значением, полученным в предыдущей части.

    ```python
    import requests

    access_token = 'your_token_here'
    room_id = 'your_room_id'
    message = 'Hello **DevNet Associates**!!'
    url = 'https://webexapis.com/v1/messages'
    headers = {
        'Authorization': 'Bearer {}'.format(access_token),
        'Content-Type': 'application/json'
    }
    params = {'roomId': room_id, 'markdown': message}
    res = requests.post(url, headers=headers, json=params)
    print(res.json())
    ```

1.  Сохраните и запустите файл. Вы должны получить ответ, подобный следующему. Обратите внимание, что Markdown был преобразован в HTML. Значения идентификаторов были усечены.

    ```python
    devasc@labvm:~/labs/devnet-src/webex-teams$ python3 create-markdown-message.py 
    {'id': 'Y2l...RjZg', 'roomId': 'Y2l...GNm', 'roomType': 'group', 'text': 'Hello DevNet Associates!!', 'personId': 'Y2l...M2U', 'personEmail': 'user@example.com', 'markdown': 'Hello **DevNet Associates**!!', 'html': '<p>Hello <strong>DevNet Associates</strong>!!</p>', 'created': '2020-06-04T19:08:49.145Z'}
    devasc@labvm:~/labs/devnet-src/webex-teams$
    ```
