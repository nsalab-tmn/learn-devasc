## Цели

-   **Часть 1: Запуск виртуальной машины DEVASC**
-   **Часть 2: Демонстрация приложения MapQuest Directions**
-   **Часть 3. Получите ключ API MapQuest**
-   **Часть 4: Создание базового приложения MapQuest Direction**
-   **Часть 5: Обновление приложения MapQuest Directions с дополнительными функциями**
-   **Часть 6: Проверка полной функциональности приложения**

## Предпосылки / Сценарий

В этой лабораторной работе вы создадите приложение в Visual Studio Code (VS Code), которое извлекает данные JSON из API MapQuest Directions, анализирует данные и форматирует их для вывода пользователю. Вы будете использовать запрос GET Route из API MapQuest Directions. Ознакомьтесь с документацией GET Route Directions API здесь:

<https://developer.mapquest.com/documentation/directions-api/route/get/>

>   **Примечание**: Если указанная выше ссылка больше не работает, найдите «Документация по API MapQuest».

## Необходимые ресурсы

-   1 ПК с операционной системой по вашему выбору
-   Virtual Box или VMWare
-   Виртуальная машина DEVASC

## Инструкции

### Часть 1. Запустите виртуальную машину DEVASC

Если вы еще не завершили **лабораторную работу «Установка виртуальной лабораторной среды»**, сделайте это сейчас. Если вы уже выполнили эту лабораторную работу, запустите виртуальную машину DEVASC сейчас.

### Часть 2. Продемонстрируйте приложение MapQuest Directions

Ваш инструктор может продемонстрировать приложение MapQuest Directions и показать вам сценарий, использованный для его создания. Однако в этой лабораторной работе вы создадите этот сценарий шаг за шагом.

Приложение запрашивает начальную точку и пункт назначения. Затем он запрашивает данные JSON из API MapQuest Directions, анализирует их и отображает полезную информацию.

```
>>>
Starting Location: Washington
Destination: Baltimore
URL: https://www.mapquestapi.com/directions/v2/route?key=your_api_key&from=Washington&to=Baltimore
API Status: 0 = A successful route call.

Directions from Washington to Baltimore
Trip Duration:   00:49:19
Kilometers:      61.32
Fuel Used (Ltr): 6.24
=============================================
Start out going north on 6th St/US-50 E/US-1 N toward Pennsylvania Ave/US-1 Alt N. (1.28 km)
Turn right onto New York Ave/US-50 E. Continue to follow US-50 E (Crossing into Maryland). (7.51 km)
Take the Balt-Wash Parkway exit on the left toward Baltimore. (0.88 km)
Merge onto MD-295 N. (50.38 km)
Turn right onto W Pratt St. (0.86 km)
Turn left onto S Calvert St/MD-2. (0.43 km)
Welcome to BALTIMORE, MD. (0.00 km)
=============================================

Starting Location: quit
>>>
```

>   **Примечание**: Чтобы увидеть JSON для вышеуказанного вывода, вы можете скопировать URL-адрес во вкладке браузера. Однако вам нужно будет заменить **your_api_key** на ключ API MapQuest, который вы получили в части 3.

### Часть 3. Получите ключ API MapQuest

Перед созданием приложения вам необходимо выполнить следующие шаги, чтобы получить ключ API MapQuest.

1.  Перейдите на: <https://developer.mapquest.com/>.
2.  Нажмите **Sign Up** вверху страницы.
3.  Заполните форму, чтобы создать новую учетную запись. В поле **Company** введите **Cisco Networking Academy Student**.
4.  После нажатия кнопки **Sign Me Up** вы будете перенаправлены на страницу **Manage Keys**. Если вы перенаправлены в другое место, щелкните **Manage Keys** в списке параметров слева.
5.  Щелкните **Approve All Keys**.
6.  Разверните **My Application**.
7.  Скопируйте свой **Consumer Key** в текстовый файл для использования в будущем. Это будет ключ, который вы будете использовать до конца этой лабораторной работы.

>   **Примечание**: MapQuest может изменить процесс получения ключа. Если описанные выше действия больше не действительны, поищите в Интернете “steps to generate mapquest api key”.

### Часть 4. Создайте базовое приложение MapQuest Direction

В этой части вы создадите сценарий Python для отправки URL-запроса в API маршрутов MapQuest. Затем вы протестируете свой вызов API. На протяжении оставшейся части этой лабораторной работы вы будете строить сценарий по частям, каждый раз сохраняя файл под новым именем. Это поможет вам изучить части приложения, а также предоставит вам серию скриптов, к которым вы можете вернуться, если у вас возникнут какие-либо проблемы в текущей версии вашего приложения.

**Шаг 1. Создайте новый файл в VS Code.**

Вы можете использовать любые инструменты, которые хотите ввести в команды Python, и выполнить код Python. Однако в этой лабораторной работе будет продемонстрировано построение приложения в VS Code.

1.  Откройте **VS Code**. Для вашего удобства на рабочем столе есть ярлык.
2.  Выберите **File \> Open Folder...**
3.  Перейдите в каталог **\~/labs/devnet-src/mapquest** и нажмите **OK**. Этот каталог в настоящее время пуст, и в нем вы будете хранить каждую итерацию вашего приложения.
4.  Выберите **File** \> **New File**.
5.  Выберите **File** \> **Save as…**, назовите файл **mapquest_parse-json_1.py** и нажмите **Save**.

**Шаг 2. Импорт модулей для приложения.**

Чтобы начать свой скрипт для анализа данных JSON, вам нужно будет импортировать два модуля из библиотеки Python: **requests** и **urllib.parse**. Модуль запросов предоставляет функции для получения данных JSON из URL-адреса. Модуль **urllib.parse** предоставляет множество функций, которые позволят вам анализировать и управлять данными JSON, которые вы получаете из запроса к URL-адресу.

1.  Добавьте следующие операторы импорта вверху вашего скрипта.

    ```python
    import urllib.parse
    import requests
    ```

1.  Выберите **Terminal \> New Terminal**, чтобы открыть терминал внутри VS Code.
2.  Сохраните и запустите ваш скрипт. Вы не должны получить никаких ошибок. Вам следует часто сохранять и запускать свои сценарии для проверки функциональности кода.

    ```
    devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_1.py 
    devasc@labvm:~/labs/devnet-src/mapquest$
    ```

**Шаг 3. Создайте URL-адрес для запроса к API маршрутов MapQuest.**

Первым шагом в создании вашего запроса API является создание URL-адреса, который ваше приложение будет использовать для выполнения вызова. Первоначально URL-адрес будет представлять собой комбинацию следующих переменных:

-   **main_api** - основной URL-адрес, к которому вы обращаетесь
-   **orig** - параметр, указывающий вашу точку отправления
-   **dest**- параметр для указания пункта назначения
-   **key** - ключ API MapQuest, полученный с веб-сайта разработчика.

1.  Создайте переменные для создания URL-адреса, который будет отправлен в запросе. В следующем коде замените **your_api_key** на Consumer Key, который вы скопировали из своей учетной записи разработчика MapQuest.

    ```python
    main_api = "https://www.mapquestapi.com/directions/v2/route?"
    orig = "Washington, D.C."
    dest = "Baltimore, Md"
    key = "your_api_key"
    ```

1.  Объедините четыре переменные **main_api**, **orig**, **dest** и **key**, чтобы отформатировать запрашиваемый URL. Используйте метод **urlencode**, чтобы правильно отформатировать значение адреса. Эта функция формирует часть параметров URL и преобразует возможные специальные символы в значении адреса в допустимые символы (например, пробел в «+» и запятую в «%2C»).

    ```python
    url = main_api + urllib.parse.urlencode({"key":key, "from":orig, "to":dest})
    ```

1.  Создайте переменную для хранения ответа запрошенного URL и распечатайте возвращенные данные JSON. Переменная **json_data** содержит представление словаря Python **json**-ответа методом **get** модуля **requests**. **Request.get** вызовет API-интерфейс MapQuest API. Оператор **print** будет временно использоваться для проверки возвращаемых данных. Позже в лабораторной работе вы замените этот оператор печати более сложными параметрами отображения.

    ```python
    json_data = requests.get(url).json()
    print(json_data)
    ```

1.  Ваш окончательный код должен выглядеть так, но с другим значением ключа.

    ```python
    import urllib.parse
    import requests

    main_api = "https://www.mapquestapi.com/directions/v2/route?"
    orig = "Washington, D.C."
    dest = "Baltimore, Md"
    key = "fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ" #Replace with your MapQuest key

    url = main_api + urllib.parse.urlencode({"key":key, "from":orig, "to":dest})

    json_data = requests.get(url).json()
    print(json_data)
    ```

**Шаг 4. Протестируйте URL-запрос.**

1.  Сохраните и запустите ваш скрипт **mapquest_parse-json_1.py** и убедитесь, что он работает.
2.  При необходимости устраните неполадки в коде. Хотя ваш вывод может немного отличаться, вы должны получить ответ JSON, подобный приведенному ниже. Обратите внимание, что на выходе получается словарь с двумя парами ключ/значение. Значение ключа **route** - это еще один словарь, который включает дополнительные словари и списки. Ключ **info** включает пару ключ/значение кода состояния **statuscode**, которую вы будете использовать позже в лабораторной работе.

    ```
    devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_1.py
    {'route': {'hasTollRoad': False, 'hasBridge': True, 'boundingBox': {'lr': {'lng': -76.612137, 'lat': 38.892063}, 'ul': {'lng': -77.019913, 'lat': 39.290443}}, 'distance': 38.089, 'hasTimedRestriction': False, 'hasTunnel': False, 'hasHighway': True, 'computedWaypoints': [], 'routeError': {'errorCode': -400, 'message': ''}, 'formattedTime': '00:49:29', 'sessionId': '5eadfc17-00ee-5f21-02b4-1a24-0647e6e69816', 'hasAccessRestriction': False, 'realTime': 2915, 'hasSeasonalClosure': False, 'hasCountryCross': False, 'fuelUsed': 1.65, 'legs': [{'hasTollRoad': False, 'hasBridge': True, 'destNarrative': 'Proceed to BALTIMORE, MD.', 'distance': 38.089, 'hasTimedRestriction': False, 'hasTunnel': False, 'hasHighway': True, 'index': 0, 'formattedTime': '00:49:29', 'origIndex': -1, 'hasAccessRestriction': False, 'hasSeasonalClosure': False, 'hasCountryCross': False, 'roadGradeStrategy': [[]], 'destIndex': 3, 'time': 2969, 'hasUnpaved': False, 'origNarrative': '', 'maneuvers': [{'distance': 0.792, 'streets': ['6th St', 'US-50 E', 'US-1 N'], 'narrative': 'Start out going north on 6th St/US-50 E/US-1 N toward Pennsylvania Ave/US-1 Alt N.', 'turnType': 0, 'startPoint': {'lng': -77.019913, 'lat': 38.892063}, 'index': 0, 'formattedTime': '00:02:06', 'directionName': 'North', 'maneuverNotes': [], 'linkIds': [], 'signs': [{'extraText': '', 'text': '50', 'type': 2, 'url': 'http://icons.mqcdn.com/icons/rs2.png?n=50&d=EAST', 'direction': 8}, {'extraText': '', 'text': '1', 'type': 2, 'url': 'http://icons.mqcdn.com/icons/rs2.png?n=1&d=NORTH', 
    <<<<<                  >>>>>
        output omitted
    <<<<<                  >>>>>
    'geocodeQuality': 'CITY', 'adminArea1Type': 'Country', 'adminArea3Type': 'State', 'latLng': {'lng': -76.61233, 'lat': 39.29044}}], 'time': 2969, 'hasUnpaved': False, 'locationSequence': [0, 1], 'hasFerry': False}, 'info': {'statuscode': 0, 'copyright': {'imageAltText': '© 2019 MapQuest, Inc.', 'imageUrl': 'http://api.mqcdn.com/res/mqlogo.gif', 'text': '© 2019 MapQuest, Inc.'}, 'messages': []}}
    devasc@labvm:~/labs/devnet-src/mapquest$
    ```

1.  Измените переменные **orig** и **dest**. Перезапустите скрипт, чтобы получить другие результаты. Для получения желаемых результатов лучше всего включать город и штат для городов США. При упоминании городов в других странах обычно можно использовать либо английское название города и страны, либо местное название. Например:

    ```python
    orig = "Rome, Italy"
    dest = "Frascati, Italy"
    ```

    или же

    ```python
    orig = "Roma, Italia"
    dest = "Frascati, Italia"
    ```

**Шаг 5. Распечатайте URL-адрес и проверьте статус запроса JSON.**

Теперь, когда вы знаете, что запрос JSON работает, вы можете добавить в приложение дополнительные функции.

1.  Сохраните ваш скрипт как **mapquest_parse-json_2.py**.
2.  Удалите инструкцию **print(json_data)**, поскольку вам больше не нужно проверять правильность форматирования запроса.
3.  Добавьте приведенные ниже инструкции, которые будут делать следующее:
    1.  Распечатывать построенный URL-адрес, чтобы пользователь мог видеть точный запрос, сделанный приложением.
    2.  Анализировать данные JSON, чтобы получить значение **statuscode**.
    3.  Запускать цикл **if**, который проверяет успешный вызов, на что указывает возвращаемое значение 0. Добавьте оператор print для отображения значения **statuscode** и его значения. Спецсимвол **\\n** добавляет пустую строку под выводом.

        Позже в этой лабораторной работе вы добавите операторы **elif** и **else** для различных значений кода состояния.

    ```python
    print("URL: " + (url))

    json_data = requests.get(url).json()
    json_status = json_data["info"]["statuscode"]

    if json_status == 0:
        print("API Status: " + str(json_status) + " = A successful route call.\n")
    ```

**Шаг 6. Состояние проверки и команды печати URL.**

1.  В данном примере используются следующие параметры.

    ```python
    orig = "Washington, D.C."
    dest = "Baltimore, Md"
    ```

1.  Сохраните и запустите ваш скрипт **mapquest_parse-json_2.py** и убедитесь, что он работает. При необходимости устраните неполадки в коде. Вы должны получить результат, подобный следующему. Обратите внимание, что ваш ключ встроен в URL-запрос.

    ```
    devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_2.py
    URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Washington%2C+D.C.&to=Baltimore%2C+Md
    API Status: 0 = A successful route call.

    devasc@labvm:~/labs/devnet-src/mapquest$
    ```

**Шаг 7. Добавьте пользовательский ввод для начального местоположения и пункта назначения.**

Вы использовали статические значения для переменных местоположения. Однако приложение требует, чтобы пользователь ввел их. Чтобы обновить приложение, выполните следующие действия:

1.  Сохраните ваш скрипт как **mapquest_parse-json_3.py**.
2.  Удалите текущие переменные **orig** и **dest**.
3.  Перепишите **orig** и **dest** так, чтобы они находились внутри цикла while, в котором он запрашивает ввод данных пользователем для начального местоположения и назначения. Цикл while позволяет пользователю продолжать делать запросы для разных направлений. Добавьте следующий код, выделенный ниже, после ключевого параметра. Убедитесь, что весь оставшийся код имеет правильный отступ внутри цикла while.

    ```python
    import urllib.parse
    import requests

    main_api = "https://www.mapquestapi.com/directions/v2/route?" 
    key = "fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ"

    while True:
    orig = input("Starting Location: ")
    dest = input("Destination: ")
    url = main_api + urllib.parse.urlencode({"key": key, "from":orig, "to":dest})
    print("URL: " + (url))
    json_data = requests.get(url).json()
    json_status = json_data["info"]["statuscode"]
    if json_status == 0:
        print("API Status: " + str(json_status) + " = A successful route call.\n")
    ```

**Шаг 8. Проверьте функциональность пользовательского ввода.**

Запустите свой скрипт **mapquest_parse-json_3.py** и убедитесь, что он работает. При необходимости устраните неполадки в коде. Вы должны получить результат, аналогичный показанному ниже. Чтобы завершить программу, нажмите **Ctrl+C**. Вы получите ошибку **KeyboardInterrupt**, как показано в выводе ниже. Чтобы остановить приложение более изящно, на следующем шаге вы добавите функцию выхода.

```
devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_3.py
Starting Location: Washington, D.C.
Destination: Baltimore, Md
URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Washington%2C+D.C.&to=Baltimore%2C+Md
API Status: 0 = A successful route call.

Starting Location: ^CTraceback (most recent call last):
File "mapquest_parse-json_3.py", line 9, in <module>
    orig = input("Starting Location: ")
KeyboardInterrupt

devasc@labvm:~/labs/devnet-src/mapquest$
```

**Шаг 9. Добавьте в приложение функцию выхода.**

Вместо того, чтобы заставлять приложение завершаться с помощью прерывания клавиатуры, вы добавите возможность для пользователя вводить **q** или **quit** в качестве ключевых слов для выхода из приложения. Чтобы обновить приложение, выполните следующие действия:

1.  Сохраните ваш скрипт как **mapquest_parse-json_4.py**.
2.  Добавьте оператор **if** после каждой переменной местоположения, чтобы проверить, вводит ли пользователь **q** или **quit**, как показано ниже.

    ```python
    while True:
        orig = input("Starting Location: ")
        if orig == "quit" or orig == "q":
            break
        dest = input("Destination: ")
        if dest == "quit" or dest == "q":
            break
    ```

**Шаг 10. Протестируйте функцию выхода.**

Выполните сценарий **mapquest_parse-json_4.py** четыре раза, чтобы проверить каждую переменную местоположения. Убедитесь, что и **quit**, и **q** завершат приложение. При необходимости устраните неполадки в коде. Вы должны получить результат, подобный следующему.

```
devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_4.py
Starting Location: q
devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_4.py
Starting Location: quit
devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_4.py
Starting Location: Washington, D.C
Destination: q
devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_4.py
Starting Location: Washington, D.C.
Destination: quit
devasc@labvm:~/labs/devnet-src/mapquest$
```

**Шаг 11. Отобразите данные JSON в JSONView.**

Браузер Chromium в виртуальной машине DEVASC включает расширение JSONView. Вы можете использовать его для просмотра объекта JSON в читаемом, цветном и сворачиваемом формате.

1.  Снова запустите свой **mapquest_parse-json_4.py** и скопируйте код, возвращенный для URL. Не используйте приведенный ниже код. Ваш результат будет включать ваш ключ API.

    ```
    devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_4.py
    Starting Location: Washington, D.C.
    Destination: Baltimore, Md
    URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Washington%2C+D.C.&to=Baltimore%2C+Md
    API Status: 0 = A successful route call.
    
    Starting Location: quit
    devasc@labvm:~/labs/devnet-src/mapquest$
    ```

1.  Вставьте URL-адрес в адресное поле браузера Chromium.
2.  Сверните данные JSONView, выбрав прочерк "-" перед **route**, вы увидите, что есть два корневых словаря: **route** и **info**.

    ```
    {
    - route:{
        hasTollRoad: false,
        hasBridge: true,
    <output omitted>
    ```

    Вы увидите, что есть два корневых словаря: **route** и **info**. Обратите внимание, что **info** содержит пару ключ/значение **statuscode**, используемую в вашем коде.

    ```
    {
    + route: {},
    - info: {
        statuscode: 0,
        - copyright: {
            imageAltText: "© 2019 MapQuest, Inc.",
            imageUrl: "http://api.mqcdn.com/res/mqlogo.gif",
            text: "© 2019 MapQuest, Inc."
        },
        messages: [ ]
    }
    }
    ```

1.  Разверните словарь **route** (щелкните знак плюса «**+**» перед **route**) и исследуйте обширные данные. Существуют значения, указывающие, есть ли на маршруте платные дороги, мосты, туннели, автомагистрали, перекрытия или переходы в другие страны. Вы также должны увидеть значения расстояния, общего времени поездки и расхода топлива. Чтобы проанализировать и отобразить эти данные в вашем приложении, вы должны указать словарь **route**, а затем выбрать пару ключ/значение, которую вы хотите распечатать. В следующей части лабораторной работы вы выполните некоторый анализ словаря маршрутов.

### Часть 5. Обновите приложение MapQuest Directions дополнительными функциями

В этой части вы добавите дополнительные функции в ваше приложение MapQuest Directions, чтобы предоставить пользователю дополнительную информацию. Вы включите краткую информацию о поездке, а затем список направлений, взятых из словаря маршрутов. В качестве последнего шага вы добавите базовую проверку ошибок для проверки ввода данных пользователем.

**Шаг 1. Отображение сводной информации о поездке с указанием продолжительности, расстояния и использованного топлива.**

1.  Сохраните ваш скрипт как **mapquest_parse-json_5.py**.
2.  Под командой печати статуса API добавьте несколько операторов печати, которые отображают исходные и исходящие местоположения, а также ключи **formattedTime**, **distance** и **fuelUsed**.

    Дополнительные операторы также включают операторы печати, которые будут отображать двойную строку перед следующим запросом начального местоположения. Убедитесь, что эти операторы встроены в функцию while True.

    ```python
        if json_status == 0:
            print("API Status: " + str(json_status) + " = A successful route call.\n")
            print("=============================================")
            print("Directions from " + (orig) + " to " + (dest))
            print("Trip Duration:   " + (json_data["route"]["formattedTime"]))
            print("Miles:           " + str(json_data["route"]["distance"]))
            print("Fuel Used (Gal): " + str(json_data["route"]["fuelUsed"]))
            print("=============================================")
    ```

1.  Сохраните и запустите **mapquest_parse-json_5.py**, чтобы увидеть следующий результат.

    ```
    devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_5.py 
    Starting Location: Washington, D.C.
    Destination: Baltimore, Md
    URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Washington%2C+D.C.&to=Baltimore%2C+Md
    API Status: 0 = A successful route call.

    =============================================
    Directions from Washington, D.C. to Baltimore, Md
    Trip Duration:   00:49:29
    Miles:           38.089
    Fuel Used (Gal): 1.65
    =============================================
    Starting Location: q
    devasc@labvm:~/labs/devnet-src/mapquest$
    ```

1.  По умолчанию MapQuest использует британскую систему мер, и нет параметра запроса для изменения данных в метрической системе. Следовательно, вам, вероятно, следует преобразовать свое приложение для отображения значений показателей, как показано ниже.

    ```python
        print("Kilometers:      " + str((json_data["route"]["distance"])*1.61))
        print("Fuel Used (Ltr): " + str((json_data["route"]["fuelUsed"])*3.78))
    ```

1.  Запустите измененный скрипт **mapquest_parse-json_5.py**, чтобы увидеть следующий результат:

    ```
    devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_5.py
    Starting Location: Washington, D.C.
    Destination: Baltimore, Md
    URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Washington%2C+D.C.&to=Baltimore%2C+Md
    API Status: 0 = A successful route call.

    =============================================
    Directions from Washington, D.C. to Baltimore, Md
    Trip Duration:   00:49:29
    Kilometers:      61.32329
    Fuel Used (Ltr): 6.236999999999999
    =============================================
    Starting Location: q
    devasc@labvm:~/labs/devnet-src/mapquest$
    ```

1.  Дополнительные десятичные разряды для Километров и Использованного топлива бесполезны. Используйте аргумент формата "{:.2f}". Для форматирования значений с плавающей запятой до двух десятичных знаков перед преобразованием их в строковые значения, как показано ниже. Каждый оператор должен быть на отдельной строке.

    ```python
        print("Kilometers:      " + str("{:.2f}".format(json_data["route"]["distance"])*1.61)))
        print("Fuel Used (Ltr): " + str("{:.2f}".format(json_data["route"]["fuelUsed"])*3.78)))
    ```

**Шаг 2. Протестируйте функциональность синтаксического анализа и форматирования.**

Сохраните и запустите сценарий **mapquest_parse-json_5.py**, чтобы убедиться, что он работает. При необходимости устраните неполадки в коде. Убедитесь, что у вас есть все открывающие и закрывающие скобки. Вы должны получить результат, подобный следующему.

```
devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_5.py
Starting Location: Washington, D.C.
Destination: Baltimore, Md
URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Washington%2C+D.C.&to=Baltimore%2C+Md
API Status: 0 = A successful route call.

=============================================
Directions from Washington, D.C. to Baltimore, Md
Trip Duration:   00:49:29
Kilometers:      61.32
Fuel Used (Ltr): 6.24
=============================================
Starting Location: q
devasc@labvm:~/labs/devnet-src/mapquest$
```

**Шаг 3. Просмотрите список маневров в данных JSON.**

1.  Теперь вы готовы отобразить пошаговые инструкции от места начала до места назначения. Вернитесь в браузер Chromium, где ранее вы просматривали вывод в JSONView. Если вы закрыли браузер, скопируйте URL-адрес из последнего запуска программы и вставьте его в адресную строку браузера.
2.  Найдите в словаре **route** список **legs**. Список **legs** включает один большой словарь с большей частью данных JSON. Найдите список **maneuvers** и сверните каждый из семи словарей внутри, как показано ниже (щелкните знак «-» **минус**, чтобы переключить его на знак «+» **плюс**). Если вы используете разные локации, у вас, вероятно, будет другое количество словарей **maneuvers**.

    ```
    - legs: [
        - {
            hasTollRoad: false,
            hasBridge: true,
            destNarrative: "Proceed to BALTIMORE, MD.",
            distance: 38.089,
            hasTimedRestriction: false,
            hasTunnel: false,
            hasHighway: true,
            index: 0,
            formattedTime: "00:49:29",
            origIndex: -1,
            hasAccessRestriction: false,
            hasSeasonalClosure: false,
            hasCountryCross: false,
        - roadGradeStrategy: [
                [ ]
            ],
            destIndex: 3,
            time: 2969,
                hasUnpaved: false,
            origNarrative: "",
        - maneuvers: [
            + {…},
            + {…},
            + {…},
            + {…},
            + {…},
            + {…},
            + {…}
            ],
            hasFerry: false
        }
            ],
        - options: {
    ```

1.  Разверните первый словарь в списке **maneuvers**. Каждый словарь содержит ключ **narrative** со значением, например “Start out going north...” (Начать с севера ...), как показано ниже. Вам необходимо проанализировать данные JSON, чтобы извлечь значение ключа **narrative** для отображения внутри вашего приложения.

    ```
    - legs: [
        - {
        hasTollRoad: false,
        hasBridge: true,
        destNarrative: "Proceed to BALTIMORE, MD.",
        distance: 38.089,
        hasTimedRestriction: false,
        hasTunnel: false,
        hasHighway: true,
        index: 0,
        formattedTime: "00:49:29",
        origIndex: -1,
        hasAccessRestriction: false,
        hasSeasonalClosure: false,
        hasCountryCross: false,
        - roadGradeStrategy: [
            [ ]
        ],
        destIndex: 3,
        time: 2969,
                hasUnpaved: false,
        origNarrative: "",
        - maneuvers: [
            - {
                distance: 0.792,
            - streets: [
                    "6th St",
                    "US-50 E",
                    "US-1 N"
                ],
                narrative: "Start out going north on 6th St/US-50E/US-1 N toward Pennsylvania Ave/US-1 Alt N.",
                turnType: 0,
            - startPoint: {
                    lng: -77.019913,
                    lat: 38.892063
                },
                index: 0,
                formattedTime: "00:02:06",
                directionName: "North",
                maneuverNotes: [ ],
                linkIds: [ ],
            - signs: [
                - {
                        extraText: "",
                        ext: "50",
                        type: 2,
    <output omitted>
    ```

**Примечание**: Перенос по словам был добавлен для значения в описательной части с целью отображения.

**Шаг 4. Добавьте цикл for для перебора данных JSON маневров.**

Выполните следующие шаги, чтобы обновить приложение и отобразить значение ключа **narrative**. Вы сделаете это, создав цикл for для итерации по списку маневров, отображая значение повествования для каждого маневра от начальной точки до конечной.

1.  Сохраните ваш скрипт как **mapquest_parse-json_6.py**.
2.  Добавьте цикл for, выделенный ниже, после второго оператора печати двойной строки. Цикл for выполняет итерацию по каждому элементу в списке **maneuvers** и выполняет следующие действия:
    1.  Печатает значение narrative.
    2.  Преобразует мили в километры с \*1.61.
    3.  Форматирует значение километра для печати только двух десятичных знаков с помощью функции "{:.2f}".format.
3.  Добавьте оператор **print**, который будет отображать двойную строку перед тем, как приложение запросит другое начальное местоположение, как показано ниже.

    >   **Примечание**: Оператор печати двойной строки не имеет отступа внутри цикла for. Следовательно, он является частью предыдущего оператора **if**, который проверяет параметр **statuscode**.

    ```python
    print("Fuel Used (Ltr): " + str("{:.2f}".format((json_data["route"]["fuelUsed"])*3.78)))
    print("=============================================")
    for each in json_data["route"]["legs"][0]["maneuvers"]:
        print((each["narrative"]) + " (" + str("{:.2f}".format((each["distance"])*1.61) + " km)"))
    print("=============================================\n")
    ```

**Шаг 5. Действие - проверьте итерацию JSON.**

Сохраните и запустите сценарий **mapquest_parse-json_6.py**, чтобы убедиться, что он работает. При необходимости устраните неполадки в коде. Вы должны получить следующий результат:

```
devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_6.py 
Starting Location: Washington, D.C.
Destination: Baltimore, Md
URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Washington%2C+D.C.&to=Baltimore%2C+Md
API Status: 0 = A successful route call.

=============================================
Directions from Washington, D.C. to Baltimore, Md
Trip Duration:   00:49:29
Kilometers:      61.32
Fuel Used (Ltr): 6.24
=============================================
Start out going north on 6th St/US-50 E/US-1 N toward Pennsylvania Ave/US-1 Alt N. (1.28 km)
Turn right onto New York Ave/US-50 E. Continue to follow US-50 E (Crossing into Maryland). (7.51 km)
Take the Balt-Wash Parkway exit on the left toward Baltimore. (0.88 km)
Merge onto MD-295 N. (50.38 km)
Turn right onto W Pratt St. (0.86 km)
Turn left onto S Calvert St/MD-2. (0.43 km)
Welcome to BALTIMORE, MD. (0.00 km)
=============================================

Starting Location: q
devasc@labvm:~/labs/devnet-src/mapquest$
```

**Шаг 6. Проверьте неверный ввод данных пользователем.**

Теперь вы готовы добавить в приложение еще одну функцию, которая будет сообщать об ошибке, когда пользователь вводит недопустимые данные. Напомним, что вы запустили цикл if, чтобы убедиться, что возвращаемый **statuscode** равен 0, перед анализом данных JSON:

```python
    json_status = json_data["info"]["statuscode"]

    if json_status == 0:
        print("API Status: " + str(json_status) + " = A successful route call.\n")
```

Но что произойдет, если **statuscode** не равен 0? Например, пользователь может ввести недопустимое местоположение или может не указать одно или несколько местоположений. Если да, то приложение отображает URL-адрес и запрашивает новое начальное местоположение. Пользователь понятия не имеет, что произошло.

1.  Чтобы ваше приложение завершилось сбоем без уведомления пользователя, попробуйте следующие значения в своем приложении. Вы должны увидеть аналогичные результаты.

    ```
    devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_6.py 
    Starting Location: Washington, D.C.
    Destination: Beijing, China
    URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Washington%2C+D.C.&to=Beijing%2C+China
    Starting Location: Washington, D.C.
    Destination: Bal
    URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Washington%2C+D.C.&to=Bal
    Starting Location: q
    devasc@labvm:~/labs/devnet-src/mapquest$
    ```

1.  Скопируйте один из URL на вкладку браузера Chromium. Обратите внимание, что единственная запись в словаре **route** - это словарь **routeError** с **errorCode** 2. В информационном словаре **statuscode** равен **402**. Таким образом, ваш цикл **if** никогда не выполняет код, если код состояния не равен **0**.
2.  Сохраните ваш скрипт как **mapquest_parse-json_7.py**.
3.  Чтобы предоставить информацию об ошибке, когда **statuscode** равен **402**, **611** или другому значению, добавьте в цикл **if** два оператора **elif** и оператор **else**. Ваши операторы **elif** и **else** должны соответствовать предыдущему оператору **if**. После последнего оператора печати с двойной строкой под условием **if json_status == 0** добавьте следующие операторы **elif** и **else**:

    ```python
        if json_status == 0:
        <statements omitted>
        for each in json_data["route"]["legs"][0]["maneuvers"]:
            print((each["narrative"]) + " (" + str("{:.2f}".format((each["distance"])*1.61) + " km)"))
            print("=============================================\n")
        elif json_status == 402:
            print("**********************************************")
            print("Status Code: " + str(json_status) + "; Invalid user inputs for one or both locations.")
            print("**********************************************\n")
        elif json_status == 611:
            print("**********************************************")
            print("Status Code: " + str(json_status) + "; Missing an entry for one or both locations.")
            print("**********************************************\n")
        else:
            print("************************************************************************")
            print("For Staus Code: " + str(json_status) + "; Refer to:")
            print("https://developer.mapquest.com/documentation/directions-api/status-codes")
            print("************************************************************************\n")
    ```

Первый оператор **elif** печатается, если значение **statuscode** **402** для недопустимого местоположения. Второй оператор **elif** печатается, если значение **statuscode** **611**, потому что пользователь не предоставил запись для одного или обоих местоположений. Оператор **else** печатается для всех других значений **statuscode**, например, когда сайт MapQuest возвращает ошибку. Оператор **else** завершает цикл **if**/**else** и возвращает приложение в цикл **while**.

**Шаг 7. Тестирование полной функциональности приложения**

Запустите свой скрипт **mapquest_parse-json_7.py** и убедитесь, что он работает. При необходимости устраните неполадки в коде. Протестируйте все возможности приложения. Вы должны получить результат, подобный следующему.

```
devasc@labvm:~/labs/devnet-src/mapquest$ python3 mapquest_parse-json_7.py 
Starting Location: Washington, D.C.
Destination: Baltimore, Md
URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Washington%2C+D.C.&to=Baltimore%2C+Md
API Status: 0 = A successful route call.

=============================================
Directions from Washington, D.C. to Baltimore, Md
Trip Duration:   00:49:29
Kilometers:      61.32
Fuel Used (Ltr): 6.24
=============================================
Start out going north on 6th St/US-50 E/US-1 N toward Pennsylvania Ave/US-1 Alt N. (1.28 km)
Turn right onto New York Ave/US-50 E. Continue to follow US-50 E (Crossing into Maryland). (7.51 km)
Take the Balt-Wash Parkway exit on the left toward Baltimore. (0.88 km)
Merge onto MD-295 N. (50.38 km)
Turn right onto W Pratt St. (0.86 km)
Turn left onto S Calvert St/MD-2. (0.43 km)
Welcome to BALTIMORE, MD. (0.00 km)
=============================================

Starting Location: Moscow, Russia
Destination: Beijing, China
URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Moscow%2C+Russia&to=Beijing%2C+China
API Status: 0 = A successful route call.

=============================================
Directions from Moscow, Russia to Beijing, China
Trip Duration:   84:31:10
Kilometers:      7826.83
Fuel Used (Ltr): 793.20
=============================================
Start out going west on Кремлёвская набережная/Kremlin Embankment. (0.37 km)
Turn slight right onto ramp. (0.15 km)
Turn slight right onto Боровицкая площадь. (0.23 km)
<output omitted>
Turn slight left onto 前门东大街/Qianmen East Street. (0.31 km)
Turn left onto 广场东侧路/E. Guangchang Rd. (0.82 km)
广场东侧路/E. Guangchang Rd becomes 东长安街/E. Chang'an Str. (0.19 km)
Welcome to BEIJING. (0.00 km)
=============================================

Starting Location: Washington, D.C.
Destination: Beijing, China
URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Washington%2C+D.C.&to=Beijing%2C+China
**********************************************
Status Code: 402; Invalid user inputs for one or both locations.
**********************************************

Starting Location: Washington, D.C.
Destination: Bal
URL: https://www.mapquestapi.com/directions/v2/route?key=fZadaFOY22VIEEemZcBFfxl5vjSXIPpZ&from=Washington%2C+D.C.&to=Bal
**********************************************
Status Code: 402; Invalid user inputs for one or both locations.
**********************************************

Starting Location: Washington, D.C.
Destination: 
URL: https://www.mapquestapi.com/directions/v2/route?key=ANUqwkHlgDv1vlsyBPtVrFeX8uu6agjA&from=Washington%2C+D.C.&to=
**********************************************
Status Code: 611; Missing an entry for one or both locations.
**********************************************

Starting Location: quit
devasc@labvm:~/labs/devnet-src/mapquest$
```