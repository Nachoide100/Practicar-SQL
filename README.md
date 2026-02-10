# 🛠️ SQL: Resolución de Problemas

Bienvenido a mi laboratorio de práctica de SQL. En este repositorio documento la resolución de diversos retos técnicos, desde consultas básicas de filtrado hasta operaciones complejas con funciones de ventana y optimización. Estan divididos en tres grupos según lo difíciles que me han resultado (totalmente subjetivo) y no estoy al 100% seguro de que el 100% de los ejercicios estén al 100% correcto asi que si alguien ve algún fallo, no dude en comunicarmelo!! Un saludo!

**Objetivo:** Practicar mucho todo tipo de consultas. 

### 🗺️ Navegación Rápida

[![](https://img.shields.io/badge/Nivel-Fácil-brightgreen?style=for-the-badge)](#-nivel-fácil)
[![](https://img.shields.io/badge/Nivel-Intermedio-yellow?style=for-the-badge)](#-nivel-intermedio)
[![](https://img.shields.io/badge/Nivel-Difícil-red?style=for-the-badge)](#-nivel-difícil)


---

## 🟢 Nivel: Fácil 
*Foco en: SELECT, WHERE, JOINs básicos y funciones de agregación (COUNT, SUM, AVG).*

### 📝 Reto 01
**Problema:** Obtener el ratio de clicks por app durante el año 2019.

**Estructura de las tablas:**

![Tabla de Clientes y Ventas](https://github.com/Nachoide100/Practicar-SQL/blob/4d2a72d5b3856cc6f5f3021aa161da1d79a2b6f2/tablas/Reto1.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
 SELECT app_id, 
			SUM (IF(event_id = 'click', 1, 0) / SUM(IF(event_id = 'impression', 1, 0)) as ctr
FROM events
WHERE 
	timestamp >= '2019-01-01'
	AND timestamp < '2020-01-01'
GROUP BY app_id
```
</details>

---

### 📝 Reto 02
**Problema:** Obtener la lista de las tres ciudades con mayor número de pedidos completados.

**Estructura de las tablas:**

![Tabla trades](https://github.com/Nachoide100/Practicar-SQL/blob/947a1f033a44da29479f4259dc0e404976aacdf5/tablas/Reto2.png)
![Tabla users](https://github.com/Nachoide100/Practicar-SQL/blob/d28b17b9bd42d3c1b0acadf2e3daf23c7078577d/tablas/Reto2_1.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT COUNT(DISTINCT t.order_id) as num_orders, u.city
FROM trades t
	JOIN users u ON t.user_id = u.user_id
WHERE t.status = 'complete'
GROUP BY city
ORDER BY num_orders DESC
LIMIT 3
```
</details>

### 📝 Reto 03
**Problema:** Suponiendo que existen tres tipos de dispositivos (laptop, tablet y phone), agrupa los dos últimos en un grupo llamado "mobile" y calcula el número de audiencia que usa laptop frente a dispositivos móbiles.

**Estructura de las tablas:**

![Tabla viewership](https://github.com/Nachoide100/Practicar-SQL/blob/2d9ad7e03adf60436b2a58b06777a7d3034e6e56/tablas/Reto3.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT SUM(IF(device_type = 'laptop', 1, 0) AS laptop_views, 
		SUM(IF(device_type IN ('phone', 'tablet'), 1, 0) as mobile_views
FROM viewership 
```
</details>

### 📝 Reto 04
**Problema:** Calcula el gasto acumulativo por fecha para cada producto en orden cronológico.

**Estructura de las tablas:**

![Tabla total_trans](https://github.com/Nachoide100/Practicar-SQL/blob/f7945c13060d854d38f12626e36e1193d72f3767/tablas/Reto4.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT trans_date,
			 product_id, 
			 SUM(spend) OVER (
				 PARTITION BY producto_id
				 ORDER BY 
					 trans_date
					) as cum_spend
FROM total_trans 
ORDER BY product_id, 
					trans_date ASC 
```
</details>

### 📝 Reto 05
**Problema:** Realiza una consulta para conocer los 10 clientes con un mayor número de productos ordenados dentro de los clientes que hayan gastado al menos 1000$. 

**Estructura de las tablas:**

![Tabla use_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/f7945c13060d854d38f12626e36e1193d72f3767/tablas/Reto5.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT user_id, 
			COUNT(product_id) as num_products
FROM user_transactions
GROUP BY user_id
HAVING SUM(spend) >= 1000
ORDER BY num_products DESC
LIMIT 10
```
</details>

### 📝 Reto 06
**Problema:** Realiza una consulta para obtener un histograma de los tweets publicados por usuario en 2020. 

**Estructura de las tablas:**

![Tabla tweets](https://github.com/Nachoide100/Practicar-SQL/blob/9481a488d3ae6b836b134a816f9efb8138528ff9/tablas/Reto6.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT num_tweets, 
		COUNT(*) as num_users
FROM 
	(
		SELECT user_id, 
			COUNT(*) as num_tweets
		FROM tweets
		WHERE tweet_date BETWEEN '2020-01-01' AND '2020-12-31'
		GROUP BY user_id
	) total_tweets
GROUP BY num_tweets
ORDER BY num_tweets ASC
```
</details>

### 📝 Reto 07
**Problema:** Realiza una consulta para obtener el número de personas que han comprado una o más unidades del mismo producto en múltiples días.  

**Estructura de las tablas:**

![Tabla purchases](https://github.com/Nachoide100/Practicar-SQL/blob/9481a488d3ae6b836b134a816f9efb8138528ff9/tablas/Reto7.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT COUNT(DISTINCT user_id) 
FROM 
	(
		SELECT 
			user_id, 
			RANK () OVER (
				PARTITION BY user_id, 
				product_id 
				ORDER BY 
					CAST(purchase_time as date)
			) as purchase_no
		FROM purchases
	)
WHERE 
purchase_no = 2
```
</details>

### 📝 Reto 08
**Problema:** Para ser un Data Scientist se precisa de Python, Tableau y PostgreSQL. Haz una consulta que devuelva qué candidatos tienen las skills necesarias para ser un Data Scientist.  

**Estructura de las tablas:**

candidates

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/83007012fbbe5fda1754cfce3d457530c54e8628/tablas/Reto8.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT candidate_id
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(skill) = 3
```
</details>

### 📝 Reto 09
**Problema:** Realiza una consulta para saber el ID de las páginas que no tienen ningún like. Muestra el resultado en orden ascendente según el page_id. 

**Estructura de las tablas:**

pages

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/83007012fbbe5fda1754cfce3d457530c54e8628/tablas/Reto9.png)

page_likes

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/83007012fbbe5fda1754cfce3d457530c54e8628/tablas/Reto9_1.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT p.page_id
FROM pages p
LEFT JOIN page_likes l ON p.page_id = l.page_id
WHERE liked_date IS NULL
ORDER BY p.page_id
```
</details>

### 📝 Reto 10
**Problema:** Viendo la tabla de posts, ejecuta una consulta que devuelva, para los usuarios que han hecho al menos dos posts en 2021, la cantidad de días que han pasado entre los posts.

**Estructura de las tablas:**

posts

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/9920f884dd366522bf899bece5e012a7f465e324/tablas/Reto10.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT user_id, MAX(post_date::DATE) - MIN(post_date::DATE) AS days_between
FROM posts
WHERE DATE_PART('year', post_date::DATE) = 2021 
GROUP BY user_id
HAVING COUNT(post_id) > 1
```
</details>

### 📝 Reto 11
**Problema:** Escribe una consulta para identificar a los dos usuarios que más mensajes hayan enviado en Agosto de 2022. Muestra el su ID ordenados según la cantidad de mensajes que enviaron. 

**Estructura de las tablas:**

messages

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/9920f884dd366522bf899bece5e012a7f465e324/tablas/Reto11.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT 
  sender_id,
  COUNT(message_id) AS count_messages
FROM messages
WHERE EXTRACT(MONTH FROM sent_date) = '8'
  AND EXTRACT(YEAR FROM sent_date) = '2022'
GROUP BY sender_id
ORDER BY count_messages DESC
LIMIT 2;
```
</details>

### 📝 Reto 12
**Problema:**  Imagina que tiene la siguiente tabla con la suscripción de usuarios a Tik Tok y su confirmación a través de email. Escribe una consulta para saber los IDs de los usuarios que no confirmaron sus suscripción al primer día, pero si al segundo.
**Estructura de las tablas:**

emails

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/2e23f5ae754ccb80d185753c43e1f9041ec93488/tablas/Reto12.png)

texts

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/2e23f5ae754ccb80d185753c43e1f9041ec93488/tablas/Reto12_1.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT user_id
FROM texts t
INNER JOIN emails e
ON t.email_id = e.email_id
WHERE action_date = signup_date + INTERVAL '1 day'
```
</details>

### 📝 Reto 13
**Problema:** Identifica cuantos UHG Policy Holders hicieron 3 o más llamadas, asumiendo que cada llama está identificada por la columna case_id.

**Estructura de las tablas:**

callers

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/2e23f5ae754ccb80d185753c43e1f9041ec93488/tablas/Reto13.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT COUNT(policy_holder_id) as policy_holder_count
FROM (
      SELECT policy_holder_id
      FROM callers
      GROUP BY policy_holder_id
      HAVING COUNT(case_id) >= 3
      ) as holders
```
</details>

### 📝 Reto 14
**Problema:** Determina el segundo salario más alto de entre todos los empleados.

**Estructura de las tablas:**

employee

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/cb89b1292d3dc0fd8636ca5b62a6ac5024a62954/tablas/Reto14.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT MAX(salary) as second_highest_salary
FROM employee
WHERE salary < 
      (SELECT MAX(salary)
      FROM employee)
```
</details>

### 📝 Reto 15
**Problema:** Desarrolla una consulta para calcular la venta total de drogas por cada manufacter. Redondea la respuesta al millón más cercano y muestra los resultados en orden descendente según el total de ventas. (Para una mayor comprensión formatea el resultado de ventas a: $36 million)

**Estructura de las tablas:**

pharmacy_sales

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/7923c6f317032baa7728f458f3dc8c2e4c985339/tablas/Reto15.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT manufacturer, 
        CONCAT('$', ROUND(SUM(total_sales) / 1000000), ' million') as sales
FROM pharmacy_sales
GROUP BY manufacturer
ORDER BY SUM(total_sales) DESC
```
</details>


### 📝 Reto 16
**Problema:** Escribe una consulta que muestre el nombre de cada tarjeta de crédito y la diferencia en el número de tarjetas dañadas entre el mes con mayor número y el que menos. Muestra los resultados en orden de mayor diferencia a menor. 

**Estructura de las tablas:**

monthly_cards_issued

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/e219608c5bae30223ad77f325c357adb07d72496/tablas/Reto16.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT card_name, 
       MAX(issued_amount) - MIN(issued_amount) as difference
FROM monthly_cards_issued
GROUP BY card_name
ORDER BY difference DESC
```
</details>


### 📝 Reto 17
**Problema:** Calcula la media de items_per_oder redondeando a un decimal. 

**Estructura de las tablas:**

items_per_order

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/e219608c5bae30223ad77f325c357adb07d72496/tablas/Reto17.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT 
  ROUND(
    SUM(CAST(item_count as DECIMAL) *order_occurrences)
    /SUM(order_occurrences)
  ,1) AS mean
FROM items_per_order;
```
</details>

### 📝 Reto 18
**Problema:** Añade una columna a la siguiente tabla que clasifique los ids según el precio en estas tres categorías:
* Menos de 50 → Económico
* Entre 50 y 150 → Estandar
* Mas de 150 → Premium

Muestra id, precio y categoria ordenado por número de id. 

**Estructura de las tablas:**

list

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/6fd8b313f66d54396e0bc6735ebe33a41b69ca33/tablas/Reto18-19.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT id, price,
	CASE 
	WHEN price < 50 THEN 'Economico'
	WHEN price BETWEEN 50 AND 150 THEN 'Estandar'
	WHEN price > 150 THEN ' Premium'
	END as categoría
FROM list
ORDER BY id
```
</details>

### 📝 Reto 19
**Problema:** Muestra todos los ids con un precio mayor al promedio de toda la tabla. Muestra el resultado en orden DESC según el precio. 

**Estructura de las tablas:**

list

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/6fd8b313f66d54396e0bc6735ebe33a41b69ca33/tablas/Reto18-19.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT id, price
FROM list 
WHERE price > (
		SELECT AVG(price)
		FROM list)
ORDER BY price DESC
```
</details>

### 📝 Reto 20
**Problema:** Obtén los 3 alojamientos con mayor número de reseñas para cada barrio

**Estructura de las tablas:**

alojamientos

![Tabla](https://github.com/Nachoide100/Practicar-SQL/blob/6fd8b313f66d54396e0bc6735ebe33a41b69ca33/tablas/Reto20.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH ranking as(
	SELECT id, neighbourhood_cleansed, number_of_reviews,
	ROW_NUMBER () OVER (PARTITION BY neighbourhood_cleansed ORDER BY number_of_reviews DESC) as ranking
	FROM alojamientos)

SELECT id, neighbourhood_cleansed, number_of_reviews
FROM ranking
WHERE ranking <= 3
```
</details>

## 🟡 Nivel: Intermedio
*Foco en: Funciones ventana, CTEs, JOINS complejos y consultas temporales*

### 📝 Reto 01
**Problema:** Construye una consulta para obtener el número total de compañías que han publicado listas de trabajo duplicadas (dos trabajos en la misma compañía con el mismo titulo y descripción).

**Estructura de las tablas:**

![Tabla job_listings](https://github.com/Nachoide100/Practicar-SQL/blob/7986e6bc1dc8065e2f616dd0899c10bce0e2ba86/tablas/RetoI1.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH job_listings_ranks as (
	SELECT company_id, 
					title, 
					description, 
					ROW_NUMBER () OVER (
						PARTITION BY company_id, 
						title, 
						description, 
						ORDER BY post_date
						) as rank
					FROM job_listings
)

SELECT COUNT(DISTINCT company_id)
FROM
	(
		SELECT company_id
		FROM job_listings_ranks
		WHERE MAX(rank) > 1
	)
```
</details>

### 📝 Reto 02
**Problema:** Realiza una consulta para obtener la lista de clientes cuya primera transaccion valía 50$ o más. 

**Estructura de las tablas:**

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/7986e6bc1dc8065e2f616dd0899c10bce0e2ba86/tablas/RetoI2.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH purchase_num as (
	SELECT user_id
					spend, 
					ROW_NUMBER () OVER (
						PARTITION BY user_id
						ORDER BY transaction_date ASC
					) as rownum
				FROM user_transactions
)
SELECT user_id
FROM purchase_num
WHERE rownum = 1 AND spend >= 50.00
					
```
</details>

### 📝 Reto 03
**Problema:** Obtén la tercera transacción de cada usuario. Muestra el user_id, spend y transaction_date. 

**Estructura de las tablas:**

transactions

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/edefc9c345a9b08db09c7f3f9ea3fa59da2a426e/tablas/Reto3I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH date_listing as(
      SELECT user_id, spend, transaction_date,
        ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY transaction_date) as date_ranking
        FROM transactions)
        
SELECT user_id, spend, transaction_date
FROM date_listing
WHERE date_ranking = 3
					
```
</details>

### 📝 Reto 04
**Problema:** Desarrolla una consulta para obtener una visión del tiempo gastado en enviar (activity_type = ‘send’) respecto al tiempo gastado en abrir (activity_type = ‘open’) en formato de porcentaje respecto al total, agrupado según el grupo de edad.

**Estructura de las tablas:**

activities

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/cb89b1292d3dc0fd8636ca5b62a6ac5024a62954/tablas/Reto4I.png)

age

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/edefc9c345a9b08db09c7f3f9ea3fa59da2a426e/tablas/Reto4I_2.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH snaps_statistics AS (
  SELECT 
    age.age_bucket, 
    SUM(CASE WHEN activities.activity_type = 'send' 
      THEN activities.time_spent ELSE 0 END) AS send_timespent, 
    SUM(CASE WHEN activities.activity_type = 'open' 
      THEN activities.time_spent ELSE 0 END) AS open_timespent, 
    SUM(activities.time_spent) AS total_timespent 
  FROM activities
  INNER JOIN age_breakdown AS age 
    ON activities.user_id = age.user_id 
  WHERE activities.activity_type IN ('send', 'open') 
  GROUP BY age.age_bucket
) 

SELECT 
  age_bucket, 
  ROUND(100.0 * send_timespent / total_timespent, 2) AS send_perc, 
  ROUND(100.0 * open_timespent / total_timespent, 2) AS open_perc 
FROM snaps_statistics;
					
```
</details>

### 📝 Reto 05
**Problema:** Crea una consulta para generar un histograma que muestre el número de consultas únicas llevadas a cabo por los empleados durante el tercer trimestre de 2023 (Julio - Septiembre). También tienen que aparcer el número de empleados que no realizaron ninguna consulta durante ese período. 

**Estructura de las tablas:**

employees_queries

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/7923c6f317032baa7728f458f3dc8c2e4c985339/tablas/Reto5I.png)

employees

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/7923c6f317032baa7728f458f3dc8c2e4c985339/tablas/Reto5I_1.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH employee_queries AS (
  SELECT 
    e.employee_id,
    COALESCE(COUNT(DISTINCT q.query_id), 0) AS unique_queries
  FROM employees AS e
  LEFT JOIN queries AS q
    ON e.employee_id = q.employee_id
      AND q.query_starttime >= '2023-07-01T00:00:00Z'
      AND q.query_starttime < '2023-10-01T00:00:00Z'
  GROUP BY e.employee_id
)

SELECT
  unique_queries,
  COUNT(employee_id) AS employee_count
FROM employee_queries
GROUP BY unique_queries
ORDER BY unique_queries;
					
```
</details>

### 📝 Reto 06
**Problema:** Calcula la media móvil de 3 días para los tweets de cada usuario. Muestra el user_id, tweet_date y la media móvil redondeada a dos decimales.  

**Estructura de las tablas:**

tweets

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/a8a09324f88d50258a688a1af54ec91f047ce395/tablas/Reto6I.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT user_id, tweet_date,
        ROUND(AVG(tweet_count) OVER (PARTITION BY user_id ORDER BY tweet_date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW), 2) as rolling_avg_3
FROM tweets
					
```
</details>

### 📝 Reto 07
**Problema:** Desarrolla una consulta para identificar los dos productos con más ventas dentro de cada categoría en el año 2022. El resultado debe incluir categoría, producto y total de ventas.  

**Estructura de las tablas:**

product_spend

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/a8a09324f88d50258a688a1af54ec91f047ce395/tablas/Reto7I.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH ranking_spends as
( 
  SELECT category, product, SUM(spend) as total_spend, 
          RANK() OVER (PARTITION BY category ORDER BY SUM(spend) DESC) as ranking
  FROM product_spend
  WHERE EXTRACT(YEAR FROM transaction_date) = 2022
  GROUP BY category, product)
  
SELECT category, product, total_spend
FROM ranking_spends
WHERE ranking <= 2
ORDER BY category, ranking
					
```
</details>

### 📝 Reto 08
**Problema:** Identifica los 3 empleados mejor pagados de cada departamento. Muestra nombre de departamento, nombre de empleado y usuario. En caso de duplicados, ordena el nombre del departamento de forma ascendente, el salario de forma descendente y si hay varios empleados con el mismo salarios ordénalos alfabéticamente. 

**Estructura de las tablas:**

employee

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/a4f27c4aca7dc7a043db53aed39c32439bad9765/tablas/Reto8I.png)

department

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/a4f27c4aca7dc7a043db53aed39c32439bad9765/tablas/Reto8I_1.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH ranked_salary AS (
  SELECT 
    name,
    salary,
    department_id,
    DENSE_RANK() OVER (
      PARTITION BY department_id ORDER BY salary DESC) AS ranking
  FROM employee
)

SELECT 
  d.department_name,
  s.name,
  s.salary
FROM ranked_salary AS s
INNER JOIN department AS d
  ON s.department_id = d.department_id
WHERE s.ranking <= 3
ORDER BY d.department_name ASC, s.salary DESC, s.name ASC
					
```
</details>

### 📝 Reto 09
**Problema:** Diseña una consulta para obtener el ratio de acitvación (porcentaje de cuentas activadas) de los usuarios de la tabla emails. Redondea el resultado a dos decimales. 

**Estructura de las tablas:**

emails

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/a4f27c4aca7dc7a043db53aed39c32439bad9765/tablas/Reto9I.png)

texts

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/a4f27c4aca7dc7a043db53aed39c32439bad9765/tablas/Reto9I_1.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT 
  ROUND(CAST(COUNT(texts.email_id) as DECIMAL)
    /COUNT(DISTINCT emails.email_id), 2) AS activation_rate
FROM emails
LEFT JOIN texts
  ON emails.email_id = texts.email_id
  AND texts.signup_action = 'Confirmed';
					
```
</details>

### 📝 Reto 10
**Problema:** Agrupando a los usuarios según su último día de transacción, diseña una consulta para obtener el número de usuarios que hicieron una compra y el número total de productos comprados en cada día de transacción. 

**Estructura de las tablas:**

user_transactions

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/c5396c667ab3e3b6165331cba6fc90cb65d451b6/tablas/Reto10I.png)




<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH latest_date as
(
	SELECT transaction_date, user_id, product_id
				RANK () OVER (PARTITION BY user_id ORDER BY CAST(transaction_date as DATE) DESC
	) as days_rank
	FROM user_transactions
)

SELECT transaction_date, 
				COUNT(DISTINCT user_id) as num_users, 
				SUM(product_id) as total_products
FROM latest_date
WHERE days_rank = 1
GROUP BY transaction_date
ORDER BY transaction_date DESC
					
```
</details>

### 📝 Reto 11
**Problema:** Para cada barrio, identifica cual es la mayor diferencia de precio entre dos alojamientos consecutivos (ordenados de menor a mayor precio). Muestra el resultado por barrio y ordenado según esa diferencia. 

**Estructura de las tablas:**

barrios

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/e3d27fdaf6a986e7033a051a59cb4fb4b1d5be98/tablas/Reto11I.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH diferencias as (
	SELECT neighbourhood_cleansed, price, 
		price - LAG(price) OVER (PARTITION BY neighbourhood_cleansed ORDER BY price) AS diferencia
	FROM barrios)

SELECT neighbourhood_cleansed, MAX(diferencia) as diferencia_maxima
FROM diferencias
GROUP BY neighbourhood_cleansed
ORDER BY diferencia_maxima DESC
					
```
</details>

### 📝 Reto 12
**Problema:** Identifica las “trampas para turistas”. Haz una consulta para buscar los alojamientos que se encuentra entre el 10 % más caro de su barrio pero tengan una puntuación mediocre (< de 4 estrellas). Muestra el resultado ordenado por precio de mayor a menor. 
**Estructura de las tablas:**

alojamientos

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/b1ac40d7a762aec568d2dc60aec4b73754a794d9/tablas/Reto12I.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH trampas as(
	SELECT id, price, neighbourhood_cleansed, review_scores_rating, 
		PERCENT_RANK() OVER (PARTITION BY neighbourhood_cleansed ORDER BY price DESC) as percent_rank
	FROM alojamientos
)

SELECT id, neighbourhood_cleansed, review_scores_rating
FROM trampas
WHERE percent_rank > 0.9
		AND review_scores_rating < 4
					
```
</details>

### 📝 Reto 13
**Problema:** Calcula la diferencia de precio promedio entre los alojamientos que tienen “Air conditioning” en su lista de amenities y los que no, agrupados por tipo de alojamiento. Muestra el resultado ordenado de mayor a menor diferencia.  
**Estructura de las tablas:**

alojamientos

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/b1ac40d7a762aec568d2dc60aec4b73754a794d9/tablas/Reto13I.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT
    room_type,
    AVG(CASE WHEN amenities LIKE '%Air conditioning%' THEN price END) as precio_con_aire,
    AVG(CASE WHEN amenities NOT LIKE '%Air conditioning%' THEN price END) as precio_sin_aire,
    AVG(CASE WHEN amenities LIKE '%Air conditioning%' THEN price END) -
    AVG(CASE WHEN amenities NOT LIKE '%Air conditioning%' THEN price END) as diferencia
FROM alojamientos
GROUP BY room_type
ORDER BY diferencia DESC
					
```
</details>

### 📝 Reto 14
**Problema:** Muestra id, barrio, precio y una nueva columna que calcule el precio promedio del piso actual y los 3 anteriores (según el orden de id) dentro de cada barrio y redondeado a dos decimales. 
**Estructura de las tablas:**

alojamientos

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/b1ac40d7a762aec568d2dc60aec4b73754a794d9/tablas/Reto14I.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT id, neighbourhood_cleansed, price, 
			ROUND(AVG(price) OVER (PARTITION BY neighbourhood_cleansed ORDER BY id ROWS BETWEEN 3 PRECEDING AND CURRENT ROW), 2) as rolling_avg_3
FROM alojamientos
					
```
</details>

## 🔴 Nivel: Difícil 
*Foco en: Funciones ventana, CTEs y JOINS complejos, UNIONS*

### 📝 Reto 01
**Problema:**  Observando las siguientes tablas y asumiendo que:

* songs_history recoge datos hasta el 31-07-2022
* songs_weekly recoge datos de la semana actual (semana del 1 de agosto).

Desarrolla una consulta que muestre el user_id, song_id y la cantidad de song_plays hasta el 04-08-2022. Debemos asumir que hay usuarios y canciones presentes en songs_weekly que no están en songs_history.

**Estructura de las tablas:**

songs_history

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/9ecd33eb7a4037f2475ad2a77c344c2093243aa8/tablas/Reto1D.png)

songs_weekly

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/9ecd33eb7a4037f2475ad2a77c344c2093243aa8/tablas/Reto1D_1.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH history AS 
( 
  SELECT user_id, song_id, song_plays
  FROM songs_history

UNION ALL

  SELECT user_id, song_id, COUNT(song_id) AS song_plays
  FROM songs_weekly
  WHERE listen_time <= '2022-08-05'
  GROUP BY user_id, song_id
)

SELECT user_id, song_id, SUM(song_plays) AS song_count
FROM history
GROUP BY user_id, song_id
ORDER BY song_count DESC
```
</details>

### 📝 Reto 02
**Problema:**  Suponiendo que un super cliente se define como aquel que ha comprado al menos un producto de cada categoría de la tabla productos, escribe una consulta para identificar los IDs de esos superclientes. 

**Estructura de las tablas:**

songs_history

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/9ecd33eb7a4037f2475ad2a77c344c2093243aa8/tablas/Reto2D.png)

songs_weekly

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/9ecd33eb7a4037f2475ad2a77c344c2093243aa8/tablas/Reto2D_1.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH super_customer AS
( 
  SELECT c.customer_id, 
        COUNT(DISTINCT p.product_category) as product_count
  FROM customer_contracts c
  INNER JOIN products p ON c.product_id = p.product_id
  GROUP BY c.customer_id
  )
  
SELECT customer_id
FROM super_customer
WHERE product_count = (
          SELECT COUNT(DISTINCT product_category) FROM products
          )
```
</details>

### 📝 Reto 03
**Problema:**  Queremos comparar rápidamente cuanto cuesta una habitación privada vs. una apartamento entero en cada barrio, pero queremos una fila por barrio con los precios en columnas separadas. Consulta para para generar una tabla con las columnas barrio, precio_privada, precio_entera y diferencia. 

**Estructura de las tablas:**

barrios

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/b1ac40d7a762aec568d2dc60aec4b73754a794d9/tablas/Reto3D.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT
    neighbourhood_cleansed,
    AVG(CASE WHEN room_type = 'Private room' THEN price END) AS precio_privada,
    AVG(CASE WHEN room_type = 'Entire home/apt' THEN price END) AS precio_entero,
    AVG(CASE WHEN room_type = 'Entire home/apt' THEN price END) - AVG(CASE WHEN room_type = 'Private room' THEN price END) as diferencia
FROM barrios
GROUP BY neighbourhood_cleansed
HAVING AVG(CASE WHEN room_type = 'Private room' THEN price END) IS NOT NULL AND AVG(CASE WHEN room_type = 'Entire home/apt' THEN price END) IS NOT NULL
```
</details>
