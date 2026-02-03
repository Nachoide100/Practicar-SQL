# 🛠️ SQL: Resolución de Problemas

Bienvenido a mi laboratorio de práctica de SQL. En este repositorio documento la resolución de diversos retos técnicos, desde consultas básicas de filtrado hasta operaciones complejas con funciones de ventana y optimización. Estan divididos en tres grupos según lo difíciles que me han resultado (totalmente subjetivo) y no estoy al 100% seguro de que el 100% de los ejercicios estén al 100% correcto asi que si alguien ve algún fallo, no dude en comunicarmelo. 

**Objetivo:** Practicar mucho todo tipo de consultas. 

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

