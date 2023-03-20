# FinanceDB | Разработка базы данных на SQL

Этот pet проект я сделал, чтобы закрепить навыки, полученные в рамках дисциплины "Разработка баз данных", которую нам читали в вузе. Мне эта дисциплина показалась интересной, поэтому я самостоятельно изучил подробнее SQL и решил создать свою первую настоящую базу данных.  
Какое-то время я подрабатывал в автошколе администратором и имел дело с финансовыми отчетами, которые велись по старинке в Excel. Мне стало интересно создать для этого базу данных, которая в дальнейшем могла бы лечь в основу целой информационной системы.  
Таким образом, я определился с предметной областью своего проекта — «Финансовая деятельность автошколы». Это система, в которой хранится вся бухгалтерская информация. Она позволяет выполнять финансовый анализ, составлять бухгалтерскую отчетность предприятия. Все входящие и исходящие платежи загружаются в систему с определенными атрибутами, благодаря чему в дальнейшем есть возможность анализировать информацию по продажам, выплатам заработной платы сотрудникам, выплатам налогов и др.  
### Логическая и физическая схемы
Для реализации этой задачи требуется хранить и управлять информацией о клиентах, входящих платежах, исходящих платежах, способах оплаты, ценах на услуги, категориях затрат. Визуализировать все эти сущности и атрибуты мне удалось с помощью реляционной модели будущей БД. Сначала я разработал логическую модель БД, на которой концептуально были определены все сущности и атрибуты:  
![image](https://user-images.githubusercontent.com/99638036/226135347-60497e10-3993-401e-9b1b-d245b5f77f62.png)  
А затем, учитывая какие типы данных реализованы в СУБД MySQL, я дополнил схему, превратив ее в физическую модель разрабатываемой БД:  

![1111](https://user-images.githubusercontent.com/99638036/226135288-e433d724-41d8-4877-9bc7-e34c7b34bcae.png)
### Создание базы данных и таблиц
После того, как логическая и физическая модели базы данных сформированы, я создал саму базу данных и набор таблиц, которые будут хранить данные.  
  
  ![image](https://user-images.githubusercontent.com/99638036/226167101-e663d211-5ed2-4133-8282-5c379b0f1c71.png)  
    
    
 С помощью [этого скрипта]( https://github.com/nikitamayorovvv/financedb/blob/main/create%20tables), я создал все необходимые таблицы:  
    
```
create table income (
id_payment INT NOT NULL AUTO_INCREMENT,
date DATE NOT NULL, 
payment_method VARCHAR(45) NOT NULL,
amount INT NOT NULL,
customer_id INT NOT NULL,
article_income_id INT NOT NULL,
product_id INT,
PRIMARY KEY(id_payment,customer_id)
);

create table list_incomes (
id_article_income INT PRIMARY KEY NOT NULL AUTO_INCREMENT,
name_article_income VARCHAR(45) NOT NULL
);

create table price_list (
id_product INT PRIMARY KEY NOT NULL AUTO_INCREMENT,
name_product VARCHAR(45) UNIQUE NOT NULL,
product_price FLOAT UNSIGNED NOT NULL
);

create table outcome  (
id_payment INT NOT NULL AUTO_INCREMENT,
date DATE NOT NULL, 
payment_method VARCHAR(45) NOT NULL,
amount FLOAT UNSIGNED NOT NULL, 
customer_id INT, 
article_outcome_id INT NOT NULL,
employee_id INT,
PRIMARY KEY(id_payment)
);

create table list_outcomes (
id_article_outcome INT PRIMARY KEY NOT NULL AUTO_INCREMENT,
name_article_outcome VARCHAR(45) NOT NULL
);

create table employees (
id_employee INT PRIMARY KEY NOT NULL AUTO_INCREMENT,
last_name VARCHAR(45) NOT NULL,
name VARCHAR(45) NOT NULL,
middle_name VARCHAR(45),
KPI FLOAT CHECK (KPI >= 0 AND KPI <= 1) NOT NULL,
position_id INT NOT NULL
);

create table positions (
id_position INT PRIMARY KEY NOT NULL AUTO_INCREMENT,
name_position VARCHAR(45) UNIQUE NOT NULL,
salary FLOAT
);



ALTER TABLE income
ADD CONSTRAINT FK_income_to_data_customers FOREIGN KEY (customer_id) REFERENCES data_customers (id_customer) ON DELETE RESTRICT ON UPDATE CASCADE;

ALTER TABLE income
ADD CONSTRAINT FK_income_to_list_incomes FOREIGN KEY (article_income_id) REFERENCES list_incomes (id_article_income) ON DELETE RESTRICT ON UPDATE CASCADE;

ALTER TABLE income
ADD CONSTRAINT FK_income_to_price_list FOREIGN KEY (product_id) REFERENCES price_list (id_product) ON DELETE RESTRICT ON UPDATE CASCADE;

ALTER TABLE outcome
ADD CONSTRAINT FK_outcome_to_data_customers FOREIGN KEY (customer_id) REFERENCES data_customers (id_customer) ON DELETE RESTRICT ON UPDATE CASCADE;

ALTER TABLE outcome
ADD CONSTRAINT FK_outcome_to_list_outcomes FOREIGN KEY (article_outcome_id) REFERENCES list_outcomes (id_article_outcome) ON DELETE RESTRICT ON UPDATE CASCADE;

ALTER TABLE outcome
ADD CONSTRAINT FK_outcome_to_employees FOREIGN KEY (employee_id) REFERENCES employees (id_employee) ON DELETE RESTRICT ON UPDATE CASCADE;

ALTER TABLE employees
ADD CONSTRAINT FK_employees_to_positions FOREIGN KEY (position_id) REFERENCES positions (id_position) ON DELETE RESTRICT ON UPDATE CASCADE;
```
### Заполнение базы данных и таблиц
После того, как база данных и ее таблицы были созданы, я заполнил их синтезированными данными.

```
INSERT INTO data_customers (last_name, name, middle_name, telephone_number) VALUES
('Ванюшин', 'Степан', 'Романович', '89265523062'),
('Менькова', 'Ирина', 'Петровна', '89154248541'),
('Гозенко', 'Татьяна', 'Максимовна', '89153425339'),
('Григорьев', 'Владислав', 'Сергеевич', '89629282121'),
('Воронцова', 'Наталья', 'Александровна', '89019576972'),
('Лахова', 'Кристина', 'Алексеевна', '89269783221'),
('Голубева', 'Елена', 'Николаевна', '89169012463'),
('Рыжкин', 'Дмитрий', 'Антонович', '89770425721'),
('Строе', 'Анна', 'Ионовна', '89047283411'),
('Трунтаева', 'Анна', 'Юрьевна', '89807917290'),
('Горошкова', 'София', 'Валентиновна', '80642675285'),
('Подвысоцкая', 'Ольга', 'Андреевна', '80858901008'),
('Государев', 'Дмитрий', 'Вячеславович', '89162933301'),
('Галенко', 'Ярослав', 'Сергеевич', '89774591771'),
('Шпак', 'Александрина', 'Васильевна', '89268718009'),
('Чумак', 'Андрей', 'Сергеевич', '89250772843'),
('Борщ', 'Николай', 'Владимирович', '89665553555'),
('Клименко', 'Андрей', 'Владимирович', '89068982356'),
('Куприянов', 'Кирилл', 'Сергеевич', '89850478969'),
('Машкина', 'Анна', 'Михайловна', '89163034367'),
('Белов', 'Михаил', 'Владимирович', '89200417275'),
('Дворцов', 'Даниил', 'Владимирович', '89262917429'),
('Карамова', 'Алина', 'Гамлетовна', '89153887143'),
('Павлова', 'Кристина', 'Прохоровна', '89295441265'),
('Козак', 'Юлия', 'Михайловна', '89154480201'),
('Бойчук', 'Ульяна', 'Игоревна', '89168403600'),
('Минин', 'Геннадий', 'Алексеевич', '89017566706'),
('Хэ', 'Янг', '', '89652737431'),
('Оварова', 'Нялика', '', '89998314648'),
('Ван', 'Хи', '', '89175817546');

INSERT INTO income (date, payment_method, amount, customer_id, article_income_id, product_id) VALUES
('2022-10-03', 'Наличные', '13300', '2', '1', '1'),
('2022-11-03', 'Наличные', '13300', '2', '1', '1'),
('2022-12-03', 'Карта', '13300.0', '2', '1', '1'),
('2022-10-04', 'Карта', '8900.0', '3', '1', '2'),
('2022-11-08', 'Наличные', '13300.0', '4', '1', '5'),
('2022-11-08', 'Наличные', '100000.0', '1', '2', NULL),
('2022-11-09', 'Карта', '3000.0', '5', '3', NULL),
('2022-11-04', 'Карта', '13300.0', '3', '1', '2'),
('2022-11-04', 'Карта', '13300.0', '3', '1', '2'),
('2022-12-08', 'Карта', '15300.0', '4', '1', '5'),
('2022-12-22', 'Наличные', '15300.0', '4', '1', '5');



INSERT INTO list_incomes (name_article_income) VALUES
('Платеж за образовательный курс'),
('Внесение наличных на счет'),
('Предоплата'),
('Другое');


INSERT INTO price_list (name_product, product_price) VALUES
('МКПП полн', '39900'),
('МКПП полн дистанц', '39900'),
('МКПП сокр', '29500'),
('МКПП сокр дистанц', '29500'),
('АКПП полн', '42700'),
('АКПП полн дистанц', '42700'),
('АКПП сокр', '29900'),
('АКПП сокр дистанц', '29900'),
('кат А дистанц', '31500'),
('кат А полн', '31500'),
('кат А1 дистанц', '31500'),
('кат А1', '31500'),
('Замена прав', '24600'),
('Замена прав дистанц', '24600'),
('А+В МКПП полн', '47100'),
('А+В МКПП сокр', '36700'),
('А+В МКПП полн дистанц', '47100'),
('А+В МКПП сокр дистанц', '36700'),
('А+В АКПП полн', '49900'),
('А+В АКПП сокр', '37100'),
('А+В АКПП полн дистанц', '49900'),
('А+В АКПП сокр дистанц', '37100');


INSERT INTO outcome (date, payment_method, amount, customer_id, article_outcome_id, employee_id) VALUES
('2022-08-03', 'Счет', '50000.0', NULL, '2', NULL),
('2022-08-05', 'Cчет', '40000.0', NULL, '1', '1'),
('2022-09-09', 'Наличные', '13300.0', '14','1', '1'),
('2022-09-11', 'Счет', '7000.0', NULL, '5', NULL),
('2022-09-13', 'Наличные', '12000.0', NULL, '6', NULL),
('2022-09-21', 'Наличные', '14540.0', NULL, '7', NULL),
('2022-09-28', 'Счет', '50000.0', NULL, '4', NULL),
('2022-10-05', 'Счет', '38000.0', NULL, '1', '8'),
('2022-10-15', 'Наличные', '40000.0', NULL, '1', '9'),
('2022-11-30', 'Счет', '90000.0', NULL, '1', '10');

INSERT INTO list_outcomes (name_article_outcome) VALUES
('Выплата заработной платы'),
('Аренда офиса'),
('Хозтовары'),
('Реклама'),
('Возврат ученику'),
('Инкассация'),
('Комунальные платежи'),
('Оплата связи'),
('Учебные материалы'),
('Разовые затраты');

INSERT INTO employees (last_name, name, middle_name, KPI, position_id) VALUES
('Потапов', 'Сергей', 'Викторович', '0.6','1'),
('Абзалов', 'Анатолий', 'Евгеньевич', '0.9','2'),
('Богомольный', 'Алексей', 'Александрович', '0.8','2'),
('Бодоев', 'Александр', 'Михайлович', '0.7','2'),
('Трунникова', 'Евгения', 'Петровна', '0.7','1'),
('Кузьмина', 'Анна', 'Андреевна', '0.6','4'),
('Фаттанов', 'Роман', 'Ахмедович', '0.4','2'),
('Майоров', 'Никита', 'Александрович', '0.9','3'),
('Дукельский', 'Иван', 'Петрович', '1','2'),
('Сосновский', 'Александр', 'Рудольфович', '1','5');

INSERT INTO positions (name_position, salary) VALUES
('Преподаватель', '25000'),
('Инструктор', '20000'),
('Администратор', '20000'),
('Уборщица', '10000'),
('Управляющий', '45000');

```
### Выполнение поисковых запросов
После того как я база данных была заполнена, я смог приступить к выполнению поисковых запросов. Сначала я провел с помощью команды WHERE серию запросов с условием и убедился. что разработанная БД работает корректно.
  
  ![image](https://user-images.githubusercontent.com/99638036/226462296-08e2d6a0-b9ae-497d-ac11-8a8fdea2a9ef.png)  
    
    
  Затем выполнил еще несколько запросов с оператором JOIN  
    
  ![image](https://user-images.githubusercontent.com/99638036/226463226-192312aa-f25f-4333-8052-c40b3ecc7fe6.png)  
    
 После я выполнил еще несколько запросов, их можно найти в [этом файле](https://github.com/nikitamayorovvv/financedb/blob/main/database%20queries.txt)
 ### Создание функций и триггеров
 И наконец, я добрался до самой сложной, но от этого самой интересной части работы - это создание фунцкий и триггеров. Я старался написать максимально практичные функции и триггеры, которые могли бы сильно упростить работу мне, как администратору автошколы, который формирует короткие финансовые отчеты. Ниже разобраны некоторые из них, остальные можно посмотреть в [этом файле](https://github.com/nikitamayorovvv/financedb/blob/main/stored%20procedures.txt)  
 
Например, вот функция, которая считает ЗП исходя из базового оклада и показателя эффективности сотрудника (KPI), но знать ей нужно лишь ФИО сотрудника, остальное она сама подтянет из БД.  
```
DROP FUNCTION IF EXISTS func_zp;

delimiter $$
CREATE FUNCTION func_zp(ln VARCHAR(45),n VARCHAR(45), mn VARCHAR(45))     
RETURNS FLOAT
DETERMINISTIC
BEGIN
	DECLARE zp FLOAT DEFAULT 0;
	DECLARE idd INT DEFAULT 0;
	DECLARE sal FLOAT DEFAULT 0;
	DECLARE kp FLOAT DEFAULT 0;
	SELECT id_employee FROM employees 
	WHERE last_name=ln AND name=n AND middle_name=mn INTO idd;
	SELECT salary FROM employees 
	INNER JOIN positions ON employees.position_id=positions.id_position  
	WHERE id_employee = idd INTO sal;
	SELECT KPI FROM employees 
	INNER JOIN positions ON employees.position_id=positions.id_position  
	WHERE id_employee = idd INTO kp;
	SET zp=sal*(1+kp);
	RETURN zp; 
END$$
delimiter ;

SELECT func_zp('Майоров', 'Никита', 'Александрович');
```
Вызов функции ниже на рисунке:  

![image](https://user-images.githubusercontent.com/99638036/226471250-1f54a921-af11-4072-a1d2-5bd275fcf268.png)  
  
 Следующая функция считает средний чек от продаж в определенный период:
 ```
 DROP FUNCTION IF EXISTS func_middle_chek;

delimiter $$
CREATE FUNCTION func_middle_chek(fd DATE, sd DATE)   
RETURNS FLOAT
DETERMINISTIC
BEGIN
	DECLARE sum_inc FLOAT DEFAULT 0;
	DECLARE count_chek FLOAT DEFAULT 0;
	DECLARE mid_chek FLOAT DEFAULT 0;

	SELECT sum(amount) FROM income
	WHERE article_income_id = 1 AND
	date BETWEEN fd and sd INTO sum_inc;
	SELECT count(amount) FROM income
	WHERE article_income_id = 1 AND
	date BETWEEN fd and sd INTO count_chek;
	SET mid_chek=sum_inc/count_chek;
	
	RETURN mid_chek; 
END$$
delimiter ;

SELECT func_middle_chek('2022-10-03','2022-11-09');
```
Результат работы функции представлен на рисунке ниже:  
![image](https://user-images.githubusercontent.com/99638036/226471754-ad958d2d-f402-403d-8752-8711778aba0f.png)  
  
Следующая функция не менее полезная: она позволяет считать бухгалтерский баланс
```
DROP FUNCTION IF EXISTS func_balance;

delimiter $$
CREATE FUNCTION func_balance()     
RETURNS FLOAT
DETERMINISTIC
BEGIN
	DECLARE inc FLOAT DEFAULT 0;
	DECLARE outc INT DEFAULT 0;
	DECLARE bal FLOAT DEFAULT 0;

	SELECT sum(amount) FROM income INTO inc;
	SELECT sum(amount) FROM outcome INTO outc;
	SET bal=inc-outc;
	RETURN bal; 
END$$
delimiter ;

SELECT func_balance();
```
Результат работы функции ниже:    

![image](https://user-images.githubusercontent.com/99638036/226472148-22f1ff43-afe1-46fb-9a46-a6f1488a1891.png)  
    
Также я создал триггер, исправляющий некорректную запись значения в столбец с методом оплаты: 
```
CREATE TRIGGER newtrg3
BEFORE INSERT ON income
FOR EACH ROW
BEGIN 
	SET NEW.payment_method = CONCAT(UPPER(LEFT(NEW.payment_method , 1)), LOWER(SUBSTRING(NEW.payment_method, 2)));
	IF NEW.payment_method = 'Card' OR NEW.payment_method = 'Картой' THEN
		SET NEW.payment_method = 'Карта';
	END IF;
END;
$$
```
Проверка работы триггера:  

Добавляем строку с некорректным значением в столбце "payment_method"  

![image](https://user-images.githubusercontent.com/99638036/226473241-a8c24e2b-794d-42dd-a6cd-bc9651155536.png)  
Видим, что созданный мной триггер исправил эту ошибку  

![image](https://user-images.githubusercontent.com/99638036/226473273-d2129e39-a94d-45e7-8edd-1b0d1e6257e2.png)  
  
  
В нашей автошколе было очень много иностранных учеников, у которых чаще всего нет отчества, поэтому я написал триггер, заменяющий пустые значения в столбце с отчеством клиента на NONE:
```
CREATE TRIGGER newtrg5
BEFORE INSERT ON data_customers
FOR EACH ROW
BEGIN 
	IF NEW.middle_name = ' ' THEN
		SET NEW.middle_name = NONE;
	END IF;
END;
$$
```
Попробуем добавить информацию о новом ученике:    

![image](https://user-images.githubusercontent.com/99638036/226474307-182a0587-a74e-4f47-92d0-95fb0339745d.png)  
Видим, что благодаря триггеру столбец с отчеством заполнился не пробелом, а значением NONE:    

![image](https://user-images.githubusercontent.com/99638036/226474529-13c02022-f088-4276-bf6a-16f9c77774f8.png)



