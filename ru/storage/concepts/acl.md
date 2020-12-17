# Список управления доступом (ACL)

В {{ yandex-cloud }} используются два независимых друг от друга механизма управления доступами к ресурсам {{ objstorage-name }}:

- [{{ iam-name }}](../../iam/concepts/index.md).
- ACL {{ objstorage-name }} — список разрешений для каждого объекта и бакета, хранящийся непосредственно в {{ objstorage-name }}.

При получении запроса к бакету или объекту {{ objstorage-name }} проверяет доступы с помощью обоих механизмов. Если необходимый доступ открыт хотя бы одним из способов, {{ objstorage-name }} выполняет запрос. Разрешения, выданные на бакет, распространяются на все находящиеся в нем объекты. ACL позволяет расширить доступы к отдельному объекту.

{% note warning %}

Бакет [наследует](#inheritance) такие же права доступа в сервисе IAM, как у каталога и облака, в котором он находится. 

{% endnote %}

> Например, если у пользователя в сервисе {{ iam-short-name }} есть роль `viewer` на каталог с бакетом, то он может просматривать его содержимое, даже если это не отражено в ACL бакета.

По умолчанию для каждого нового объекта или бакета {{ objstorage-name }} создает пустой ACL. Пользователь, обладающий соответствующими правами, может отредактировать или загрузить ACL для бакетов и объектов {{ objstorage-name }}.

С помощью ACL можно выдать разрешения пользователю {{ yandex-cloud }}, сервисному аккаунту и системной группе, при этом необходимо знать [идентификатор получателя разрешений](#accounts-ids). При выдаче разрешений вы можете использовать [предопределенные ACL](#predefined-acls), которые содержат популярные наборы доступов.

Описание структуры ACL смотрите в разделе [XML-схема ACL](../s3/api-ref/acl/xml-config.md). В одном ACL вы можете задать не более 100 правил.

{% note info %}

ACL, загруженный для объектов, применяется мгновенно. ACL, загруженный для бакетов, и доступы, измененные в IAM, применяются с задержкой. Подробнее про задержки читайте в [документации IAM](../../iam/concepts/access-control/index.md). 

{% endnote %}

## Идентификатор получателя разрешений {#accounts-ids}

- Пользователь {{ yandex-cloud }}
    
    Идентификатор можно получить:

    - В разделе [IAM]({{ link-console-access-management }}) консоли управления.
    - С помощью [CLI или API IAM](../../iam/operations/users/get.md).
- [Сервисный аккаунт](../../iam/concepts/users/service-accounts)

    Для получения идентификатора перейдите в раздел **Сервисные аккаунты** консоли управления.

- [Системная группа](#system-groups)

    Для выдачи разрешений используйте URI системной группы.

## Операции с ACL {#acl-operations}

- В консоли управления вы можете редактировать ACL для [бакетов](../operations/buckets/edit-acl.md) и [объектов](../operations/objects/edit-acl.md).
- С помощью API, совместимого с Amazon S3, вы можете [загрузить или скачать](../s3/api-ref/acl.md) ACL для бакета или объекта.

    Удалить ACL невозможно. Чтобы удалить все разрешения, загрузите пустой ACL.

## Виды разрешений {#permissions-types}

Разрешения соответствуют ролям пользователей в {{ iam-name }}.

Название разрешения | Роль в IAM | Описание
--------------------|------------|--------
`READ` | `viewer` | Для бакета: разрешение на получение списка объектов в бакете, чтение различных настроек бакета (жизненный цикл, CORS, статический хостинг), чтение всех объектов в бакете.<br/><br/>Для объекта: разрешение на чтение.
`WRITE` | `editor` | Для бакета: запись объектов в бакет.<br/>Используется обязательно вместе с `READ`, отдельно указать разрешение `WRITE` нельзя.<br/><br/>Для объекта: разрешение не имеет смысла, при записи объекта проверяются разрешения для бакета.
`FULL_CONTROL` | `admin` | Полный доступ к объектам и бакетам.
`READ_ACP` | `viewer` | Разрешение на чтение ACL. Только для объектов.
`WRITE_ACP` | `editor` | Разрешение на запись ACL. Только для объектов.

{% note info %}

Если при оформлении ACL указать доступ `WRITE`, но при этом не указать `READ`, то {{ objstorage-name }} ответит с кодом `501 Not Implemented`.

{% endnote %}

## Предопределенные ACL {#predefined-acls}

ACL | Описание
----|---------
`private`<br/>`bucket-owner-full-control` | Пользователи {{ yandex-cloud }} получают разрешения в соответствии со своими ролями в IAM.
`public-read` | Системная группа `AllUsers` получает разрешение `READ`.
`public-read-write` | Системная группа `AllUsers` получает разрешения `READ` и `WRITE`.
`authenticated-read` | Системная группа `AuthenticatedUsers` получает разрешение `READ`.

Предопределенные ACL могут применяться как к объектам, так и к бакетам. ACL `public-read-write`, примененный к объекту, эквивалентен `public-read`.

Загрузить предопределенный ACL можно только с помощью [HTTP API, совместимого с Amazon S3](../s3/api-ref/acl.md). При загрузке ACL используйте HTTP-заголовок `x-amz-acl`.

## Системные группы {#system-groups}

### AllUsers {#all-users}

Включает в себя всех пользователей интернета.

Разрешение для `AllUsers` выглядит следующим образом:

```
<Grantee xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="Group">
    <URI>http://acs.amazonaws.com/groups/global/AllUsers</URI>
</Grantee>
```

### AuthenticatedUsers {#authenticated-users}

Включает в себя всех аутентифицированных пользователей {{ yandex-cloud }}: как из вашего облака, так и из облаков других пользователей.

Разрешение для `AuthenticatedUsers` выглядит следующим образом:

```
<Grantee xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="Group">
    <URI>http://acs.amazonaws.com/groups/global/AuthenticatedUsers</URI>
</Grantee>
```

## Наследование прав доступа на бакет системными группами {{ yandex-cloud }} {#inheritance}

Бакет наследует такие же права доступа в сервисе {{ iam-short-name }}, как у каталога и облака, в котором он находится. Если у пользователя есть права доступа на каталог или облако, которым принадлежит бакет, то у него будут права и на сам бакет.

{% note warning %}

Назначение ролей [системным группам](../../iam/concepts/access-control/system-group.md) `allUsers` и `allAuthenticatedUsers` на каталог или облако, которым принадлежит бакет, эквивалентно выдаче **публичного доступа** к бакету:
- `allAuthenticatedUsers` — доступ к бакету получат все аутентифицированные пользователи {{ yandex-cloud }}: как из вашего облака, так и из облаков других пользователей.
- `allUsers` — доступ получат все пользователи.

Аналогичный доступ к бакету можно выдать, добавив в ACL бакета права для групп `AuthenticatedUsers` и `AllUsers`.
 
{% endnote %}

### Как посмотреть права на бакет, наследуемые от каталога {#examples}

Бакет наследует права от каталога. Если вы хотите знать, какие именно права унаследовал бакет, получите список ролей на каталог. Также вы можете в любой момент отозвать эти роли.

- Чтобы получить список ролей на каталог, выполните команду:

  ```bash
  $ yc resource-manager folder list-access-bindings \
      --id b1g7gvsi89m34qmcm4ke
  ```

  Пример вывода команды:

  ```bash
  +---------+--------------+-----------------------+
  | ROLE ID | SUBJECT TYPE |      SUBJECT ID       |
  +---------+--------------+-----------------------+
  | viewer  | system       | allAuthenticatedUsers |
  | viewer  | system       | allUsers              |
  +---------+--------------+-----------------------+
  ```
 
  В выводе присутствует `allAuthenticatedUsers` и `allUsers`. Это значит, что пользователи этих групп имеют права на данный каталог и все содержащиеся в нем ресурсы, в том числе бакеты.

- Чтобы отозвать роль у системной группы `allAuthenticatedUsers` выполните команду:

  ```bash
  $ yc resource-manager folder remove-access-binding \
      --id b1g7gvsi89m34qmcm4ke \
      --role viewer \
      --allAuthenticatedUsers
  ```

- Чтобы отозвать роль у системной группы `allUsers` выполните команду:

  ```bash
  $ yc resource-manager folder remove-access-binding \
      --id b1g7gvsi89m34qmcm4ke \
      --role viewer \
      --allUsers
  ```