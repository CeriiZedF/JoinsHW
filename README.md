#Querry SQL
--1. Показать названия и категории товаров, поставщиками которых являются ООО "Паньки" или ООО «Какие люди»
USE Magasine
SELECT p.name [Name], c.name [Category]
FROM Product p 
JOIN Category c ON p.id_category = c.id
JOIN Producer pr ON p.id_producer = pr.id AND pr.name = 'Рент' OR pr.name = 'Авент'

--2. Выбрать все товары с указанием их поставщика, имя производителя которых не содержит букв [АКМ], и категория которых не "Крупы"
USE Magasine
SELECT p.name [Name], c.name [Category], pr.name [Producer]
FROM Product p 
JOIN Category c ON p.id_category = c.id AND c.name != 'Крупы'
JOIN Producer pr ON p.id_producer = pr.id
WHERE pr.name NOT LIKE '%[АКМ]%'

--3. Показать названия и категории товаров с указанием поставщика и страны производителя. 
--Условие: страна производителя не Украина, не Молдова и не Польша, цена поставки менее 50 гривен, 
--а дата поставки от 10.05.2023 и по сей день
USE Magasine
SELECT p.name [Name], p.price[Price], c.name [Category], pr.name [Producer], co.name [Country]
FROM Product p 
JOIN Delivery d ON d.id_product = p.id AND d.date_of_delivery > CONVERT(date, '2023-05-10')
JOIN Category c ON p.id_category = c.id AND p.price < 50
JOIN Producer pr ON p.id_producer = pr.id 
JOIN Address a ON pr.id_address = a.id
JOIN City ci ON a.id_city = ci.id
JOIN Region r ON ci.id_region = r.id
JOIN Country co ON r.id_country = co.id AND co.name != 'США' AND co.name != 'Молдова' AND co.name != 'Польша'

--4. Показать все кондитерские и безалкогольные товары, которых было продано более 100. 
--Показать информацию о поставщике и производителе
USE Magasine
SELECT p.name [Name], c.name [Category], s.quatity [Sale], pr.name [Producer], su.name[Supplier]
FROM Product p 
JOIN Producer pr ON p.id_producer = pr.id
JOIN Delivery d ON p.id = d.id_product
JOIN Supplier su ON su.id = d.id_supplier
JOIN Sale s ON s.id_product = p.id AND s.quatity > 100
JOIN Category c ON p.id_category = c.id 
WHERE c.name = 'Овощи' OR c.name = 'Техника'

--5. Сделать запрос на выборку информации о поставке товаров в следующем виде: 
--название товара и его поставщика, категории, дата поставки и общую стоимость поставленных товаров. 
--Условие: только трёх указанных в запросе поставщиков. Отсортировать названия товаров в алфавитном порядке
USE Magasine
Select TOP 3 p.name[Name], s.name[Supplier Name], c.name[Category], d.date_of_delivery[Date], d.price * d.quatity[Sum]
FROM Product p
JOIN Delivery d ON p.id = d.id_product
JOIN Supplier s ON s.id = d.id_supplier
JOIN Category c ON p.id_category = c.id
ORDER BY p.name

--6. Сделать запрос на выборку о продажах товаров в следующем виде:
--название товара и его производителя, полный адрес (страна, город, улица) 
--производителя в одной ячейке, категории, дате продажи и общей стоимости продажи. 
--Условие: выведенная информация не должна касаться двух указанных в запросе производителей. 
--Отсортировать общую стоимость продаж в порядке убывания

USE Magasine
Select p.name[Name], s.name[Supplier Name], (co.name + ', ' + ci.name + ', ' + a.street)[Full Address] ,c.name[Category], sa.[date_of_sale], sa.price * sa.quatity[Sum]
FROM Product p
JOIN Delivery d ON p.id = d.id_product
JOIN Supplier s ON s.id = d.id_supplier AND s.name != 'Vantes' AND s.name != 'Huka'
JOIN Address a ON s.id_address = a.id
JOIN City ci ON a.id_city = ci.id
JOIN Region r ON ci.id_region = r.id
JOIN Country co ON r.id_country = co.id
JOIN Category c ON p.id_category = c.id
JOIN Sale sa ON p.id = sa.id_product
ORDER BY p.name DESC
