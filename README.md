## Информация о проекте
Необходимо организовать систему учета для питомника, в котором живут
домашние и вьючные животные.

## Задание
1. Используя команду cat в терминале операционной системы Linux, создать
два файла Домашние животные (заполнив файл собаками, кошками и
хомяками) и Вьючные животные (заполнив файл лошадьми, верблюдами и
ослами), а затем объединить их. Просмотреть содержимое созданного файла.
Переименовать файл, дав ему новое имя: “Друзья человека”.

![1.png](/1.png)

2. Создать директорию, переместить файл туда.

![2.png](/2.png)

3. Подключить дополнительный репозиторий MySQL. Установить любой пакет
из этого репозитория.

![3.png](/3.png)

4. Установить и удалить deb-пакет с помощью dpkg.

![4.png](/4.png)

5. Выложить [историю команд](/LinuxCommandsHistory.md) в терминале ubuntu
6. Нарисовать [диаграмму](/diagram.drawio), в которой есть класс родительский класс, домашние
животные и вьючные животные, в составы которых в случае домашних
животных войдут классы: собаки, кошки и хомяки, а в класс вьючные животные
войдут: лошади, верблюды и ослы.

![diagram.png](/diagram.png)

7. В подключенном MySQL репозитории создать базу данных “Друзья
человека”
```sql
CREATE DATABASE HUMAN_FRIENDS;
```

8. Создать таблицы с иерархией из диаграммы в БД
```sql
USE HUMAN_FRIENDS;
CREATE TABLE ANIMAL_CLASSES
(
	ID INT AUTO_INCREMENT PRIMARY KEY, 
	CLASS_NAME VARCHAR(20)
);

INSERT INTO ANIMAL_CLASSES (CLASS_NAME)
VALUES ('Вьючные'),
('Домашние');  


CREATE TABLE PACK_ANIMALS
(
	  ID INT AUTO_INCREMENT PRIMARY KEY,
    GENUS_NAME VARCHAR (20),
    CLASS_ID INT,
    FOREIGN KEY (CLASS_ID) REFERENCES ANIMAL_CLASSES (ID) ON DELETE CASCADE ON UPDATE CASCADE
);

INSERT INTO PACK_ANIMALS (GENUS_NAME, CLASS_ID)
VALUES ('Лошади', 1),
('Ослы', 1),  
('Верблюды', 1); 
    
CREATE TABLE HOME_ANIMALS
(
	  ID INT AUTO_INCREMENT PRIMARY KEY,
    GENUS_NAME VARCHAR (20),
    CLASS_ID INT,
    FOREIGN KEY (CLASS_ID) REFERENCES ANIMAL_CLASSES (ID) ON DELETE CASCADE ON UPDATE CASCADE
);

INSERT INTO HOME_ANIMALS (GENUS_NAME, CLASS_ID)
VALUES ('Кошки', 2),
('Собаки', 2),  
('Хомяки', 2); 

CREATE TABLE CATS 
(       
    ID INT AUTO_INCREMENT PRIMARY KEY, 
    NAME VARCHAR(20), 
    BIRTHDAY DATE,
    COMMANDS VARCHAR(50),
    GENUS_ID INT,
    FOREIGN KEY (GENUS_ID) REFERENCES HOME_ANIMALS (ID) ON DELETE CASCADE ON UPDATE CASCADE
);
```
9. Заполнить низкоуровневые таблицы именами(животных), командами
которые они выполняют и датами рождения
```sql
INSERT INTO CATS (NAME, BIRTHDAY, COMMANDS, GENUS_ID)
VALUES ('Мэри', '2019-10-09', 'кис-кис', 1),
('Барсик', '2021-12-07', 'кис-кис', 1); 

CREATE TABLE DOGS 
(       
    ID INT AUTO_INCREMENT PRIMARY KEY, 
    NAME VARCHAR(20), 
    BIRTHDAY DATE,
    COMMANDS VARCHAR(50),
    GENUS_ID INT,
    FOREIGN KEY (GENUS_ID) REFERENCES HOME_ANIMALS (ID) ON DELETE CASCADE ON UPDATE CASCADE
);
INSERT INTO DOGS (NAME, BIRTHDAY, COMMANDS, GENUS_ID)
VALUES ('Джек', '2021-03-04', 'сидеть, голос', 2),
('Джесси', '2018-06-06', 'сидеть, лежать', 2);

CREATE TABLE HAMSTERS 
(       
    ID INT AUTO_INCREMENT PRIMARY KEY, 
    NAME VARCHAR(20), 
    BIRTHDAY DATE,
    COMMANDS VARCHAR(50),
    GENUS_ID INT,
    FOREIGN KEY (GENUS_ID) REFERENCES HOME_ANIMALS (ID) ON DELETE CASCADE ON UPDATE CASCADE
);
INSERT INTO HAMSTERS (NAME, BIRTHDAY, COMMANDS, GENUS_ID)
VALUES ('Шуршик', '2022-12-12', NULL, 3),
('Кнопка', '2022-11-11', NULL, 3);

CREATE TABLE HORSES 
(       
    ID INT AUTO_INCREMENT PRIMARY KEY, 
    NAME VARCHAR(20), 
    BIRTHDAY DATE,
    COMMANDS VARCHAR(50),
    GENUS_ID INT,
    FOREIGN KEY (GENUS_ID) REFERENCES PACK_ANIMALS (ID) ON DELETE CASCADE ON UPDATE CASCADE
);
INSERT INTO HORSES (NAME, BIRTHDAY, COMMANDS, GENUS_ID)
VALUES ('Зевс', '2020-10-10', 'бегом, прыжок', 1),
('Афина', '2018-03-05', 'бегом, шагом', 1);

CREATE TABLE DONKEYS 
(       
    ID INT AUTO_INCREMENT PRIMARY KEY, 
    NAME VARCHAR(20), 
    BIRTHDAY DATE,
    COMMANDS VARCHAR(50),
    GENUS_ID INT,
    FOREIGN KEY (GENUS_ID) REFERENCES PACK_ANIMALS (ID) ON DELETE CASCADE ON UPDATE CASCADE
);
INSERT INTO DONKEYS (NAME, BIRTHDAY, COMMANDS, GENUS_ID)
VALUES ('Бетти', '2018-04-04', NULL, 2),
('Дункан', '2020-07-03', NULL, 2);

CREATE TABLE CAMELS 
(       
    ID INT AUTO_INCREMENT PRIMARY KEY, 
    NAME VARCHAR(20), 
    BIRTHDAY DATE,
    COMMANDS VARCHAR(50),
    GENUS_ID INT,
    FOREIGN KEY (GENUS_ID) REFERENCES PACK_ANIMALS (ID) ON DELETE CASCADE ON UPDATE CASCADE
);
INSERT INTO CAMELS (NAME, BIRTHDAY, COMMANDS, GENUS_ID)
VALUES ('Лили', '2020-02-02', NULL, 3),
('Бобо', '2017-03-04', NULL, 3);
```

10. Удалить из таблицы верблюдов, т.к. верблюдов решили перевезти в другой
питомник на зимовку. Объединить таблицы лошади, и ослы в одну таблицу.
```sql
SET SQL_SAFE_UPDATES = 0;
DELETE FROM CAMELS;

SELECT NAME, BIRTHDAY, COMMANDS FROM HORSES
UNION SELECT  NAME, BIRTHDAY, COMMANDS FROM DONKEYS;
```

11. Создать новую таблицу “молодые животные” в которую попадут все
животные старше 1 года, но младше 3 лет и в отдельном столбце с точностью
до месяца подсчитать возраст животных в новой таблице
```sql
CREATE TEMPORARY TABLE ANIMALS AS 
SELECT *, 'Лошади' as GENUS FROM HORSES
UNION SELECT *, 'Ослы' AS GENUS FROM DONKEYS
UNION SELECT *, 'Собаки' AS GENUS FROM DOGS
UNION SELECT *, 'Кошки' AS GENUS FROM CATS
UNION SELECT *, 'Хомяки' AS GENUS FROM HAMSTERS;

CREATE TABLE YOUNG_ANIMALS AS
SELECT NAME, BIRTHDAY, COMMANDS, GENUS, TIMESTAMPDIFF(MONTH, BIRTHDAY, CURDATE()) AS AGE_IN_MONTH
FROM ANIMALS WHERE BIRTHDAY BETWEEN ADDDATE(CURDATE(), INTERVAL -3 YEAR) AND ADDDATE(CURDATE(), INTERVAL -1 YEAR);
 
SELECT * FROM YOUNG_ANIMALS;
```
12. Объединить все таблицы в одну, при этом сохраняя поля, указывающие на
прошлую принадлежность к старым таблицам.
```sql
SELECT H.NAME, H.BIRTHDAY, H.COMMANDS, PA.GENUS_NAME, YA.AGE_IN_MONTH 
FROM HORSES H
LEFT JOIN YOUNG_ANIMALS YA ON YA.NAME = H.NAME
LEFT JOIN PACK_ANIMALS PA ON PA.ID = H.GENUS_ID
UNION 
SELECT D.NAME, D.BIRTHDAY, D.COMMANDS, PA.GENUS_NAME, YA.AGE_IN_MONTH 
FROM DONKEYS D 
LEFT JOIN YOUNG_ANIMALS YA ON YA.NAME = D.NAME
LEFT JOIN PACK_ANIMALS PA ON PA.ID = D.GENUS_ID
UNION
SELECT C.NAME, C.BIRTHDAY, C.COMMANDS, HA.GENUS_NAME, YA.AGE_IN_MONTH 
FROM CATS C
LEFT JOIN YOUNG_ANIMALS YA ON YA.NAME = C.NAME
LEFT JOIN HOME_ANIMALS HA ON HA.ID = C.GENUS_ID
UNION
SELECT d.NAME, d.BIRTHDAY, d.COMMANDS, HA.GENUS_NAME, YA.AGE_IN_MONTH 
FROM DOGS D
LEFT JOIN YOUNG_ANIMALS YA ON YA.NAME = D.NAME
LEFT JOIN HOME_ANIMALS HA ON HA.ID = D.GENUS_ID
UNION
SELECT HM.NAME, HM.BIRTHDAY, HM.COMMANDS, HA.GENUS_NAME, YA.AGE_IN_MONTH 
FROM HAMSTERS HM
LEFT JOIN YOUNG_ANIMALS YA ON YA.NAME = HM.NAME
LEFT JOIN HOME_ANIMALS HA ON HA.ID = HM.GENUS_ID;
```

13. Создать [класс с Инкапсуляцией методов и наследованием по диаграмме](/System/src/Model).
14. Написать [программу, имитирующую работу реестра домашних животных](/System/src).
В программе должен быть реализован следующий функционал:    
	14.1 Завести новое животное    
	14.2 определять животное в правильный класс    
	14.3 увидеть список команд, которое выполняет животное    
	14.4 обучить животное новым командам    
	14.5 Реализовать навигацию по меню    
15. Создайте [класс Счетчик](/System/src/Controller/Counter.java), у которого есть метод add(), увеличивающий
значение внутренней int переменной на 1 при нажатии “Завести новое
животное” Сделайте так, чтобы с объектом такого типа можно было работать в
блоке try-with-resources. Нужно бросить исключение, если работа с объектом
типа счетчик была не в ресурсном try и/или ресурс остался открыт. Значение
считать в ресурсе try, если при заведении животного заполнены все поля.