# Analytics

-- виводимо основну інформацію про аккаунти і підраховуємо їх
WITH account_info AS (
SELECT
       ses.date AS date,
       sp.country AS country,
       acc.id AS account_id,
       acc.send_interval AS send_interval,
       is_verified AS is_verified,
       is_unsubscribed AS is_unsubscribed,
       COUNT (DISTINCT acc.id) AS acc_cnt,
       0 AS sent_message,
       0 AS open_message,
       0 AS visit_message

FROM `data-analytics-mate.DA.account` acc
LEFT JOIN `data-analytics-mate.DA.account_session` acs
ON acc.id = acs.account_id
JOIN `data-analytics-mate.DA.session` ses
ON ses.ga_session_id = acs.ga_session_id
JOIN `data-analytics-mate.DA.session_params` sp
ON acs.ga_session_id = sp.ga_session_id
GROUP BY acc.id,
       ses.date,
       sp.country,
       acc.send_interval,
       is_verified,
       is_unsubscribed


),
email_info AS
-- виводимо основну інформацію про емейл і підраховуємо листи

(
SELECT
      DATE_ADD (ses.date, INTERVAL es.sent_date DAY) AS date,
     
      sp.country AS country,
      es.id_account AS account_id,
      acc.send_interval AS send_interval,
      is_verified AS is_verified,
      is_unsubscribed AS is_unsubscribed,
      0 AS acc_cnt,
     
      COUNT (DISTINCT es.id_message) AS sent_message,
      COUNT (DISTINCT eo.id_message) AS open_message,
      COUNT (DISTINCT ev.id_message) AS visit_message,




 FROM `DA.email_sent` es
 LEFT JOIN `DA.email_open` eo
 ON es.id_message = eo.id_message
 LEFT JOIN `DA.email_visit` ev
 ON es.id_message = ev.id_message
 JOIN `DA.account_session` acs
 ON acs.account_id = es.id_account
 JOIN `DA.session` ses
 ON ses.ga_session_id = acs.ga_session_id
 JOIN `DA.account` acc
 ON acc.id = acs.account_id
 JOIN `DA.session_params` sp
 ON sp.ga_session_id = acs.ga_session_id

 GROUP BY account_id, country,
       date,
       acc.send_interval,
       is_verified,
       is_unsubscribed
 ), union_data AS -- об'єднуємо дві таблиці
(
SELECT *
FROM account_info

UNION ALL

SELECT *
FROM email_info),
unionsum_info AS


-- виводимо метрики, які в подальшому підуть в основну таблицю
(SELECT date,
       country,
       send_interval,
       is_verified,
       is_unsubscribed,
       SUM (acc_cnt) AS account_cnt,
       SUM (sent_message) AS sent_msg,
       SUM (open_message) AS open_msg,
       SUM (visit_message) AS visit_msg

FROM union_data
GROUP BY 1,2,3,4,5),
totalcount AS (


-- підраховуємо аккаунти і листи в розрізі країн
SELECT *,
       SUM (account_cnt) OVER (PARTITION BY country) AS total_country_account_cnt,
       SUM (sent_msg) OVER (PARTITION BY country) AS total_country_sent_cnt

FROM unionsum_info
ORDER BY total_country_sent_cnt), ranking
AS (
-- проводимо ранжування
SELECT *,
       DENSE_RANK() OVER (ORDER BY total_country_account_cnt DESC) AS rank_total_country_account_cnt,
       DENSE_RANK() OVER (ORDER BY total_country_sent_cnt DESC) AS rank_total_country_sent_cnt

FROM totalcount)

-- виводимо фінальний результат
SELECT *
FROM ranking
WHERE rank_total_country_account_cnt <= 10 OR rank_total_country_sent_cnt <= 10
ORDER BY date

















