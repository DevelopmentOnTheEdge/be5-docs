Представление (view)
********************

Layout
======

.. list-table::
   :header-rows: 1

   * - Атрибут
     - Краткое описание
   * - type
     - тип документа на фронтенде
   * - tableBox
     - только для представлений — имя tableBox компонента на фронтенде, синоним (``_displayType_``)
   * - title
     - переопределение генерированного по умолчанию заголовка
   * - hideTitle
     - прячет заголовок (типа entity: view) для представлений
   * - mode
     - "named" — выводить значение колонок по именам, по умолчанию это массив
   * - defaultPageLimit
     - задание лимита записей по умолчанию

Примеры::

   layout: '{"type":"modalForm"}'
   layout: '{"mode":"named"}'
   layout: '{"tableBox":"galleries", "mode":"named", "defaultPageLimit": 30}'


Атрибуты колонок
================

Атрибуты колонок позволяют добавить дополнительную мета-информацию в SQL запросах
для настройки вывода результатов запроса, как правило, в виде таблицы. 

При этом в отличие от встроенных функций той или иной СУБД, эти функции, исполняясь ядром BeanExplorer'а, 
являются платформенно независимыми, то есть SQL запрос может выполняться на любой СУБД.

Атрибуты колонок можно задавать в SQL запросах представлений двумя способами.

1) Встраивание в колонку SQL запроса.

.. code-block:: sql

   SELECT
     someValue AS "Value;<blankNulls/>"
   FROM someTable

В данном примере при помощи ``<blankNulls/>`` мы определяем, что NULL значения при выводе должны заменяться пустыми строками.

Описанный способ оказывается не применим на всех базах данных, поскольку многие из них накладывают ограничения на максимальную длину заголовка колонки. 
Так например Oracle и IBM DB2 не позволяют иметь длину больше 30 символов. В таких случаях можно использовать второй способ.

2) Встраивание в выборку.

.. code-block:: sql

   SELECT
     someValue AS "Value"
     '<blankNulls/>' AS ";Value"
   FROM someTable

Если в выборке встречается колонка, начинающаяся с символа точки с запятой, 
такая колонка интерпретируется как служебная (содержащая не данные, а метаданные). 
В этом случае содержимое колонки считает атрибутом колонки "Value".

Второй способ предоставляет дополнительную гибкость, тем что позволяет менять атрибуты колонок для каждой записи выборки, 
в отличие от первого, в котором атрибут колонки задается только раз на всю выборку. 


.. list-table::
   :header-rows: 1

   * - Атрибут
     - Краткое описание
   * - <link/>
     - генерация перекрестных ссылок между представлениями
   * - <sql/>
     - встраиваемые запросы
   * - <ref/>
     - генерация перекрестных ссылок между представлениями
   * - <grouping/>
     - группировка записей по колонке
   * - <aggregate/>
     - подсчет агрегированных функций
   * - <safexml/>
     - Замена угловых скобок в выводимых значениях на ``&lt;``; ``&gt;``
   * - <quick/>
     - быстрое скрытие/показ полей в таблице
   * - <nosort/>
     - выключение сортировки по колонке
   * - <roles />
     - Управление видимостью колонки в зависимости от роли
   * - <format/>
     - Форматирование выводимого числового значения по маске (``<format mask="###,###,###.00" />``)
     
<link>     
------

Атрибут, генерирующий ссылку на произвольное представление. 
Имя представления и условия фильтрации задаются параметрами атрибута.

Вариант c указанием представления и параметров
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: sql

    SELECT
       <@SHOW_PHOTO />,
       p.ID AS "___persID",
       p.lastName AS "Last Name",
       p.firstName AS "First Name",
       p.middleName AS "Middle Name",
       p.userName AS "User Name",
      '<sql>SELECT d.title
         FROM departments d
            INNER JOIN staff ON staff.personID = <var:___persID /> AND d.ID = staff.departmentID
       </sql>'
        AS "Department",
      '<link table="staff" using="___persID" columns="personID" />' 
        AS ";Department"
    FROM persons p

.. list-table::
   :header-rows: 1

   * - Атрибут
     - Краткое описание
   * - table
     - Название сущности, в которой определено представление
   * - queryName
     - Опциональное название представления. Если не указано, то используется ``All records``
   * - columns
     - Список колонок в целевом представлении, значения которых передаются при открытии ссылки
   * - using
     - Наименования колонок из выборки, значения которых надо передать для ``columns``
   * - class
     - Опциональное значение. Если указано ``open-in-modal`` то представление будет открыто в модальном окне

В приведенном выше примере будет сгенерирован ссылка вида

.. code-block:: text

    https://some.site/#!table/staff/All%20records/personID=1234


Более сложный пример

.. code-block:: sql

    SELECT DISTINCT
      c.CODE, 
      c.competence,
      c.description,
      pb.programPlanID AS "___planID",

      '<sql>
          .... 
      </sql>'
        AS "Распределение",
      '<link table="programCompetences" queryName="Distribution" using="code,___planID" columns="competence,planID" class="open-in-modal" />' 
        AS ";Распределение"
    FROM programCompetences pc
      INNER JOIN programPlanItems ppi ON pc.programPlanItemID = ppi.ID
        INNER JOIN programBlocks pb ON ppi.programBlockID = pb.ID

Откроет в модальном окне ссылку вида

.. code-block:: text

    https://some.site/#!table/programCompetences/Distribution/competence=%D0%A41.1/planID=1


Вариант с генерируемой ссылкой
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Важная особенность такой ссылки, что сохраняется порядок сортировки в колонке при клике по заголовку колонки.

.. code-block:: sql

    SELECT
        CONCAT('groupCard/', CAST(g.ID AS CHAR)) AS "___GroupUrl",
        g.name AS "Group;<link url='___GroupUrl'/>"
    FROM groups g

.. list-table::
   :header-rows: 1

   * - Атрибут
     - Краткое описание
   * - url
     - имя колонки с url, если указан этот атрибут, остальные игнорируются
   * - class
     - css class, по умолчанию ``process-hash-url``

<sql>     
-----

В BeanExplorer существует механизм встраиваемых подзапросов. 
С технической точки зрения, этот механизм скорее является механизмом placeholders, 
поскольку родительский запрос ничего не знает о дочерних подзапросах. 
Дочерние же подзапросы имеют доступ к значениям выбранным родительским запросом.

.. code-block:: sql

    SELECT
      some_column AS "___param",
      '<sql using="___param">SELECT COUNT(*) FROM comments WHERE col = ?</sql>'
      AS "Value;<nosort/>"
    FROM ...
 
 