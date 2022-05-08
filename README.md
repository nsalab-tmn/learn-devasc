# learn-devasc
Cisco Certified Devnet Assosicate

## Scope and sequence

1.	TODO

Именование лекций (тем внутри каждго модуля) осуществляется согласно [оригинальному scope & sequence](./readme-assets/devasc-v1-scope-and-sequence.pdf)

## Инструкция по переносу материалов с иcпользовнием разметки Markdown

### Структура файлов

Каждый модуль должен лежать в отдельной папке и оформляется как отдельная лекция

Именование папок осуществляется по следующей маске:

```
xx.yy-lowercase-without-spaces
```

Структура файлов для каждой лекции\модуля:
- learn-metadata.json - файл с метаданными
- lecture.md - файл с лекциями
- assets - папка для хранения файлов, связанных с лекцией

В файле learn-metadata.json указывается название лекции (модуля) и продолжительность

```json
{
    "title": "Основные типы сетей", <-- меняется для каждой лекции\модуля
    "materialType": "lecture",  <-- не меняется
    "description": "", <-- не меняется
    "difficulty": 3, <-- не меняется
    "duration": "PT0H10M", <-- не меняется, считается автоматически
    "tags": ["Network", "Cisco"], <-- не меняется
    "skills": {}, <-- не меняется
    "testProjectPath": "./lecture.md", <-- не меняется
    "assetsPath": "assets" <-- не меняется
}
```

### Заголовки
- Заголовок модуля помечается как H1 (#) - только для заголовка лекции модуля, не указывается в тексте лекции, указывается только в learn-metadata.json
- Заголовки тем помечаются как H2 (##)
- Все остальные подзаголовки помечаются как H3 (###), если подходят по структуре

Пример: оригинал лекции из Netacad

![](./readme-assets/headers_example.png)

Файл 12.08-subnet-an-ipv6-network\lecture.md:

```
<!-- 12.8.1 -->
## Разделение на подсети с использованием идентификатора подсети

Во введении к этому модулю упоминается подсеть сети IPv6. Также сказано, что вы можете обнаружить, что это немного проще, чем подсеть сети IPv4. Вы скоро узнаете!

Напомним, что с IPv4 мы должны заимствовать биты из части хоста для создания подсетей. Это связано с тем, что подсеть была задним числом с IPv4. Однако IPv6 был разработан с учетом подсетей. Для создания подсетей используется отдельное поле ID подсети в GUA IPv6. Как показано на рисунке, поле ID подсети — это область между префиксом глобальной маршрутизации и идентификатором интерфейса.

![](./assets/12.8.1.png "GUA с 16-разрядным идентификатором подсети")
<!-- /courses/itn-dl/aeed55b2-34fa-11eb-ad9a-f74babed41a6/af2380e0-34fa-11eb-ad9a-f74babed41a6/assets/2e4e42f1-1c25-11ea-81a0-ffc2c49b96bc.svg -->

Преимущество 128-битного адреса заключается в том, что он может поддерживать более чем достаточное количество подсетей и узлов в каждой подсети для каждой сети. Решение проблемы сохранения не является проблемой. Например, если префикс глобальной маршрутизации имеет значение /48 и использует типичные 64 бита для идентификатора интерфейса, это создаст 16-битный идентификатор подсети:
...
```

Файл 12.08-subnet-an-ipv6-network\learn-metadata.json

```
{
    "title": "Разделение на подсети в IPv6",
    "materialType": "lecture",
    "description": "",
    "difficulty": 3,
    "duration": "PT0H4M",
    "tags": [
        "Network",
        "Cisco"
    ],
    "skills": {},
    "testProjectPath": "./lecture.md",
    "assetsPath": "assets"
}
```

### Нумерация тем

В файлах с лекциями нужно оставлять отбивку по номерам тем в виде комментариев, чтобы было не видно при рендеринге, но чтобы сохранять ориентировку с оригиналом

```
<!-- 1.2.5 -->
## Средства сетевого подключения
Коммуникация передается по среде передачи данных. Среда передачи данных предоставляет собой канал, по которому сообщение передается от источника к адресату.
```

### Рисунки

- По умолчанию все объекты (рисунки, видео, файлы PKA, PDF и прочее) сохраняется в папку assets по маске номера темы, например:
  - 1.4.1.png
  - 1.5.2.mp4
- Если под одним номером темы несколько объектов:
  - 1.4.1-1.png
  - 1.4.1-2.png и тд.
- Большинство схем хранятся в курсах в виде svg файлов. Для скорости нужно разворачивать эти схемы на весь экран и встроенными средствами капчи экрана сохранять в png, при этом сохраняя в комментах ссылку на оригинальный svg файл:
```
![](./assets/1.4.2-1.png)
<!-- /courses/itn-dl/aeec9260-34fa-11eb-ad9a-f74babed41a6/af1e50c0-34fa-11eb-ad9a-f74babed41a6/assets/2d8cc7b3-1c25-11ea-81a0-ffc2c49b96bc.svg -->
```
- Найти ссылку на оригинальный svg файл можно провалившись в исходный код страницы как показано ниже:

![](./readme-assets/pictures.png)

### Скрытый текст

Если при переносе текста выделять его вместе с рисунками, часто захватывается скрытый текст с описанием того, что изображено на схеме, как показано ниже:

![](./readme-assets/hidden_text.png)

- Если там написано что-нибудь внятное - такой текст лучше оставлять и помещать его под схему.
- Если там совсем очевидное описание, то такой текст лучше сразу удалять - так как подсчет времени прохождения лекции считается по количеству слов в md файале

### Видео

Нужно выкачать утилитой [ffmpeg](https://1drv.ms/u/s!At1RfXB5mNd79FouXrqLgHRRkUgd?e=W0K1lF) используя следующую команды

```
ffmpeg -i https://example.org/master.m3u8 -c copy -bsf:a aac_adtstoasc C:\downloads\output.mp4
```

Найти ссылку на m3u8 файл можно во вкладке Network в режиме разработчика как показано ниже:

![](./readme-assets/video.png)

Все видео будут загружены на YouTube, но до этого момента нужно сохранять их в виде следующего тега:

```
![youtube](https://www.youtube.com/watch?v=1.2.3.mp4)
```

Где ``1.2.3`` порядковый номер темы. Позже данный номер будет заменет на ID видео на YouTube.

### Файлы PKA и PDF

Аналогино сохраняем в папку assets, вставляем ссылки как в примере ниже

```
[Открыть описание в PDF](./assets/1.0.5-packet-tracer---logical-and-physical-mode-exploration_ru-RU.pdf)

[Скачать файл для Packet Tracer](./assets/1.0.5-packet-tracer---logical-and-physical-mode-exploration_ru-RU.pka)
```

### Анимации

Делаем из анимации gif-файл при помощи [Recordit](https://recordit.co/)
 
1. Выделяем область захвата, нажимаем Record, запускаем анимацию.
 ![](./readme-assets/gif-1.png)
 
2. Ждем окончания анимации, нажимаем Stop.
  ![](./readme-assets/gif-2.png)
  
3. Переходим на сайт.
![](./readme-assets/gif-3.png)

4. Жмем на Play и затем на кнопку GIF.
![](./readme-assets/gif-4.png)

5. Сохраняем gif файл.
![](./readme-assets/gif-5.png)

### Квизы в конце модуля

Пока игнорируем
