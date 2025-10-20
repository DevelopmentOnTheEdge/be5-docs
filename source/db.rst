Типы колонок
============

В be5 определены типы `колонок <ColumnDef>`_ таблиц, независимые от движка СУБД. В коде тип колонок хранится в классе ``com.beanexplorer.enterprise.metadata.model.SqlColumnType``.

У типа всегда есть строковое представление. Имеются следующие типы:

* KEYTYPE — тип, который используется по умолчанию для первичного ключа таблицы (обычно совпадает с BIGINT).
* Текстовые:

  - CHAR — строка фиксированной длины. Длина задаётся в скобках. Например: CHAR(10).
  - VARCHAR — строка произвольной длины вплоть до заданной. Длина задаётся в скобках. Например: VARCHAR(15).
  - TEXT — большое текстовое поле (не меньше 4000 символов).
  - BIGTEXT — очень большое текстовое поле (TODO: уточнить минимальную длину).
* Двоичные:

  - BLOB — бинарные данные (TODO: уточнить минимальную длину).
  - MEDIUMBLOB — бинарные данные (TODO: уточнить минимальную длину).
* Численные:

  - INT — целое знаковое число (минимум 32-битное).
  - INT UNSIGNED — целое беззнаковое число (минимум 32-битное). Поддерживается не всеми движками.
  - BIGINT — целое знаковое число (минимум 64-битное).
  - BIGINT UNSIGNED — целое беззнаковое число (минимум 64-битное). Поддерживается не всеми движками.
  - SMALLINT — целое знаковое число (минимум 16-битное).
  - DECIMAL — дробное число указанной длины с указанной точностью (количество десятичных знаков). Например DECIMAL(10,2).
  - CURRENCY — валюта. Сейчас совпадает с DECIMAL(18,2).
* Дата и время:

  - DATE — дата.
  - DATETIME — дата и время.
  - TIMESTAMP — дата и время. Совпадает с DATETIME во всех движках кроме MySQL. 
* Перечислимые:

  - ENUM — перечисление допустимых значений. Например ENUM(on,off,pending)
  - BOOL — допустимо два значения — 'no' и 'yes' (совпадает с ENUM(no,yes))

Сопоставление типов колонок
---------------------------

.. list-table::
   :header-rows: 1

   * - Тип be5
     - MySQL
     - PostGres
     - DB2
     - MS SQL 2008
     - Oracle
   * - BLOB
     - MEDIUMBLOB
     - BYTEA
     - BLOB(4M)
     - IMAGE
     - BLOB
   * - MEDIUMBLOB
     - MEDIUMBLOB
     - BYTEA
     - BLOB(16M)
     - IMAGE
     - MEDIUMBLOB
   * - TEXT
     - TEXT
     - TEXT
     - CLOB(64K)
     - VARCHAR(MAX)
     - VARCHAR2(4000 CHAR)
   * - BIGTEXT
     - TEXT
     - TEXT
     - CLOB(128K)
     - VARCHAR(MAX)
     - CLOB
   * - VARCHAR
     - VARCHAR (если размер >255, то TEXT)
     - VARCHAR
     - VARCHAR
     - VARCHAR
     - VARCHAR2
   * - CHAR
     - CHAR
     - CHAR
     - CHAR
     - CHAR
     - CHAR
   * - KEYTYPE
     - BIGINT UNSIGNED
     - BIGINT
     - BIGINT
     - BIGINT
     - VARCHAR2(15 CHAR)
   * - INT
     - INT
     - INT
     - INT
     - INT
     - NUMBER(10)
   * - INT UNSIGNED
     - INT UNSIGNED
     - INT
     - INT
     - INT
     - NUMBER(10)
   * - BIGINT
     - BIGINT
     - BIGINT
     - BIGINT
     - BIGINT
     - NUMBER(20)
   * - BIGINT UNSIGNED
     - BIGINT UNSIGNED
     - BIGINT
     - BIGINT
     - BIGINT
     - NUMBER(20)
   * - SMALLINT
     - SMALLINT
     - SMALLINT
     - SMALLINT
     - SMALLINT
     - NUMBER(5)
   * - DECIMAL
     - DECIMAL
     - DECIMAL
     - DECIMAL
     - DECIMAL
     - NUMBER
   * - CURRENCY
     - DECIMAL(18,2)
     - DECIMAL(18,2)
     - DECIMAL(18,2)
     - DECIMAL(18,2)
     - NUMBER(18,2)
   * - DATE
     - DATE
     - DATE
     - DATE
     - DATE
     - DATE
   * - DATETIME
     - DATETIME
     - TIMESTAMP
     - TIMESTAMP
     - DATETIME
     - TIMESTAMP
   * - TIMESTAMP
     - TIMESTAMP
     - TIMESTAMP
     - TIMESTAMP
     - DATETIME
     - TIMESTAMP
   * - ENUM
     - ENUM
     - VARCHAR + CONSTRAINT
     - VARCHAR + CONSTRAINT
     - VARCHAR + CONSTRAINT
     - VARCHAR2 + CONSTRAINT
   * - BOOL
     - ENUM('no','yes')
     - VARCHAR + CONSTRAINT
     - VARCHAR + CONSTRAINT
     - VARCHAR + CONSTRAINT
     - VARCHAR2 + CONSTRAINT
