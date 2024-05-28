1.Найдите количество вопросов, которые набрали больше 300 очков или как минимум 100 раз были добавлены в «Закладки».
    SELECT COUNT(posts.id)
    FROM stackoverflow.posts
    JOIN stackoverflow.post_types  as pt on pt.id = post_type_id
    WHERE post_type_id = 1 AND (score > 300 OR favorites_count >= 100)

2.Сколько в среднем в день задавали вопросов с 1 по 18 ноября 2008 включительно? Результат округлите до целого числа.
    WITH e as (SELECT  CAST(DATE_TRUNC('day',creation_date) as date),
            COUNT(*) as avg
    FROM stackoverflow.posts
    JOIN stackoverflow.post_types  as pt on pt.id = post_type_id
    WHERE post_type_id = 1 AND CAST(DATE_TRUNC('day',creation_date) as date) BETWEEN '2008-11-01' AND '2008-11-18'
    GROUP BY CAST(DATE_TRUNC('day',creation_date) as date))
    SELECT ROUND(AVG(avg),0)
    FROM e

3.Сколько пользователей получили значки сразу в день регистрации? Выведите количество уникальных пользователей.
    SELECT COUNT(DISTINCT b.user_id)       
    FROM stackoverflow.badges as b
    JOIN stackoverflow.users as u  ON u.id = b.user_id
    WHERE (CAST(DATE_TRUNC('day',u.creation_date) as date))  = (CAST(DATE_TRUNC('day',b.creation_date) as date)) 

4.Сколько уникальных постов пользователя с именем Joel Coehoorn получили хотя бы один голос?
    SELECT COUNT(DISTINCT sp.id)
    FROM stackoverflow.users as su
    JOIN stackoverflow.posts as sp ON sp.user_id = su.id
    JOIN stackoverflow.votes as vt ON vt.post_id = sp.id
    WHERE display_name = 'Joel Coehoorn'

5.Выгрузите все поля таблицы vote_types. Добавьте к таблице поле rank, в которое войдут номера записей в обратном порядке. 
Таблица должна быть отсортирована по полю id.
    SELECT *,
        ROW_NUMBER() OVER(ORDER BY id DESC) AS rank
    FROM stackoverflow.vote_types
    ORDER BY id

6.Отберите 10 пользователей, которые поставили больше всего голосов типа Close. 
Отобразите таблицу из двух полей: идентификатором пользователя и количеством голосов. 
Отсортируйте данные сначала по убыванию количества голосов, потом по убыванию значения идентификатора пользователя.
    SELECT su.id,
           COUNT(svt.name)
    FROM stackoverflow.users as su
    JOIN stackoverflow.votes as sv ON su.id=sv.user_id
    JOIN stackoverflow.vote_types as svt ON sv.vote_type_id = svt.id
    WHERE svt.name = 'Close'
    GROUP BY su.id
    ORDER BY COUNT(svt.name) DESC, su.id DESC
    LIMIT 10

7.Отберите 10 пользователей по количеству значков, полученных в период с 15 ноября по 15 декабря 2008 года включительно.
Отобразите несколько полей:
идентификатор пользователя;
число значков;
место в рейтинге — чем больше значков, тем выше рейтинг.
Пользователям, которые набрали одинаковое количество значков, присвойте одно и то же место в рейтинге.
Отсортируйте записи по количеству значков по убыванию, а затем по возрастанию значения идентификатора пользователя.
    WITH e as (SELECT  user_id,
                COUNT(id) as col_vo
    FROM stackoverflow.badges
    WHERE CAST(DATE_TRUNC('day',creation_date)as date) BETWEEN '2008-11-15' AND '2008-12-15'
    GROUP BY  user_id
    ORDER BY col_vo DESC
    LIMIT 10)
    SELECT *,
            DENSE_RANK() OVER (ORDER BY col_vo DESC)
    FROM e

8.Сколько в среднем очков получает пост каждого пользователя?
Сформируйте таблицу из следующих полей:
заголовок поста;
идентификатор пользователя;
число очков поста;
среднее число очков пользователя за пост, округлённое до целого числа.
Не учитывайте посты без заголовка, а также те, что набрали ноль очков.
    SELECT title,
           user_id,
           score,
           ROUND(AVG(score) OVER(PARTITION BY user_id),0) as avg_score
    FROM stackoverflow.posts
    WHERE title IS NOT NULL AND score != 0

9.Отобразите заголовки постов, которые были написаны пользователями, получившими более 1000 значков. 
Посты без заголовков не должны попасть в список.
    SELECT title
    FROM stackoverflow.posts
    WHERE user_id IN (SELECT user_id 
                        FROM stackoverflow.users as su
                        JOIN stackoverflow.badges as sb ON su.id = sb.user_id
                        GROUP BY user_id
                        HAVING COUNT(name) > 1000)
                 AND title IS NOT NULL       

10.Напишите запрос, который выгрузит данные о пользователях из Канады (англ. Canada). 
Разделите пользователей на три группы в зависимости от количества просмотров их профилей:
пользователям с числом просмотров больше либо равным 350 присвойте группу 1;
пользователям с числом просмотров меньше 350, но больше либо равно 100 — группу 2;
пользователям с числом просмотров меньше 100 — группу 3.
Отобразите в итоговой таблице идентификатор пользователя, количество просмотров профиля и группу. 
Пользователи с количеством просмотров меньше либо равным нулю не должны войти в итоговую таблицу.
    SELECT id,
           views,
           CASE
               WHEN views >= 350 THEN 1
               WHEN views < 350 AND views >= 100 THEN 2
               WHEN views < 100 THEN 3
           END    
    FROM stackoverflow.users
    WHERE location LIKE '%Canada%' AND views > 0

11.Дополните предыдущий запрос. Отобразите лидеров каждой группы — пользователей, 
которые набрали максимальное число просмотров в своей группе.
Выведите поля с идентификатором пользователя, группой и количеством просмотров. 
Отсортируйте таблицу по убыванию просмотров, а затем по возрастанию значения идентификатора.
    WITH e as (SELECT id,
                       grouped,
                       views,
                       MAX(views) OVER (PARTITION BY grouped)
               FROM (SELECT id,
                           views,
                           CASE
                               WHEN views >= 350 THEN 1
                               WHEN views < 350 AND views >= 100 THEN 2
                               WHEN views < 100 THEN 3
                           END as grouped   
                     FROM stackoverflow.users
                     WHERE location LIKE '%Canada%' AND views > 0) as f)
    SELECT e.id,
           e.views,
           e.grouped 
    FROM e
    WHERE e.max = e.views
    ORDER BY e.views DESC, e.id

12.Посчитайте ежедневный прирост новых пользователей в ноябре 2008 года. Сформируйте таблицу с полями:
номер дня;
число пользователей, зарегистрированных в этот день;
сумму пользователей с накоплением.
    WITH e as(SELECT EXTRACT(DAY FROM CAST(creation_date as date)) as days ,
           COUNT(id) as col_vo
    FROM stackoverflow.users
    WHERE CAST(DATE_TRUNC('month', creation_date)as date) = '2008-11-01'
    GROUP BY days )
    SELECT *,
            SUM(col_vo) OVER (ORDER BY days,col_vo)
    FROM e

13.Для каждого пользователя, который написал хотя бы один пост, 
найдите интервал между регистрацией и временем создания первого поста. Отобразите:
идентификатор пользователя;
разницу во времени между регистрацией и первым постом.
WITH e as (SELECT DISTINCT user_id,
       MIN(creation_date) OVER (PARTITION BY user_id) 
FROM stackoverflow.posts )
SELECT e.user_id,
       e.min - su.creation_date
FROM stackoverflow.users as su
JOIN e ON e.user_id = su.id

14.Выведите общую сумму просмотров у постов, опубликованных в каждый месяц 2008 года. 
Если данных за какой-либо месяц в базе нет, такой месяц можно пропустить. 
Результат отсортируйте по убыванию общего количества просмотров.
    SELECT CAST(DATE_TRUNC('month', creation_date ) as date) as monthy,
           SUM(views_count)
    FROM stackoverflow.posts
    GROUP BY monthy
    ORDER BY SUM(views_count) DESC

15.Выведите имена самых активных пользователей, которые в первый месяц после регистрации (включая день регистрации) дали больше 100 ответов.
Вопросы, которые задавали пользователи, не учитывайте. Для каждого имени пользователя выведите количество уникальных значений user_id. 
Отсортируйте результат по полю с именами в лексикографическом порядке.
    SELECT u.display_name,
           COUNT(DISTINCT p.user_id)
    FROM stackoverflow.posts as p
    JOIN stackoverflow.users as u ON p.user_id=u.id
    JOIN stackoverflow.post_types as pt ON pt.id=p.post_type_id
    WHERE p.creation_date::date BETWEEN u.creation_date:: date AND (u.creation_date::date + INTERVAL '1 month')
                                                                    AND pt.type LIKE '%Answer%'
    GROUP BY u.display_name
    HAVING COUNT(*) > 100
    ORDER BY u.display_name

16.Выведите количество постов за 2008 год по месяцам. Отберите посты от пользователей, 
которые зарегистрировались в сентябре 2008 года и сделали хотя бы один пост в декабре того же года. 
Отсортируйте таблицу по значению месяца по убыванию.
    WITH u as (SELECT u.id
    FROM stackoverflow.users as u
    JOIN stackoverflow.posts as p ON u.id=p.user_id
    WHERE DATE_TRUNC('month',u.creation_date)::date = '2008-09-01' 
            AND DATE_TRUNC('month',p.creation_date)::date = '2008-12-01'
    GROUP BY u.id)    
    SELECT COUNT(p.id),
            DATE_TRUNC('month',p.creation_date)::date
    FROM stackoverflow.posts as p
    WHERE p.user_id IN (SELECT *
                       FROM u)
                       AND EXTRACT(YEAR FROM p.creation_date) = 2008
    GROUP BY  DATE_TRUNC('month',p.creation_date)::date
    ORDER BY DATE_TRUNC('month',p.creation_date)::date  DESC

17.Используя данные о постах, выведите несколько полей:
идентификатор пользователя, который написал пост;
дата создания поста;
количество просмотров у текущего поста;
сумма просмотров постов автора с накоплением.
Данные в таблице должны быть отсортированы по возрастанию идентификаторов пользователей, 
а данные об одном и том же пользователе — по возрастанию даты создания поста.
    SELECT  user_id,
            creation_date,
            views_count,
            SUM(views_count) OVER (PARTITION BY user_id ORDER BY creation_date)
    FROM stackoverflow.posts

18.Сколько в среднем дней в период с 1 по 7 декабря 2008 года включительно пользователи взаимодействовали с платформой? 
Для каждого пользователя отберите дни, в которые он или она опубликовали хотя бы один пост. 
Нужно получить одно целое число — не забудьте округлить результат.
    WITH us as (SELECT user_id,
           COUNT(DISTINCT DATE_TRUNC('day', creation_date)::date) as dt
    FROM stackoverflow.posts    
    WHERE DATE_TRUNC('day', creation_date)::date BETWEEN '2008-12-01' AND '2008-12-07'
    GROUP BY user_id)
    SELECT ROUND(AVG(dt),0)
    FROM us

19.На сколько процентов менялось количество постов ежемесячно с 1 сентября по 31 декабря 2008 года? 
Отобразите таблицу со следующими полями:
Номер месяца.
Количество постов за месяц.
Процент, который показывает, насколько изменилось количество постов в текущем месяце по сравнению с предыдущим.
Если постов стало меньше, значение процента должно быть отрицательным, если больше — положительным. 
Округлите значение процента до двух знаков после запятой.
Напомним, что при делении одного целого числа на другое в PostgreSQL в результате получится целое число, 
округлённое до ближайшего целого вниз. Чтобы этого избежать, переведите делимое в тип numeric.
    WITH a as(SELECT EXTRACT(MONTH FROM creation_date),
                       COUNT(id)
             FROM stackoverflow.posts
             WHERE DATE_TRUNC('day', creation_date)::date BETWEEN '2008-09-01' AND '2008-12-31'
             GROUP BY EXTRACT(MONTH FROM creation_date)
             ORDER BY EXTRACT(MONTH FROM creation_date))
    SELECT *,
            ROUND((count::numeric / (LAG(count) OVER (ORDER BY extract)::numeric)-1)*100,2)
    FROM a

20.Найдите пользователя, который опубликовал больше всего постов за всё время с момента регистрации. 
Выведите данные его активности за октябрь 2008 года в таком виде:
номер недели;
дата и время последнего поста, опубликованного на этой неделе.
    WITH e as(SELECT p.user_id
    FROM stackoverflow.posts p
    JOIN stackoverflow.users u ON p.user_id=u.id
    WHERE p.creation_date >= u.creation_date
    GROUP BY p.user_id
    ORDER BY COUNT(*) DESC
    LIMIT 1)
    SELECT EXTRACT(WEEK FROM creation_date),
           MAX(creation_date)
    FROM stackoverflow.posts p
    JOIN e ON p.user_id = e.user_id
    WHERE CAST(DATE_TRUNC('month',creation_date) as date) = '2008-10-01'
    GROUP BY EXTRACT(WEEK FROM creation_date)
















