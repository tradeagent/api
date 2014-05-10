API TradeAgent.mobi
=========

- [Основные сведения](#main)
- [Публикация контрагентов](#customers)
    - [Публикация торговых точек](#addresses)
    - [Удаление не актуальных контрагентов](#customers-remove)
- [Публикация валют](#currencies)
    - [Удаление не актуальных валют](#currencies-remove)
- [Публикация групп товаров](#groups)
    - [Удаление не актуальных групп товаров](#groups-remove)
- [Публикация товаров](#goods)
    - [Формат цен](#prices)
    - [Удаление не актуальных товаров](#goods-remove)
- [Публикация категорий (типов) цен](#price-levels)
    - [Удаление не актуальных категорий (типов) цен](#clear-price-levels)
- [Публикация единиц измерения](#units)
    - [Удаление не актуальных единиц измерения](#clear-units)
- [Публикация пользователей учетной системы](#users)
    - [Удаление не актуальных пользователей учетной системы](#users-remove)
- [Публикация юридических лиц учетной системы](#companies)
    - [Удаление не актуальных юридических лиц учетной системы](#companies-remove)
- [Получение документов из системы](#documents)
    - [Формат полей документа типа "Заявка"](#order)
        - [Товары в документе](#document-goods)
        - [Валюты в документе](#document-currencies)
    - [Формат полей документа типа "Приходной кассовый ордер"](#cash-receipt)
    - [Формат полей документа типа "Возврат"](#return)
    - [Изменение статусов документов в системе](#statuses)
- [Получение сообщений из системы](#messages)
    - [Получение WebSocket сообщений из системы](#websocket)
    - [Получение Comet сообщений из системы](#comet)
    - [Формат сообщения](#message-format)
- [Форматы данных](#formats)
    - [Формат даты](#date-format)
    - [Формат даты и времени](#date-time-format)

# <a name="main"></a> Основные сведения

Взаимодействие между сервисом TradeAgent.mobi и учетными системами происходит по HTTP-протоколу.
Данные при запросах и ответах передаются в формате [JSON](http://json.org/json-ru.html).
Для обеспечения безопасности передачи данных все запросы и ответы шифруются с помощью SSL ([HTTPS](http://ru.wikipedia.org/wiki/HTTPS)).
Для доступа к API используется ключ доступа **YOUR_API_KEY** из админ панели на сайте [tradeagent.mobi](https://tradeagent.mobi).
Ключ доступа передается как URL-параметр **key** при каждом запросе к сервису TradeAgent.mobi.

# <a name="customers"></a> Публикация контрагентов

Для публикации контрагентов используется POST-запрос по адресу **https://tradeagent.mobi/api/v1/customers?key=YOUR_API_KEY**.

Публикация большого количества контрагентов должна происходить в несколько запросов.
Каждый запрос должен публиковать не больше 200 контрагентов.

Пример публикации двух контрагентов:


    $ curl -XPOST -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/customers?key=YOUR_API_KEY' -d '[
        {"id": "1", "name": "Иванов"},
        {"id": "2", "name": "Петров"}
    ]'

Формат данных:

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Обязательное</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>string</td>
    <td>Да</td>
    <td>Уникальный идентификатор контрагента</td>
</tr>
<tr>
    <td>name</td>
    <td>string</td>
    <td>Да</td>
    <td>Имя контрагента</td>
</tr>
<tr>
    <td>address</td>
    <td>string</td>
    <td>нет</td>
    <td>Юридический адрес</td>
</tr>
<tr>
    <td>phone</td>
    <td>string</td>
    <td>нет</td>
    <td>Телефон</td>
</tr>
<tr>
    <td>priceLevelId</td>
    <td>string</td>
    <td>нет</td>
    <td>Уровень (категория) цен</td>
</tr>
<tr>
    <td>regid</td>
    <td>string</td>
    <td>нет</td>
    <td>Код регистратора юридического лица. Для Украины ЄДРПОУ</td>
</tr>
<tr>
    <td>backlog</td>
    <td>double</td>
    <td>нет</td>
    <td>общая задолженность</td>
</tr>
<tr>
    <td>sortBy</td>
    <td>int</td>
    <td>нет</td>
    <td>Порядок сортировки</td>
</tr>
</tbody>
</table>

TODO: расписать задолженность

## <a name="addresses"></a> Публикация торговых точек

Пример публикации валют:

    $ curl -XPOST -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/customers?key=YOUR_API_KEY' -d '[
        {"id": "1", "name": "Иванов", "deliveryAddresses": [
            {"id": "1984", "name": "м.Київ,вул.Карпенка,10 А"}
        ]}
    ]'

Формат данных:

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Обязательное</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>int</td>
    <td>да</td>
    <td>Код торговой точки</td>
</tr>
<tr>
    <td>name</td>
    <td>string</td>
    <td>да</td>
    <td>Название торговой точки (адрес доставки)</td>
</tr>
</tbody>
</table>

## <a name="customers-remove"></a> Удаление не актуальных контрагентов

DELETE-запрос по адресу **https://tradeagent.mobi/api/v1/customers** удаляет всех контрагентов старше *hours* часов.

Пример удаления всех контрагентов опубликованных раньше чем 24 часа назад:

    $ curl -XDELETE -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/customers?key=YOUR_API_KEY' -d '{"hours": 24}'

# <a name="currencies"></a> Публикация валют

Пример публикации валют:

    $ curl -XPOST -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/currencies?key=YOUR_API_KEY' -d '[
        {"id": 980, "name": "Гривна", "shortName": "грн", "rate": 1.0, "factor": 1.0},
        {"id": 840, "name": "Доллар", "shortName": "дол", "rate": 8.4, "factor": 1.0},
        {"id": 643, "name": "Рубль", "shortName": "руб", "rate": 3.3, "factor": 10.0}
    ]'

Формат данных:

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Обязательное</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>int</td>
    <td>Да</td>
    <td>Уникальный идентификатор валюты</td>
</tr>
<tr>
    <td>name</td>
    <td>string</td>
    <td>Да</td>
    <td>Название валюты</td>
</tr>
<tr>
    <td>shortName</td>
    <td>string</td>
    <td>Да</td>
    <td>Короткое название валюты</td>
</tr>
<tr>
    <td>rate</td>
    <td>double</td>
    <td>Да</td>
    <td>Курс валюты</td>
</tr>
<tr>
    <td>factor</td>
    <td>double</td>
    <td>Да</td>
    <td>Кратность</td>
</tr>
</tbody>
</table>


## <a name="currencies-remove"></a> Удаление не актуальных валют

DELETE-запрос по адресу **https://tradeagent.mobi/api/v1/currencies** удаляет все валюты старше *hours* часов.

Пример удаления всех валют опубликованных раньше чем 24 часа назад:

    $ curl -XDELETE -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/currencies?key=YOUR_API_KEY' -d '{"hours": 24}'



# <a name="groups"></a> Публикация групп товаров

Для публикации групп товаров используется POST-запрос по адресу **https://tradeagent.mobi/api/v1/groups?key=YOUR_API_KEY**.
Группы товаров образуют многоуровневое дерево.
Публикация большого количества групп товаров должна происходить в несколько запросов.
Каждый запрос должен публиковать не больше 200 групп товаров.

Пример публикации групп товаров:

    $ curl -XPOST -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/groups?key=YOUR_API_KEY' -d '[
        {"id": "17142", "name": "Бижутерия", "groups": [
            {"id": "21080", "name": "Банты"},
            {"id": "20881", "name": "Резинки"}
        ]}
    ]'

Формат данных:

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Обязательное</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>string</td>
    <td>да</td>
    <td>Код группы товаров</td>
</tr>
<tr>
    <td>name</td>
    <td>string</td>
    <td>да</td>
    <td>Название группы товаров</td>
</tr>
<tr>
    <td>groups</td>
    <td>array</td>
    <td>нет</td>
    <td>Список подгрупп (такого же формата)</td>
</tr>
</tbody>
</table>


## <a name="groups-remove"></a> Удаление не актуальных групп товаров

DELETE-запрос по адресу **https://tradeagent.mobi/api/v1/groups** удаляет все группы товаров старше *hours* часов.

Пример удаления всех групп товаров опубликованных раньше чем 24 часа назад:

    $ curl -XDELETE -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/groups?key=YOUR_API_KEY' -d '{"hours": 24}'


# <a name="goods"></a> Публикация товаров

Для публикации товаров используется POST-запрос по адресу **https://tradeagent.mobi/api/v1/goods?key=YOUR_API_KEY**.

Публикация большого количества товаров должна происходить в несколько запросов.

Каждый запрос должен публиковать не больше 200 товаров.

Пример публикации товаров:

    $ curl -XPOST -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/goods?key=YOUR_API_KEY' -d '[
          {"id": "15418", "groupId": "15416", "taxRate": 1.0, "packageSize": 0.0, "name": "Помада", "basePriceTax": 0.57, "basePrice": 0.57, "packing": 1000.0, "weight": 1.0, "pieceWeight": false, "rest": 240.0, "discountGroupId": 0, "sortBy": 2116, "currencyId": "2", "prices": [
              {"priceLevelId": "1", "price": 0.45, "priceTax": 0.45},
              {"priceLevelId": "2", "price": 0.51, "priceTax": 0.51}
          ], "unit": "3", "units": [
              {"id": "1", "mult": 10.0},
              {"id": "2", "mult": 100.0}
          ]}
      ]'

Формат данных:

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Обязательное</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>string</td>
    <td>да</td>
    <td>Код товара</td>
</tr>
<tr>
    <td>name</td>
    <td>string</td>
    <td>да</td>
    <td>Название товара</td>
</tr>
<tr>
    <td>groupId</td>
    <td>int</td>
    <td>нет</td>
    <td>Код группы товаров</td>
</tr>
<tr>
    <td>basePriceTax</td>
    <td>double</td>
    <td>нет</td>
    <td>Базовая цена без НДС</td>
</tr>
<tr>
    <td>basePrice</td>
    <td>double</td>
    <td>нет</td>
    <td>Базовая цена</td>
</tr>
<tr>
    <td>weight</td>
    <td>double</td>
    <td>нет</td>
    <td>Вес места (одного ящика), килограмм. информационное поле</td>
</tr>
<tr>
    <td>packing</td>
    <td>double</td>
    <td>нет</td>
    <td>Фасовка, грамм. информационное поле</td>
</tr>
<tr>
    <td>packageSize</td>
    <td>double</td>
    <td>нет</td>
    <td>Количество в упаковке (в ящике), штук. информационное поле</td>
</tr>
<tr>
    <td>pieceWeight</td>
    <td>boolean</td>
    <td>нет</td>
    <td>Признак &quot;весовой товар&quot; (true), иначе штучный (false)</td>
</tr>
<tr>
    <td>unit</td>
    <td>string</td>
    <td>нет</td>
    <td>Код базовой единицы измерения</td>
</tr>
<tr>
    <td>units</td>
    <td>array</td>
    <td>нет</td>
    <td>Список дополнительных единиц измерения и их коэффициенты по отношению к базовой</td>
</tr>
<tr>
    <td>sortBy</td>
    <td>int</td>
    <td>нет</td>
    <td>Порядок сортировки</td>
</tr>
<tr>
    <td>rest</td>
    <td>double</td>
    <td>нет</td>
    <td>Остаток товара на складе</td>
</tr>
<tr>
    <td>taxRate</td>
    <td>double</td>
    <td>нет</td>
    <td>Коэффициент НДС (без - 1.0, 10% - 1.1, 20% - 1.2 и т.д.)</td>
</tr>
<tr>
    <td>currencyId</td>
    <td>string</td>
    <td>нет</td>
    <td>Код валюты цен товаров</td>
</tr>
</tbody>
</table>

Формат единиц измерения:

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Обязательное</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>string</td>
    <td>да</td>
    <td>Код единицы измерения</td>
</tr>
<tr>
    <td>mult</td>
    <td>double</td>
    <td>да</td>
    <td>Коэффициент относительно базовой единицы измерения</td>
</tr>
</tbody>
</table>

## <a name="prices"></a> Формат цен

Поддерживается две схемы поиска цены - по категориям цен и оптовая схема.

Формат данных:

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Обязательное</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>priceLevelId</td>
    <td>string</td>
    <td>нет</td>
    <td>Код уровня (категории) цены. Если не используется - пропустить поле</td>
</tr>
<tr>
    <td>price</td>
    <td>double</td>
    <td>да</td>
    <td>Цена без НДС</td>
</tr>
<tr>
    <td>priceTax</td>
    <td>double</td>
    <td>да</td>
    <td>Цена с НДС</td>
</tr>
<tr>
    <td>amount</td>
    <td>double</td>
    <td>нет</td>
    <td>Минимальное количество для использования цены.
    Используется в потовой схеме. Если не используется - пропустить поле или оставить 0</td>
</tr>
<tr>
    <td>id</td>
    <td>int</td>
    <td>нет</td>
    <td>Приоритет формирования цены.
    Если ноль, то приоритет следующий: категория цены, минимальное количество товара, максимальна цена</td>
</tr>
</tbody>
</table>

## <a name="goods-remove"></a> Удаление не актуальных товаров

DELETE-запрос по адресу *https://tradeagent.mobi/api/v1/goods* удаляет все группы товаров старше *hours* часов.

Пример удаления всех товаров опубликованных раньше чем 24 часа назад:

    $ curl -XDELETE -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/goods?key=YOUR_API_KEY' -d '{"hours": 24}'


# <a name="price-levels"></a> Публикация категорий (типов) цен

Для публикации категорий (типов) цен используется POST-запрос по адресу **https://tradeagent.mobi/api/v1/price_levels?key=YOUR_API_KEY**.

Эта команда публикует наименования категорий (типов) цен.

Пример публикации категорий цен:

    $ curl -XPOST -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/price_levels?key=YOUR_API_KEY' -d '[
        {"id": 1, "name": "Категория 1"},
        {"id": 2, "name": "Закупочные"},
        {"id": 3, "name": "Оптовые"},
        {"id": 4, "name": "Розничные"}
    ]'

Формат данных:

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Обязательное</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>string</td>
    <td>да</td>
    <td>Код категории цены</td>
</tr>
<tr>
    <td>name</td>
    <td>string</td>
    <td>да</td>
    <td>Имя категории (типа) цены</td>
</tr>
</tbody>
</table>

## <a name="clear-price-levels"></a> Удаление не актуальных категорий (типов) цен

DELETE-запрос по адресу *https://tradeagent.mobi/api/v1/price_levels* удаляет все категории (типы) цен старше *hours* часов.

Пример удаления всех категорий цен опубликованных раньше чем 24 часа назад:

    $ curl -XDELETE -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/price_levels?key=YOUR_API_KEY' -d '{"hours": 24}'




# <a name="units"></a> Публикация единиц измерения

Для публикации единиц измерения используется POST-запрос по адресу **https://tradeagent.mobi/api/v1/units?key=YOUR_API_KEY**.

Пример публикации единиц измерения:

    $ curl -XPOST -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/units?key=YOUR_API_KEY' -d '[
        {"id": "1", "shortName": "шт.", "name": "Штука"},
        {"id": "2", "shortName": "ящ.", "name": "Ящик"}
    ]'

Формат данных:

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Обязательное</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>string</td>
    <td>да</td>
    <td>Код единицы измерения</td>
</tr>
<tr>
    <td>name</td>
    <td>string</td>
    <td>да</td>
    <td>Имя единицы измерения</td>
</tr>
<tr>
    <td>shortName</td>
    <td>string</td>
    <td>да</td>
    <td>Короткое имя единицы измерения</td>
</tr>
</tbody>
</table>

## <a name="clear-units"></a> Удаление не актуальных единиц измерения

DELETE-запрос по адресу *https://tradeagent.mobi/api/v1/units* удаляет все единицы измерения старше *hours* часов.

Пример удаления всех единиц измерения опубликованных раньше чем 24 часа назад:

    $ curl -XDELETE -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/units?key=YOUR_API_KEY' -d '{"hours": 24}'


# <a name="users"></a> Публикация пользователей учетной системы

Для публикации пользователей учетной системы используется POST-запрос по адресу **https://tradeagent.mobi/api/v1/users?key=YOUR_API_KEY**.

Торговый агент привязан к одному пользователю учетной системы. Каждый созданный торговым агентом документ имеет поле - код пользователя учетной системы.

Пример публикации пользователей:

    $ curl -XPOST -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/users?key=YOUR_API_KEY' -d '[
        {"id": "1", "name": "Иванова"},
        {"id": "2", "name": "Петрова"}
    ]'

Формат данных:

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Обязательное</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>string</td>
    <td>да</td>
    <td>Код пользователя</td>
</tr>
<tr>
    <td>name</td>
    <td>string</td>
    <td>да</td>
    <td>Имя пользователя</td>
</tr>
</tbody>
</table>


## <a name="users-remove"></a> Удаление не актуальных пользователей учетной системы

DELETE-запрос по адресу *https://tradeagent.mobi/api/v1/users* удаляет всех пользователей учетной системы старше **hours** часов.

Пример удаления всех пользователей учетной системы опубликованных раньше чем 24 часа назад:

    $ curl -XDELETE -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/users?key=YOUR_API_KEY' -d '{"hours": 24}'


# <a name="companies"></a> Публикация юридических лиц учетной системы

Для публикации юридических лиц учетной системы используется POST-запрос по адресу **https://tradeagent.mobi/api/v1/companies?key=YOUR_API_KEY**.

Созданный торговый агентом документ привязан к одному юридическому лицу учетной системы.

Пример публикации пользователей:

    $ curl -XPOST -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/companies?key=YOUR_API_KEY' -d '[
        {"id": "1", "name": "ТОВ Продукты"},
        {"id": "2", "name": "ООО Бакалея"}
    ]'

Формат данных:

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Обязательное</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>string</td>
    <td>да</td>
    <td>Код юридического лица</td>
</tr>
<tr>
    <td>name</td>
    <td>string</td>
    <td>да</td>
    <td>Имя юридического лица</td>
</tr>
</tbody>
</table>


## <a name="companies-remove"></a> Удаление не актуальных пользователей учетной системы

DELETE-запрос по адресу *https://tradeagent.mobi/api/v1/companies* удаляет всех юридических лиц системы старше **hours** часов.

Пример удаления всех юридических лиц учетной системы опубликованных раньше чем 24 часа назад:

    $ curl -XDELETE -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/companies?key=YOUR_API_KEY' -d '{"hours": 24}'



# <a name="documents"></a> Получение документов из системы [TradeAgent.mobi](https://tradeagent.mobi)

Для получения из системы документов используется GET-запрос по адресу **https://tradeagent.mobi/api/v1/docs?key=YOUR_API_KEY**.

Команда возвращает не обработанные или обработанные с признаком ошибки документы. Возвращается не более 200 документов за один запрос.

Пример получения документов:

    $ curl -XGET 'https://tradeagent.mobi/api/v1/docs?key=YOUR_API_KEY'

Пример ответа:

    {"documents": [
        {"docType": 0, "id": "534efe81-4a33-45e2-a7a6-1bae58040624", "stats": {"accessCounts": 0, "creatingTime": "24.03.2014 12:35", "accessTime": "24.03.2014 12:35"}, "customerId": "150", "deliveryAddressId": "728", "goods": [
            {"id": "27522", "amount": 1.0, "price": 38.0, "sum": 38.0, "sumTax": 38.0}
        ], "companyId": "1", "priceLevelId": "1", "sum": 38.0, "sumTax": 38.0, "discountSum": 0.0, "shipDate": "", "currencyId": "980", "currencyRate": 1.0, "note": "", "accUserId": "u1"}
    ], "message": {"info": "Успешно", "isError": false}}



### <a name="order"></a> Формат полей документа типа "Заявка"

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>string</td>
    <td>Уникальный идентификатор документа</td>
</tr>
<tr>
    <td>accUserId</td>
    <td>string</td>
    <td>Код пользователя учетной системы</td>
</tr>
<tr>
    <td>docType</td>
    <td>int</td>
    <td>Тип документа. 0 - заявка, 1 - кассовый ордер, 3 - возврат</td>
</tr>
<tr>
    <td>customerId</td>
    <td>string</td>
    <td>Код контрагента</td>
</tr>
<tr>
    <td>deliveryAddressId</td>
    <td>string</td>
    <td>Код торговой точки</td>
</tr>
<tr>
    <td>companyId</td>
    <td>String</td>
    <td>Код юридического лица</td>
</tr>
<tr>
    <td>sum</td>
    <td>double</td>
    <td>Общая сумма документа без НДС</td>
</tr>
<tr>
    <td>sumTax</td>
    <td>double</td>
    <td>Общая сумма документа с НДС</td>
</tr>
<tr>
    <td>discountSum</td>
    <td>double</td>
    <td>Сумма скидки</td>
</tr>
<tr>
    <td>cashless</td>
    <td>boolean</td>
    <td>Безналичный расчет</td>
</tr>
<tr>
    <td>priceLevelId</td>
    <td>string</td>
    <td>Код категории цен</td>
</tr>
<tr>
    <td>currencyId</td>
    <td>string</td>
    <td>Код валюты цен в документе</td>
</tr>
<tr>
    <td>currencyRate</td>
    <td>double</td>
    <td>Курс валюты документа</td>
</tr>
<tr>
    <td>note</td>
    <td>string</td>
    <td>Примечание к документу</td>
</tr>
<tr>
    <td>shipdate</td>
    <td>date</td>
    <td>Дата доставки</td>
</tr>
<tr>
    <td>stats.creatingTime</td>
    <td>timestamp</td>
    <td>Дата-время создания документа</td>
</tr>
<tr>
    <td>stats.accessTime</td>
    <td>timestamp</td>
    <td>Дата-время последнего доступа к документу</td>
</tr>
<tr>
    <td>stats.accessCounts</td>
    <td>int</td>
    <td>Количество открытий документа</td>
</tr>
</tbody>
</table>

### <a name="document-goods"></a> Формат товаров в документе

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>string</td>
    <td>Код товара</td>
</tr>
<tr>
    <td>amount</td>
    <td>double</td>
    <td>Количество товара</td>
</tr>
<tr>
    <td>price</td>
    <td>double</td>
    <td>Цена</td>
</tr>
<tr>
    <td>priceLevelId</td>
    <td>string</td>
    <td>Код категории цен</td>
</tr>
<tr>
    <td>sum</td>
    <td>double</td>
    <td>Сумма</td>
</tr>
<tr>
    <td>sumTax</td>
    <td>double</td>
    <td>Сумма с НДС</td>
</tr>
</tbody>
</table>



### <a name="document-currencies"></a> Валюты в документе

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>int</td>
    <td>Код валюты</td>
</tr>
<tr>
    <td>factor</td>
    <td>double</td>
    <td>Курс валюты</td>
</tr>
<tr>
    <td>rate</td>
    <td>double</td>
    <td>Кратность</td>
</tr>
</tbody>
</table>



### <a name="cash-receipt"></a>  Формат полей документа типа "Приходной кассовый ордер"

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>string</td>
    <td>Уникальный идентификатор документа</td>
</tr>
<tr>
    <td>accUserId</td>
    <td>string</td>
    <td>Код пользователя учетной системы</td>
</tr>
<tr>
    <td>docType</td>
    <td>int</td>
    <td>Тип документа. 0 - заявка, 1 - кассовый ордер, 3 - возврат</td>
</tr>
<tr>
    <td>customerId</td>
    <td>string</td>
    <td>Код контрагента</td>
</tr>
<tr>
    <td>deliveryAddressId</td>
    <td>string</td>
    <td>Код торговой точки</td>
</tr>
<tr>
    <td>companyId</td>
    <td>String</td>
    <td>Код юридического лица</td>
</tr>
<tr>
    <td>sum</td>
    <td>double</td>
    <td>Оплаченная сумма</td>
</tr>
<tr>
    <td>date</td>
    <td>timestamp</td>
    <td>Дата-время получения денег</td>
</tr>
<tr>
    <td>billId</td>
    <td>string</td>
    <td>Номер накладной</td>
</tr>
<tr>
    <td>typeBill</td>
    <td>string</td>
    <td>Тип накладной (из поля задолженности backlog)</td>
</tr>
<tr>
    <td>currencyId</td>
    <td>string</td>
    <td>Код валюты цен в документе</td>
</tr>
<tr>
    <td>currencyRate</td>
    <td>double</td>
    <td>Курс валюты документа</td>
</tr>
<tr>
    <td>note</td>
    <td>string</td>
    <td>Примечание к документу</td>
</tr>
<tr>
    <td>stats.creatingTime</td>
    <td>timestamp</td>
    <td>Дата-время создания документа</td>
</tr>
<tr>
    <td>stats.accessTime</td>
    <td>timestamp</td>
    <td>Дата-время последнего доступа к документу</td>
</tr>
<tr>
    <td>stats.accessCounts</td>
    <td>int</td>
    <td>Количество открытий документа</td>
</tr>
</tbody>
</table>


### <a name="return"></a>  Формат полей документа типа "Возврат"

Формат полей документа возврат аналогичен формату полей [Заявка](#order).

### <a name="statuses"></a> Изменение статусов документов в системе [TradeAgent.mobi](https://tradeagent.mobi)


Для изменения статусов документов в системе используется POST-запрос по адресу **https://tradeagent.mobi/api/v1/docs/statuses?key=YOUR_API_KEY**.

Пример обновления статусов:

    $ curl -XPOST -H 'Content-Type: application/json' 'https://tradeagent.mobi/api/v1/docs/statuses?key=YOUR_API_KEY' -d '[
        {"id": "055bf12b-39c4-4a66-9e24-78c93280c228", "isError": true, "info": "Ошибка создания документа в учетной системе"},
        {"id": "05e74b95-104c-469d-b87f-fbd7159cb9c7", "isError": false, "info": "Документ обработан успешно"},
        {"id": "0756d1cd-a8ba-4555-a15b-c57a9aa89560", "isError": false}
    ]'

Формат данных:

<table>
<thead>
<tr>
    <td>Поле</td>
    <td>Тип</td>
    <td>Обязательное</td>
    <td>Описание</td>
</tr>
</thead>
<tbody>
<tr>
    <td>id</td>
    <td>string</td>
    <td>Да</td>
    <td>Уникальный идентификатор документа</td>
</tr>
<tr>
    <td>isError</td>
    <td>boolean</td>
    <td>Да</td>
    <td>Признак ошибки обработки документа в учетной системе</td>
</tr>
<tr>
    <td>info</td>
    <td>string</td>
    <td>Нет</td>
    <td>Сообщение из учетной системы</td>
</tr>
</tbody>
</table>


# <a name="messages"></a> Получение сообщений из системы [TradeAgent.mobi](https://tradeagent.mobi)

У учетной системы есть возможность подключиться к системе [TradeAgent.mobi](https://tradeagent.mobi) для получения сообщений в реальном времени.
Поддерживается два варианта подключений: WebSocket-подключение или Comet-подключение.


## <a name="websocket"></a> Получение [WebSocket](http://www.websocket.org/) сообщений из системы [TradeAgent.mobi](https://tradeagent.mobi)

URL для подключения:

    wss://tradeagent.mobi/api/v1/messages/ws?key=YOUR_API_KEY

Для тестирования подключения можно использовать эхо сервис [http://www.websocket.org/echo.html](http://www.websocket.org/echo.html) (отметить *Use secure WebSocket (TLS)*).

Или протестировать можно через curl:

    curl -i -N -H "Connection: Upgrade" -H "Upgrade: websocket" -H "Host: tradeagent.mobi" -H "Origin: https://tradeagent.mobi" https://tradeagent.mobi/api/v1/messages/ws?key=YOUR_API_KEY


## <a name="comet"></a> Получение [Comet](http://en.wikipedia.org/wiki/Comet_\(programming\)) сообщений из системы [TradeAgent.mobi](https://tradeagent.mobi)

Comet соединение это потоковое [Chunked-соединение](http://ru.wikipedia.org/wiki/Chunked_transfer_encoding), возвращающее сообщения от сервиса.

Пример подключения через curl:

    $ curl -N 'https://tradeagent.mobi/api/v1/messages?key=YOUR_API_KEY'


## <a name="message-format"></a> Формат сообщения

Пример сообщения:

    {"type":"docs","message":{"info":"Документы","isError":false}}

Поле *type* указывает на тип сообщения. Может быть:

* docs - сообщение про наличие новых необработанных документов
* dicts - запрос на выгрузку справочников из учетной системы


## <a name="formats"></a> Форматы данных
### <a name="date-format"></a> Формат даты:

дд.мм.гггг

* дд - день
* мм - месяц
* гггг - год

Пример: 10.08.2014

### <a name="date-time-format"></a> Формат даты и времени:

дд.мм.гггг чч:mm:сс

* дд - день
* мм - месяц
* гггг - год
* чч - час
* mm - минута
* сс - секунда

Пример: 17.09.2014 11:30:07