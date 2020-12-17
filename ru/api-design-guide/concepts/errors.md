{% include [errors-handling](../../_includes/errors-handling.md) %}

## Обработка ошибок в асинхронных вызовах {#async-errors}

При вызове асинхронных операций сервер возвращает объект [Operation](operation.md). В случае возникновения ошибки, сообщение `Status` будет добавлено в объект `Operation` в поле `error`.
