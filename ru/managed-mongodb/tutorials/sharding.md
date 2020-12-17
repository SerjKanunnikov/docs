# Шардирование коллекций {{ MG }}

Шардирование в {{ mmg-name }} доступно для кластеров с версией {{ MG }} не ниже 4.0. Если ваш кластер развернут с версией 3.6, вы можете [обновить его](../operations/cluster-version-update.md).

Также стоит учесть, что шардирование кластера {{ mmg-name }} происходит с автоматическим созданием служебных хостов `MONGOS` и `MONGOCFG` или `MONGOINFRA`, которые [тарифицируются](../pricing.md) отдельно от основных хостов СУБД.

{% include [irreversible-sharding-note.md](../../_includes/mdb/irreversible-sharding-note.md) %}

Шардирование коллекций имеет смысл, когда распределение по шардам поможет существенно улучшить производительность СУБД или доступность данных. Для повышения доступности каждый шард рекомендуется составлять из 3 и более хостов БД.

Удобство в работе и фактическое улучшение производительности сильно зависят от выбора ключа шардирования: данные коллекции должны быть логично распределены по шардам, а данные разных шардов не должны быть связаны между собой.

Распределять по шардам стоит:

* Данные значительного объема: задуматься стоит при объеме коллекции от 200 ГБ.
* Коллекции с неоднородным содержанием: например, данные можно четко разделить на часто и редко запрашиваемые.
* Коллекции с высокими требованиями к скорости чтения и записи данных: шардирование поможет распределить нагрузку по хостам, чтобы обойти технические ограничения.

Подробнее о концепции шардирования читайте в разделе [{#T}](../concepts/sharding.md).


## Как включить шардирование коллекций {#enable}

{% note warning %}

Все операции по настройке шардирования с помощью CLI `mongo` необходимо выполнять от имени пользователя с ролью [mdbShardingManager](../concepts/users-and-roles.md#mdbShardingManager) в базе данных [admin](https://docs.mongodb.com/manual/reference/glossary/#term-admin-database).

{% endnote %}

1. [Включите шардирование](../operations/shards.md#enable) для кластера.
1. [Подключитесь](../operations/connect.md) к хосту `MONGOS` или `MONGOINFRA` с помощью CLI `mongo` и включите шардирование:

   ```
   sh.enableSharding(<имя базы>)
   ```

   Тип хоста можно запросить со [списком хостов в кластере](../operations/hosts.md#list-hosts).

1. Задайте индекс в шардированной коллекции:

   ```
   db.<имя базы>.ensureIndex( { "<индекс>": "hashed" } )
   ```

1. Включите шардирование коллекции:

   ```
   sh.shardCollection( "<коллекция>", { "<индекс>": "hashed" } )
   ```

   Подробное описание команды `shardCollection` см. в [документации {{ MG }}](https://docs.mongodb.com/manual/reference/method/sh.shardCollection/#definition).

1. Переключите приложения, которые используют вашу базу данных, на использование **исключительно** хостов `MONGOS` или `MONGOINFRA`.


### Ссылки по теме {#links}

Документация {{ MG }} рассказывает о решениях проблем шардирования:

* О механизме шардирования в общем: [Sharding](https://docs.mongodb.com/manual/sharding/index.html).
* О выборе ключа шардирования и стратегиях шардирования: [Shard Keys](https://docs.mongodb.com/manual/core/sharding-shard-key/).


## Пример шардирования {#example}

Допустим, у вас уже есть кластер {{ mmg-name }} с шардами и базой данных `billing`. Необходимо включить шардирование для коллекций `payment` и `addresses`. Для примера в качестве ключа шардирования используется хэш индекса для `payment` и значение поля `addresses`.

Последовательность операций:

1. [Подключитесь](../operations/connect.md) к базе `billing`. У пользователя, от имени которого вы подключаетесь к базе, должна быть роль [mdbShardingManager](../concepts/users-and-roles.md#mdbShardingManager) в базе данных [admin](https://docs.mongodb.com/manual/reference/glossary/#term-admin-database).
1. Включите шардирование для базы `billing`:

   ```
   sh.enableSharding("billing")
   ```

1. Определите индекс в шардированной коллекции:

   ```
   db.payments.ensureIndex( { "_id": "hashed" } )
   ```

1. Создайте необходимое количество шардов в [консоли управления]({{ link-console-main }}).
1. Задайте шардирование для коллекции с учетом ее пространства имен:
   ```
   sh.shardCollection( "billing.payments", { "_id": "hashed" } )
   ```

После этого шардирование будет включено и настроено. Чтобы убедиться в этом, попробуйте вывести список доступных шардов с помощью команды `db.adminCommand( { listShards: 1 } )`.