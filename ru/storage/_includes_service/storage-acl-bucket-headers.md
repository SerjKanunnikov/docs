Заголовок | Описание
--- | ---
`x-amz-acl` | Устанавливает [предопределенный ACL](../concepts/acl.md#predefined-acls) для объекта.
`x-amz-grant-read` | Устанавливает получателю доступа разрешение на просмотр содержимого бакета и разрешение на чтение объектов в бакете.
`x-amz-grant-write` | Устанавливает получателю доступа разрешение на запись объектов. Используйте обязательно вместе с `x-amz-grant-read`, иначе {{ objstorage-name }} ответит с кодом `501 Not Implemented`.
`x-amz-grant-read-acp` | Устанавливает получателю доступа разрешение на чтение ACL бакета.
`x-amz-grant-write-acp` | Устанавливает получателю доступа разрешение на запись ACL бакета.
`x-amz-grant-full-control` | Устанавливает получателю доступа разрешения: `READ`, `WRITE`, `READ_ACP`, `WRITE_ACP` на бакет.

Значение для заголовков `x-amz-grant-*` представляет собой список получателей доступа, разделенный запятыми. Каждый получатель доступа идентифицируется структурой вида `<тип получателя доступа>:<идентификатор получателя доступа>`. {{ objstorage-name }} поддерживает следующие типы получателей:
* `id` — получатель доступа — пользователь облака.
* `uri` — получатель доступа — системная группа.

Пример:

```
x-amz-grant-read: uri="http://acs.amazonaws.com/groups/s3/AuthenticatedUsers"
```