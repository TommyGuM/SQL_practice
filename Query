1. Отобразите все записи из таблицы company по компаниям, которые закрылись.
SELECT *
FROM company
WHERE status = 'closed'

2. Отобразите количество привлечённых средств для новостных компаний США. Используйте данные из таблицы company. 
Отсортируйте таблицу по убыванию значений в поле funding_total.
SELECT funding_total
FROM company
WHERE category_code = 'news' AND country_code = 'USA'
ORDER BY funding_total DESC

3. Найдите общую сумму сделок по покупке одних компаний другими в долларах. Отберите сделки, которые осуществлялись только за наличные с 2011 по 2013 год включительно.
SELECT SUM(price_amount)
FROM acquisition
WHERE term_code = 'cash' AND EXTRACT (YEAR FROM CAST(acquired_at as date)) IN (2011,2012,2013)

4. Отобразите имя, фамилию и названия аккаунтов людей в поле network_username, у которых названия аккаунтов начинаются на 'Silver'.
SELECT first_name, last_name, network_username
FROM people
WHERE network_username LIKE 'Silver%'

5.Выведите на экран всю информацию о людях, у которых названия аккаунтов в поле network_username содержат подстроку 'money', а фамилия начинается на 'K'.
SELECT *
FROM people
WHERE network_username LIKE '%money%' AND last_name LIKE 'K%'

6.
Для каждой страны отобразите общую сумму привлечённых инвестиций, которые получили компании, зарегистрированные в этой стране. 
Страну, в которой зарегистрирована компания, можно определить по коду страны. Отсортируйте данные по убыванию суммы.
SELECT country_code,SUM(funding_total)
FROM company
GROUP BY country_code
ORDER BY SUM(funding_total) DESC

7.Составьте таблицу, в которую войдёт дата проведения раунда, а также минимальное и максимальное значения суммы инвестиций, привлечённых в эту дату.
Оставьте в итоговой таблице только те записи, в которых минимальное значение суммы инвестиций не равно нулю и не равно максимальному значению.
SELECT CAST(funded_at as date),
       MIN(raised_amount),  
       MAX(raised_amount)
FROM funding_round
GROUP BY CAST(funded_at as date)
HAVING MIN(raised_amount) != 0 AND MIN(raised_amount) != MAX(raised_amount)

8.Создайте поле с категориями:
Для фондов, которые инвестируют в 100 и более компаний, назначьте категорию high_activity.
Для фондов, которые инвестируют в 20 и более компаний до 100, назначьте категорию middle_activity.
Если количество инвестируемых компаний фонда не достигает 20, назначьте категорию low_activity.
Отобразите все поля таблицы fund и новое поле с категориями.
SELECT *,
    CASE
        WHEN invested_companies >= 100 THEN 'high_activity'
        WHEN invested_companies >= 20 THEN 'middle_activity'
        WHEN invested_companies < 20 THEN 'low_activity'
    END                
FROM fund        

9.Для каждой из категорий, назначенных в предыдущем задании, посчитайте округлённое до ближайшего целого числа среднее количество инвестиционных раундов, в которых фонд принимал участие. 
Выведите на экран категории и среднее число инвестиционных раундов. Отсортируйте таблицу по возрастанию среднего.
SELECT ROUND(AVG(investment_rounds)),
       CASE
           WHEN invested_companies>=100 THEN 'high_activity'
           WHEN invested_companies>=20 THEN 'middle_activity'
           ELSE 'low_activity'
       END AS activity
FROM fund
GROUP BY activity
ORDER BY ROUND(AVG(investment_rounds)) ;

10.Проанализируйте, в каких странах находятся фонды, которые чаще всего инвестируют в стартапы. 
Для каждой страны посчитайте минимальное, максимальное и среднее число компаний, в которые инвестировали фонды этой страны, основанные с 2010 по 2012 год включительно.
Исключите страны с фондами, у которых минимальное число компаний, получивших инвестиции, равно нулю. 
Выгрузите десять самых активных стран-инвесторов: отсортируйте таблицу по среднему количеству компаний от большего к меньшему. 
Затем добавьте сортировку по коду страны в лексикографическом порядке.
SELECT country_code,
        MIN(invested_companies),
        MAX(invested_companies),
        AVG(invested_companies)        
FROM fund
WHERE EXTRACT (YEAR FROM CAST(founded_at as date)) IN (2010,2011,2012)
GROUP BY country_code
HAVING MIN(invested_companies) != 0
ORDER BY AVG(invested_companies) DESC, country_code
LIMIT 10

11. Отобразите имя и фамилию всех сотрудников стартапов. Добавьте поле с названием учебного заведения, которое окончил сотрудник, если эта информация известна.
SELECT first_name, last_name, e.instituition
FROM people as p
LEFT JOIN education as e ON e.id=p.id

12.Для каждой компании найдите количество учебных заведений, которые окончили её сотрудники. 
Выведите название компании и число уникальных названий учебных заведений. Составьте топ-5 компаний по количеству университетов.
SELECT c.name,
        COUNT(DISTINCT e.instituition)
FROM people as p
JOIN company as c ON c.id = p.company_id 
JOIN education as e ON e.person_id = p.id 
GROUP BY c.name
ORDER BY COUNT(DISTINCT e.instituition) DESC
LIMIT 5

13.Составьте список с уникальными названиями закрытых компаний, для которых первый раунд финансирования оказался последним
SELECT DISTINCT name
FROM company
WHERE status = 'closed' AND id IN (SELECT company_id
                                          FROM funding_round
                                          WHERE is_first_round = 1 AND is_last_round = 1 )

14. Составьте список уникальных номеров сотрудников, которые работают в компаниях, отобранных в предыдущем задании.
WITH
s as (SELECT id
FROM company
WHERE status = 'closed' AND id IN (SELECT company_id
                                          FROM funding_round
                                          WHERE is_first_round = 1 AND is_last_round = 1 ))
SELECT DISTINCT p.id
FROM s
JOIN people as p ON s.id = p.company_id

15. Составьте таблицу, куда войдут уникальные пары с номерами сотрудников из предыдущей задачи и учебным заведением, которое окончил сотрудник.
WITH
s as (SELECT id
FROM company
WHERE status = 'closed' AND id IN (SELECT company_id
                                          FROM funding_round
                                          WHERE is_first_round = 1 AND is_last_round = 1 ))
SELECT DISTINCT p.id,
        e.instituition       
FROM s
JOIN people as p ON s.id = p.company_id
LEFT JOIN education as e ON p.id = e.person_id
WHERE e.instituition IS NOT NULL

16.Посчитайте количество учебных заведений для каждого сотрудника из предыдущего задания. При подсчёте учитывайте, что некоторые сотрудники могли окончить одно и то же заведение дважды.
WITH
s as (SELECT id
FROM company
WHERE status = 'closed' AND id IN (SELECT company_id
                                          FROM funding_round
                                          WHERE is_first_round = 1 AND is_last_round = 1 ))
SELECT DISTINCT p.id,
        COUNT(e.instituition)
FROM s
JOIN people as p ON s.id = p.company_id
LEFT JOIN education as e ON p.id = e.person_id
WHERE e.instituition IS NOT NULL
GROUP BY p.id

17.Дополните предыдущий запрос и выведите среднее число учебных заведений (всех, не только уникальных), которые окончили сотрудники разных компаний.
Нужно вывести только одну запись, группировка здесь не понадобится.
SELECT AVG(count)
FROM (WITH
s as (SELECT id
FROM company
WHERE status = 'closed' AND id IN (SELECT company_id
                                          FROM funding_round
                                          WHERE is_first_round = 1 AND is_last_round = 1 ))
SELECT DISTINCT p.id,
        COUNT(e.instituition)        
FROM s
JOIN people as p ON s.id = p.company_id
LEFT JOIN education as e ON p.id = e.person_id
WHERE e.instituition IS NOT NULL
GROUP BY p.id) as d

18.Напишите похожий запрос: выведите среднее число учебных заведений (всех, не только уникальных), которые окончили сотрудники Socialnet.
SELECT AVG(count)
FROM (WITH
s as (SELECT id
FROM company
WHERE name = 'Socialnet')
SELECT  p.id,
        COUNT(e.instituition)        
FROM s
JOIN people as p ON s.id = p.company_id
LEFT JOIN education as e ON p.id = e.person_id
WHERE e.instituition IS NOT NULL
GROUP BY p.id) as d

19. Составьте таблицу из полей:
name_of_fund — название фонда;
name_of_company — название компании;
amount — сумма инвестиций, которую привлекла компания в раунде.
В таблицу войдут данные о компаниях, в истории которых было больше шести важных этапов, а раунды финансирования проходили с 2012 по 2013 год включительно.
WITH
-- компании, в истории которых было больше шести важных этапов
c as (SELECT *
FROM company
WHERE milestones > 6), 
--раунды финансирования проходили с 2012 по 2013 год 
r as (SELECT *
FROM funding_round
WHERE CAST(funded_at as date)  BETWEEN '2012-01-01' AND '2013-12-31')

SELECT f.name as name_of_fund,
        c.name as name_of_company,
        raised_amount as amount
        
FROM investment as i 
JOIN c ON c.id = i.company_id
JOIN r ON r.id = i.funding_round_id
JOIN fund as f ON f.id = i.fund_id

20.Выгрузите таблицу, в которой будут такие поля:
название компании-покупателя;
сумма сделки;
название компании, которую купили;
сумма инвестиций, вложенных в купленную компанию;
доля, которая отображает, во сколько раз сумма покупки превысила сумму вложенных в компанию инвестиций, округлённая до ближайшего целого числа.
Не учитывайте те сделки, в которых сумма покупки равна нулю. Если сумма инвестиций в компанию равна нулю, исключите такую компанию из таблицы. 
Отсортируйте таблицу по сумме сделки от большей к меньшей, а затем по названию купленной компании в лексикографическом порядке. Ограничьте таблицу первыми десятью записями.
WITH
c as (SELECT *
      FROM company
      ),
b as (SELECT *
      FROM company
      WHERE funding_total != 0)      
          
SELECT  c.name as name_buy,         
        price_amount,  
        b.name as name_to_sell,
        b.funding_total,
        ROUND(a.price_amount / b.funding_total)
FROM acquisition as a
JOIN c ON c.id = a.acquiring_company_id
JOIN b ON b.id = a.acquired_company_id
WHERE price_amount != 0 
ORDER BY price_amount DESC, name_to_sell
LIMIT 10

21. Выгрузите таблицу, в которую войдут названия компаний из категории social, получившие финансирование с 2010 по 2013 год включительно. 
Проверьте, что сумма инвестиций не равна нулю. Выведите также номер месяца, в котором проходил раунд финансирования.
SELECT name,
        EXTRACT (MONTH FROM CAST(funded_at as date)) as month
FROM funding_round as f 
JOIN company as c ON f.company_id = c.id
WHERE (CAST(funded_at as date) BETWEEN '2010-01-01' AND '2013-12-31') AND (raised_amount != 0) AND c.category_code = 'social'

22. Отберите данные по месяцам с 2010 по 2013 год, когда проходили инвестиционные раунды. Сгруппируйте данные по номеру месяца и получите таблицу, в которой будут поля:
номер месяца, в котором проходили раунды;
количество уникальных названий фондов из США, которые инвестировали в этом месяце;
количество компаний, купленных за этот месяц;
общая сумма сделок по покупкам в этом месяце.
WITH
--месяца
m as (SELECT *, EXTRACT (MONTH FROM CAST(funded_at as date)) as month
      FROM funding_round
      WHERE EXTRACT (YEAR FROM CAST(funded_at as date)) BETWEEN '2010' AND '2013'),
-- купленные компании и цена покупки        
c as  (SELECT EXTRACT (MONTH FROM CAST(acquired_at as date)) as month,
        COUNT(acquired_company_id) as buy_comp,
        SUM(price_amount) as total
FROM acquisition
WHERE EXTRACT (YEAR FROM CAST(acquired_at as date)) BETWEEN '2010' AND '2013'
GROUP BY month),

--  фонды из США
f as (SELECT DISTINCT id
        FROM fund
        WHERE (country_code = 'USA'))

SELECT m.month,
       COUNT(DISTINCT f.id) as inv_usa,
       c.buy_comp,
       c.total
FROM investment as i
JOIN f ON f.id = i.fund_id
JOIN m ON m.id = i.funding_round_id
JOIN c ON c.month = m.month
GROUP BY m.month, c.buy_comp, c.total

23. Составьте сводную таблицу и выведите среднюю сумму инвестиций для стран, в которых есть стартапы, зарегистрированные в 2011, 2012 и 2013 годах. 
Данные за каждый год должны быть в отдельном поле. 
Отсортируйте таблицу по среднему значению инвестиций за 2011 год от большего к меньшему.
WITH
a as (SELECT country_code as country,
        AVG(funding_total) as year_2011
        FROM company
        WHERE EXTRACT(YEAR FROM CAST(founded_at as date)) = 2011
        GROUP BY country),
b as (SELECT country_code as country,
        AVG(funding_total) as year_2012
        FROM company
        WHERE EXTRACT(YEAR FROM CAST(founded_at as date)) = 2012
        GROUP BY country),    
c as (SELECT country_code as country,
        AVG(funding_total) as year_2013
        FROM company
        WHERE EXTRACT(YEAR FROM CAST(founded_at as date)) = 2013
        GROUP BY country)
        
SELECT a.country,
        a.year_2011,
        b.year_2012,
        c.year_2013
FROM a 
JOIN b ON a.country = b.country
JOIN c ON a.country = c.country
ORDER BY a.year_2011 DESC

































































