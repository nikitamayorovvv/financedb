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

https://github.com/nikitamayorovvv/financedb/blob/main/create%20tables
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
После того, как база данных и ее таблицы были созданы, я заполнил их данными.

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
('Афонин', 'Анатолий', 'Евгеньевич', '0.9','2'),
('Богомолов', 'Алексей', 'Александрович', '0.8','2'),
('Боденов', 'Александр', 'Михайлович', '0.7','2'),
('Трунова', 'Евгения', 'Петровна', '0.7','1'),
('Кузьмина', 'Анна', 'Андреевна', '0.6','4'),
('Фаттахов', 'Роман', 'Ахмедович', '0.4','2'),
('Майоров', 'Никита', 'Александрович', '0.9','3'),
('Казирод', 'Иван', 'Петрович', '1','2'),
('Сосновский', 'Александр', 'Рудольфович', '1','5');

INSERT INTO positions (name_position, salary) VALUES
('Преподаватель', '25000'),
('Инструктор', '20000'),
('Администратор', '20000'),
('Уборщица', '10000'),
('Управляющий', '45000');

```
### Выполнение поисковых запросов
После того как я база данных была заполнена, я смог приступить к самой интересной части - выполнению поисковых запросов. Сначала я провел с помощью команды WHERE серию запросов с условием и убедился. что разработанная БД работает корректно.
  
  ![image](https://user-images.githubusercontent.com/99638036/226462296-08e2d6a0-b9ae-497d-ac11-8a8fdea2a9ef.png)  
    
    
  Затем выполнил еще несколько запросов с оператором JOIN  
    
    ![image](https://user-images.githubusercontent.com/99638036/226463226-192312aa-f25f-4333-8052-c40b3ecc7fe6.png)  
      
      После я выполнил еще несколько запросов, их можно найти в [этом файле]
