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
**Problema:** Suponiendo que existen tres tipos de dispositivos (laptop, tablet y phone), agrupa los dos últimos en un grupo llamado "mobile" y el número audienca que usan laptop frente dispositivos móbiles.

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

## 🔴 Nivel: Difícil 

