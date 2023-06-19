# SQL

### ПОРЯДОК ОПЕРАТОРОВ В ЗАПРОСЕ

```
SELECT 
FROM 
WHERE 
GROUP BY 
HAVING 
ORDER BY 
LIMIT; 
```

#### Порядок операторов в запросе с оконными функциями
```
SELECT 
    [column_1], 
    [column_2],
    [window_function]() OVER [window_name]
FROM 
    [table_name]
WHERE 
    [...]
GROUP BY 
    [...]
HAVING 
    [...]
WINDOW [window_name] AS (
    PARTITION BY [...]
    ORDER BY [...]
    [window_frame])
ORDER BY 
    [...]
LIMIT;
```
### ПЕРЕВОД В ИНОЙ ТИП ДАННЫХ

```
toFloat64OrNull(column) -- перевод данных из str к числу с точкой
SELECT '2022-12-31'::DATE -- преобразование текста в дату
```

### РАБОТА СО СТРОКАМИ
```
SELECT CONCAT('SQL', ' ', 'Simulator ', 2022) 
-- принимает на вход несколько аргументов и возвращает результат их последовательного сложения друг с другом
```

### ФУНКЦИИ
```
CASE  
WHEN logical_expression_1 THEN expression_1
WHEN logical_expression_2 THEN expression_2
...
ELSE expression_else
END AS case_example
```

### ФУНКЦИИ ДЛЯ РАБОТЫ С ЧИСЛАМИ

| функция   | описание работы                                      | пример работы         |
| --------- | --------------------------------------------- | -------------- |
| CEILING(x) | округляет x до целого числа в большую сторону | CEILING(2.2) = 3,<br> CEILING(-3.8) = -3 |
| ROUND(x, k) | округляет x до k знаков после запятой, если k не указано – до целого | ROUND(8.3631) = 8,<br> ROUND(2.74562, 1) = 2.7 |
| FLOOR(x)  | округляет до целого числа в меньшую сторону | FLOOR(9.3) = 9,<br> FLOOR(-7.8) = -8 |
| POWER(x, y) | возведение x в степень y                        | POWER(2, 3) = 8.0 |
| SQRT(x)   | квадратный корень из x                          | SQRT(16) = 4.0,<br> SQRT(9)=3.0. |
| DEGREES(x) | конвертирует x из радиан в градусы     | DEGREES(20) = 1145.91559026164 |
| RADIANS(x) | конвертирует x из градусов в радианы  | RADIANS(45)= 0.7853981633974483 |
| ABS(x)    | модуль числа x                                 | ABS(-5) = 5, ABS(5) = 5 |
| PI()      | выводит число pi                            | SELECT PI() = 3.141592653589793 |


### РАБОТА С ДАТАМИ
```
CAST(column as date_type) -- перевод колонки к формату с датой
DAY(), MONTH(), YEAR() -- извлекают из даты день, месяц и год соответственно. В скобках нужно указать один аргумент - столбец с датами
MONTHNAME(column) -- название месяца
MONTH(column) -- номер месяца из даты
GETDATE() -- возвращает текущую дату и время сервера в формате datetime (год, месяц, день, часы, минуты, секунды)
DATEPART(year, column) -- возвращает значение части даты и времени (год, месяц, день, час, минута, секунда)
DATEDIFF(datepart, startdate, enddate) -- вычисляет разницу между двумя датами в заданной единице измерения
DATEADD(day, 10, column) --  добавляет заданное количество времени к дате (дни, часы, минуты, секунды)
toStartOfMonth(toDateOrNull(column)) -- перевод str столбца к дате с заполнением пропусков (null) и приводом к старту месяца
```
### ОТБОР ДАННЫХ
```
SELECT COALESCE(column, 'filler value') 
-- если значение в строке NULL, оно заменится на значение, указанное вторым аргументом

```
#### ИСПОЛЬЗОВАНИЕ ОПЕРАТОРА LIKE
| шаблон/символ | с чем совпадает? | пример работы | 
| ------------------------ | ------------------------- | -------------------------- |
| %     | строка, содержащая ноль или более символов      | SELECT * FROM list WHERE names LIKE '%П.М.%'<br>ищет все имена с инициалами П.М.     |
| _     | любой одиночный символ      | SELECT * FROM list WHERE names LIKE '_____'<br>ищет все имена состоящие из пяти любых символов     |

### СОЗДАНИЕ И ИЗМЕНЕНИЕ ТАБЛИЦ
```
CREATE TABLE IF NOT EXISTS database_name.table_name ( 
    column_1 UInt32, -- колонка, тип данных
    column_2 UInt32,
    column_3 DateTime('Europe/Moscow'),
    column_4 String
    ) 
```
#### ПОДЧИНЕННЫЕ ТАБЛИЦЫ

В подчиненной таблице можно настроить, какие действия должны выполняться при удалении связанной строки из главной таблицы.<br> Доступны четыре опции:

CASCADE:<br> автоматически удаляет все связанные строки в подчиненной таблице при удалении строки из главной таблицы.<br>
SET NULL:<br> при удалении связанной строки из главной таблицы устанавливает значение NULL для соответствующего столбца в подчиненной таблице. Для этого столбца должна быть разрешена возможность принимать значение NULL.<br>
SET DEFAULT:<br> при удалении связанной строки из главной таблицы устанавливает значение по умолчанию для соответствующего столбца в подчиненной таблице.<br>
RESTRICT:<br> запрещает удаление строки из главной таблицы, если на нее ссылаются строки в подчиненной таблице.<br>
```
CREATE TABLE primary (
    id INT PRIMARY KEY AUTO_INCREMENT, 
    name VARCHAR(50) NOT NULL, 
    personal_number INT NOT NULL,
    phone_number INT,
    salary DECIMAL(8,2), 
    FOREIGN KEY (name)  REFERENCES secondary (name) ON DELETE CASCADE,
    FOREIGN KEY (id, salary)  REFERENCES secondary (primary_id, salary) ON DELETE SET NULL
);
```
### ОБЪЕДИНЕНИЕ ТАБЛИЦ и ЗАПРОСОВ

#### ОБЪЕДИНЕНИЕ ЗАПРОСОВ
```
select column_1, column_2
from table_1
union
select column_1, column_2
from table_2
-- объединяет результаты двух запросов из двух разных таблиц в один результат:
-- всего могут быть следующие варианты:
-- union: объединяет два списка и убирает дубли;
-- union all: объединяет два списка с сохранением дублирующих строк;
-- except: из верхней таблицы оставляет уникальные строки, которые отсутствуют в нижней таблице;
-- intersect: оставляет уникальные строки, которые присутствуют и в верхней и в нижней таблицах
```
### РЕКУРСИВНЫЕ ЗАПРОСЫ
```
WITH RECURSIVE temp_table AS (
    SELECT * FROM table WHERE column_1 = 2020
    UNION
    SELECT table.* FROM table
    JOIN temp_table ON table.column_1 = temp_table.column_1 
        OR table.column_2 = temp_table.column_2 OR table.column_3 = temp_table.column_3)
    SELECT DISTINCT * FROM temp_table;
-- данный запрос находит все строки из таблицы table, 
-- которые имеют совпадения по column_1, column_2 или column_3 с любой строкой, 
-- удовлетворяющей условию column_1 = 2020
```
