#### Квоты {#quotas}
Вид ограничения | Значение
----- | -----
Геокодирование* (количество уникальных записей в сутки) в тарифе Бесплатный | 0
Геокодирование* (количество уникальных записей в сутки) в тарифе Стандарт | 25 000

\* Функции геокодирования — [GEOCODE](../../datalens/function-ref/GEOCODE.md) и [GEOINFO](../../datalens/function-ref/GEOINFO.md).


#### Общие лимиты {#common-limits}
Вид ограничения | Значение
----- | -----
Количество экземпляров DataLens в одном облаке | 1
Максимальный размер CSV-файла | 200 МБ
Максимальное количество строк материализованных данных<br>в одном датасете | 100 000 000

#### Лимиты данных в чартах {#chart-data-limits}

Каждый чарт имеет ограничение на количество данных, которые он может отобразить.

При каждой визуализации чарта {{ datalens-short-name }} делает запрос в датасет.
Если количество строк в ответе превышает лимит, {{ datalens-short-name }} не отобразит чарт. 

Вы можете изменить количество строк данных в ответе через параметры запроса, например, с помощью фильтрации.

Тип чарта | Количество строк данных из датасета,<br/>которое может отобразить чарт
----- | -----
**Диаграммы** |
Линейная диаграмма | 75 000
Диаграмма с областями<br/>Нормированная диаграмма с областями | 75 000
Столбчатая диаграмма<br/>Нормированная столбчатая диаграмма | 75 000
Линейчатая диаграмма<br/>Нормированная линейчатая диаграмма | 75 000
Точечная диаграмма | 75 000
Круговая диаграмма | 1 000
Древовидная диаграмма | 1 000
**Таблицы** |
Таблица | 100 000
Сводная таблица | 50 000
**Геокарты** | 
Точечная карта | 40 000
Фоновая карта | 40 000
Тепловая карта | 60 000