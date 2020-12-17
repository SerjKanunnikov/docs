# Создание подключения к ClickHouse

{% note info %}

Особенности работы {{ datalens-short-name }} с подключением к {{ CH }}:
  - Подключение к {{ CH }} выполняется только по HTTP-интерфейсу.
  - Все запросы к данным выполняются с включенным флагом [join_use_nulls](https://clickhouse.tech/docs/ru/operations/settings/settings/#join_use_nulls).

{% endnote %}

{% include [connection-note](../../../_includes/datalens/datalens-connection-note.md) %}

## Подключение к ClickHouse {#clickhouse-connection}

Чтобы создать подключение к {{ CH }}:
1. Перейдите на [страницу подключений](https://datalens.yandex.ru/connections).
1. Нажмите кнопку **Создать подключение**.
1. Выберите коннектор **ClickHouse**.
1. Задайте **Имя подключения**. Имя может быть произвольным.
1. Выберите тип подключения:
 
   {% list tabs %}

    - Выбрать в Облаке
     
      Укажите параметры подключения для БД {{ CH }}, доступной в {{ yandex-cloud }}:
      
      - **Кластер**. Укажите кластер из списка доступных кластеров {{ CH }}. Если у вас нет доступного кластера, нажмите кнопку **Создать новый**.
      
        {% note info %} 
                
          В списке кластеров отображаются кластеры {{ CH }}:
            
           - с правами доступа для пользователя создающего подключение;
           - созданные в том же каталоге, что и экземпляр {{ datalens-short-name }}.     
                
        {% endnote %}   
                
      - **Имя хоста**. Выберите имя хоста из списка доступных в кластере {{ CH }}.  
      - **Порт HTTP-интерфейса**. Укажите порт подключения к {{ CH }}. Порт по умолчанию — 8443.
      - **Имя пользователя**. Укажите имя пользователя для подключения к {{ CH }}.
        
           {% include [datalens-db-note](../../../_includes/datalens/datalens-db-note.md) %}
             
      - **Пароль**. Укажите пароль для пользователя.  
      - **HTTPS**. Проверьте, что опция безопасного подключения активирована.
   
    - Указать вручную
     
      Укажите параметры подключения для внешней БД {{ CH }}:
     
      - **Имя хоста**. Укажите путь до хоста или IP-адрес хоста {{ CH }}.  
      - **Порт HTTP-интерфейса**. Укажите порт подключения к {{ CH }}. Порт по умолчанию — 8443.
      - **Имя пользователя**. Укажите имя пользователя для подключения к {{ CH }}.
           
         {% include [datalens-db-note](../../../_includes/datalens/datalens-db-note.md) %}
                   
      - **Пароль**. Укажите пароль для пользователя.  
      - **HTTPS**. Активируйте опцию безопасного подключения, если ваш сервер поддерживает протокол HTTPS. 
     
   {% endlist %}   
           
1. Нажмите **Сохранить**. Подключение появится в списке.

{% note info %}

Вы можете проверить подключение к хосту перед созданием. Для этого нажмите кнопку **Проверить подключение**.

{% endnote %}
 