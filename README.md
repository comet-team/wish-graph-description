Wish Graph
===
**Рекомендуем NFT на основе графа владения**

Мобильное приложение на Android (Kotlin):
https://github.com/comet-team/wish-graph-mobile

Фронт (Node.js):
https://github.com/comet-team/wish-graph-web

ML (Python):
https://github.com/comet-team/wish-graph-model
(ветка ML_Factorization)

Сервер (Java + Spring):
https://github.com/comet-team/wish-graph-back 

Сайт крутится тут:
http://185.46.8.253:3000

Приложение можно скачать тут: 
https://disk.yandex.ru/d/3W75r0KiT3tDrg

Содержание
---
+ [**Проблема**](#проблема)  
+ [**Решение**](#решение) 
+ [**Архитектура**](#архитектура) 
  + [**API сервер**](#api-сервер)
  + [**Использование Rarible Protocol**](#использование-rarible-protocol)
  + [**Машинное обучение**](#машинное-обучение)
  + [**Сайт**](#сайт)
  + [**Мобильное приложение**](#мобильное-приложение) 
+ [**Демонстрация решения**](#демонстрация-решения)
+ [**Направления дальнейшего развития**](#направления-дальнейшего-развития)  
+ [**Road map**](#road-map)  

Проблема
---
Из-за резко возросшей популярности NFT рынок переполнен токенами самых разных направлений. Мы задумались над инструментом, который мог бы среди огромного разнообразия NFT выделить наиболее интересные для пользователя.  

Решение
---

На решение нас натолкнула [теория 6-ти рукопожатий](https://ru.wikipedia.org/wiki/Теория_шести_рукопожатий), согласно которой любые два человека на Земле разделены не более чем пятью уровнями общих знакомых. Поэтому мы решили разработать рекомендательную систему, которая будет основана на механизме NFT-знакомств, где знакомство - это факт владения NFT одного автора. С использованием Rarible Protocol мы сможем находить эти знакомства и строить граф для каждого конкретного пользователя. На основе анализа этого графа и будут составляться рекомендации.

К этой идее мы решили подойти с трёх сторон:
+ Сервер, который публикует соответствующий API => техническая реализация идея и основной продукт
+ Мобильное приложение, которое демонстрирует работу API (по адресу кошелька выводит рекомендованные NFT) => реклама API и будущий маркетплейс
+ Сайт, на котором можно посмотреть визуализированный граф, потыкать и посмотреть рекомендации => fan для пользователя

### Причины выбора решения
+ Математический бэкграунд участников команды
+ Более стандартные подходы к рекомендациям уже реализованы (например, на основе просмотров или покупок) 
+ Это может стать инструментом для улучшения социального взаимодействия между пользователями в web3.0 => основа для социальной сети по аналогии с Linkedin
+ Идея предполагает интенсивное использование Rarible Protocol
+ Перспективы интеграции с NFT-маркетплейсами (в частности, Rarible)
+ Хотелось предложить новую для сферы идею
+ Идея отвечает тренду на метавселенные, потому что благодаря нашему API можно будет реализовывать механики связи пользователей

### Рынок и целевая аудитория
Сегодня рынок NFT имеет объём $27 млрд, показывает годовой рост продаж в 220 раз и не собирается на этом останавливаться.
Проект нацелен на улучшение пользовательского опыта в NFT-маркетплейсах, что положительно скажется на их монетизации. Наш проект мы сможем предложить как крупным маркетплейсам, так и связанным с ними стартапам.

### Бизнес-модель
Мы делаем open source продукт.

Тем не менее, доступ к API будет осуществляться по подписке, чтобы оплачивать вычислительные мощности.
Кроме того, мы планируем добавить к сайту и мобильному приложению функционал маркетплейса, чтобы установить дополнительную комиссию за покупку.

### Требования к продукту
+ Отсутвие комиссии за использование на данном этапе
+ Удобное в использовании API
+ Хорошая документация 
+ Сайт и приложение
  + Простой продукт с минимумом кнопок :)
  + Отсутствие криптовалютных терминов
  + Лаконичный и гибкий дизайн

Архитектура
---
<img src="https://github.com/comet-team/wish-graph-description/blob/main/img/Architecture.png" width="500" />

Принцип построения графа знакомств

<img src="https://github.com/comet-team/wish-graph-description/blob/main/img/API%20structure.png" width="300" />

### API сервер
[Репозиторий](https://github.com/comet-team/wish-graph-model)

На данный момент сервер предоставляет только один API эндпоинт для получения списка рекомендованных NFT:

**GET http://5.63.159.42:8081/user_recommend/<wallet_token>**

Возвращает список NFT которые можно рекомендовать пользователю

**Parameters**

|          Name | Required |  Type   | Description                                                                                                                                                           |
| -------------:|:--------:|:-------:| --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|     `wallet_token` | required | string  | Токен кошелька пользователя.                                                                     |                                                            |

**Response**

```
[
    {
        "account": string,
        "nft": [
            {
                "id": string,
                "url": string
            },
            ...
        ],
        "value": double
    }
]
```

### Использование Rarible Protocol
Сервер использует следующие эндпоинты, предоставляемые API от Rarible:
+ https://api.rarible.com/protocol/v0.1/ethereum/nft/items/byCreator
+ https://api.rarible.org/v0.1/items/

### Машинное обучение

В данном случае мы имеем проблемы с быстрым получением графа по пользователю, а значит входная информация для рекомендаций крайне небольшая. Авторов что мы находим необходимо отсортировать по релевантности и для этого существует несколько часто используемых способов. Например, можно рассмотреть нормализованную матрицу сопряжения графа отношений владения пользователей и создателей с весами ребер вычисленными на основе количества, вида и других параметров nft. Для нахождения близости взять вектор пользователя, умножить его на транспонированную матрицу таким образом получив вектор ‘близости’ для других пользователей по отношению к данному, а затем умножить его на матрицу получив таким образом близость уже авторов. 

В рекомендательных системах часто используют метод Collaborative Filtering. Достаточно факторизовать матрицу смежности таким образом чтобы извлечь фичи и получить векторы-эмбеддинги для пользователей и авторов. Учитывая, что у нас крайне небольшая матрица возникает непростая задача оценить эффективность работы данного алгоритма для чего необходимо создать достаточный объем тестовой выборки. Как правило для этого используют разделение транзакций по времени таким образом учитывая порядок истории что логично. 

Помимо collaborative filtering – самой часто используемой моделью в области рекомендательных систем нам могут подойти более примитивные варианты – например модель предполагающая, что стоимость вычисляется как сумма параметров лишь пользователя и автора. Пока что основным ограничением является скорость построения графа, проблема того как изначально фильтровать данные и оставлять достаточно информации для предсказания.

### Сайт
[Репозиторий](https://github.com/comet-team/web)

### Мобильное приложение
[Репозиторий](https://github.com/comet-team/wish-graph-mobile)

Приложение под Android на Kotlin для просмотра рекомендованных NFT по адресу кошелька.

Демонстрация решения
---
[Сайт](https://disk.yandex.ru/i/UZQz4nIAIxrddg)

[Приложение](https://disk.yandex.ru/i/iVbhsky4qRDbBg)

<img src="https://github.com/comet-team/wish-graph-description/blob/main/img/Wish%20Graph%20Mobile%20Screens.png" width="700" />

Направления дальнейшего развития
---
Идеи развития проекта:
+ Оптимизировать алгоритмы поиска наиболее релевантных знакомых, чтобы API работала быстрее
+ Расширить варианты рекомендаций. Например, рекомендовать не только конкретные NFT, а ещё и художников
+ Развить созданную API в полноценный аналитический инструмент
+ Подключить кастодиальный кошелёк вместо ручного ввода адреса кошелька
+ Добавить в сайт и приложение возможность покупать nft (доделать базовый функционал маркетплейса)
+ Провести тестирование на разработчиках и пользователях 

Road Map
---
Как для нас прошёл этот кейс-чемпионат:
- [x] Воскресенье - анализ кейса
- [x] Понедельник - проработка нескольких идей
- [x] Вторник - выбор лучшей идеи и обсуждение с ментором
- [x] Среда, четверг - разработка mvp
- [x] Пятница - создание презентации и описания проекта
- [x] Суббота, воскресенье - ожидание результата отборочного этапа и закрытие дедлайнов в вузе :)
- [x] Понедельник - созвон с ментором и проработка продукта
- [x] Вторник, среда - доработка реализации
- [ ] Четверг - защита проекта  
- [ ] Пятница - ожидание результатов финала
- [ ] Суббота - очная часть чемпионата

Наши планы после чемпионата:
- [ ] Получение и обсуждение фидбека по проекту
- [ ] Техническая доработка проекта
- [ ] Проведение тестирования на первых пользователях
- [ ] Популяризация проекта в тематических сообществах
 
И многое другое!
