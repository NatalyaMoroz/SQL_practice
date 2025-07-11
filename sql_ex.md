#1

Дима и Миша пользуются продуктами от одного и того же производителя.  
Тип Таниного принтера не такой, как у Вити, но признак "цветной или нет" - совпадает.  
Размер экрана Диминого ноутбука на 3 дюйма больше Олиного.  
Мишин ПК в 4 раза дороже Таниного принтера.  
Номера моделей Витиного принтера и Олиного ноутбука отличаются только третьим символом.  
У Костиного ПК скорость процессора, как у Мишиного ПК; объем жесткого диска, как у Диминого ноутбука; объем памяти, как у Олиного ноутбука, а цена - как у Витиного принтера.  
Вывести все возможные номера моделей Костиного ПК.
  

*Схема БД состоит из четырех таблиц:  
Product(maker, model, type)  
PC(code, model, speed, ram, hd, cd, price)  
Laptop(code, model, speed, ram, hd, price, screen)  
Printer(code, model, color, type, price)*

```sql
WITH vitya_printer AS (
    SELECT DISTINCT p.type, p.color, p.price, p.model
    FROM Printer AS p
    JOIN Laptop AS l
      ON SUBSTRING(p.model, 1, 2) = SUBSTRING(l.model, 1, 2)
         AND SUBSTRING(p.model, 4, 1) = SUBSTRING(l.model, 4, 1)
         AND SUBSTRING(p.model, 3, 1) <> SUBSTRING(l.model, 3, 1)
),
tanya_printer AS (
    SELECT *
    FROM Printer
    WHERE type NOT IN (SELECT type FROM vitya_printer)
      AND color IN (SELECT color FROM vitya_printer)
),
misha_pc AS (
    SELECT DISTINCT pr.maker, pc.speed, pc.price
    FROM PC AS pc
    JOIN Product AS pr ON pc.model = pr.model
    JOIN tanya_printer AS tp ON pc.price = tp.price * 4
),
olya_laptops AS (
    SELECT l.screen, l.ram
    FROM Laptop AS l
    JOIN vitya_printer AS vp
      ON SUBSTRING(vp.model, 1, 2) = SUBSTRING(l.model, 1, 2)
     AND SUBSTRING(vp.model, 4, 1) = SUBSTRING(l.model, 4, 1)
     AND SUBSTRING(vp.model, 3, 1) <> SUBSTRING(l.model, 3, 1)
),
dima_laptops AS (
    SELECT l.hd, l.screen
    FROM Laptop AS l
    JOIN Product AS p ON l.model = p.model
    JOIN misha_pc AS m ON p.maker = m.maker
),
olya_dima AS (
    SELECT o.ram AS olya_laptop, d.hd AS dima_laptop
    FROM olya_laptops AS o
    JOIN dima_laptops AS d ON d.screen = o.screen + 3
)

SELECT pc.model
FROM PC AS pc
JOIN misha_pc AS m ON pc.speed = m.speed
JOIN olya_dima AS od ON pc.hd = od.dima_laptop AND pc.ram = od.olya_laptop
JOIN vitya_printer AS vp ON pc.price = vp.price;
```
```
