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
**Problema:** Calcula el precio medio, precio mínimo y precio máximo por cada número de habitaicones. El resultado debe estar ordenado de mayor a menor número de habs. 

**Estructura de las tablas:**

inmuebles

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT habitaciones, AVG(precio) as avg_price,
			 MIN(precio) as min_price, MAX(precio) as max_price
FROM inmuebles
GROUP BY habitaciones
ORDER BY habitaciones DESC
```
</details>

### 📝 Reto 21
**Problema:** Muestra el promedio del Precio_m2 para cada distrito, sin incluir aquellos inmuebles clasificados como Outliers. 

**Estructura de las tablas:**

inmuebles

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D.png)

dim_barrios

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D_2.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT b.distrito, AVG(i.precio_m2) as promedio_precio_m2
FROM inmuebles i
JOIN dim_barrios b ON i.barrio_oficial = b.barrio_oficial
WHERE i.es_outlier IS FALSE
GROUP BY b.distrito
```
</details>

### 📝 Reto 22
**Problema:** Muesta el precio_m2 promedio y el número total de inmuebles para cada tipo de vivienda pero solo para aquellos inmuebles que tengan entre 2 y 4 habitaciones y cuya superficie sea mayor a 70 m2. Excluye los outliers.

**Estructura de las tablas:**

inmuebles

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT AVG(precio_m2) as avg_precio_m2, COUNT(id_inmueble) as count, tipo_vivienda
FROM inmuebles
WHERE habitaciones BETWEEN 2 AND 4
	AND metros > 70 
	AND es_outlier IS FALSE
GROUP BY tipo_vivienda
```
</details>

### 📝 Reto 23
**Problema:** Compara el promedio de Precio_Euro entre los días laborales y los findes de semana. Mapea la columna Es_Fin_Semana para que no aparezcan 1 y 0. 

**Estructura de las tablas:**

reservas_padel

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/c1d21b8e5602c389760fc0e1d3db2ffb1d2e8cbe/tablas/23F.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT AVG("Precio_Euro"), 
	CASE
		WHEN "Es_Fin_Semana" = 1 THEN 'Fin de semana'
		ELSE 'Día Laboral'
		END AS Dia
FROM reservas_padel
GROUP BY Dia
```
</details>

### 📝 Reto 24
**Problema:**  Clasifica las reservas en tres franjas horarias (mañana, tarde y noche) y cuenta cuántas reservas ‘Ocupadas’ hubo en cada una. 

**Estructura de las tablas:**

reservas_padel

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/c1d21b8e5602c389760fc0e1d3db2ffb1d2e8cbe/tablas/24F.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT 
    CASE
        WHEN Hora < '14:00' THEN 'Mañana'
        WHEN Hora < '20:00' THEN 'Tarde'
        ELSE 'Noche'
    END AS franja_horaria,
    COUNT(*) as total_reservas
FROM reservas_padel
WHERE Estado = 'Ocupada'
GROUP BY franja_horaria
```
</details>

### 📝 Reto 25
**Problema:**  Calcula el porcentaje de ocupación de las pistas para cada club. Muestra el resultado ordenado de mayor a menor. 
**Estructura de las tablas:**

reservas_padel

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/c1d21b8e5602c389760fc0e1d3db2ffb1d2e8cbe/tablas/25F.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT "Club",(COUNT(CASE WHEN "Estado" = 'Ocupada' THEN 1 END) * 100.0) / COUNT(*) as tasa_ocupacion
FROM reservas_padel
GROUP BY 1
ORDER BY tasa_ocupacion DESC
```
</details>

### 📝 Reto 26
**Problema:**  Ahora queremos identificar los clubes que están operando mucho pero ganando poco. Encuentra los clubes con un ingreso total inferior a 5.000$ y con más de 200 registros en el sistema. 

**Estructura de las tablas:**

reservas_padel

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/c1d21b8e5602c389760fc0e1d3db2ffb1d2e8cbe/tablas/26F.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT "Club", SUM("Ingreso_Generado") as ingresos_totales, COUNT(*) num_reserva
FROM reservas_padel
GROUP BY "Club"
HAVING SUM("Ingreso_Generado") < 5000
		AND COUNT(*) > 200
```
</details>

### 📝 Reto 27
**Problema:**  Calcula el promedio de popularity y de energy para cada género. Filtra los resultados para mostrar solo aquellos géneros que tengas más de 100 canciones en el dataset y ordénalos por popularidad de mayor a menor.  

**Estructura de las tablas:**

spotify_tracks

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/c1d21b8e5602c389760fc0e1d3db2ffb1d2e8cbe/tablas/27F.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT track_genre, AVG(popularity) as avg_popularity, 
		AVG(energy) as avg_energy
FROM spotify_tracks
GROUP BY track_genre
HAVING COUNT(*) > 100
ORDER BY avg_popularity DESC
```
</details>

### 📝 Reto 28
**Problema:**  Crea una consulta que clasifique las canciones en tres categorías según sus atributos:

- **'Fiesta'**: Si `danceability` > 0.7 y `energy` > 0.7.
- **'Relajación'**: Si `danceability` < 0.4 y `acousticness` > 0.6.
- **'Estándar'**: Cualquier otro caso.
- 
Muestra cuántas canciones hay en cada categoría y su popularidad media. 

**Estructura de las tablas:**

spotify_tracks

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/c1d21b8e5602c389760fc0e1d3db2ffb1d2e8cbe/tablas/28F.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT CASE
		WHEN danceability > 0.7 AND energy > 0.7 THEN 'Fiesta'
		WHEN danceability < 0.4 AND acousticness > 0.6 THEN 'Relajación'
		ELSE 'Estándar'
		END AS Mood, 
		COUNT(*) as total_songs, 
		AVG(popularity) as avg_popularity
FROM spotify_tracks
GROUP BY Mood
```
</details>

### 📝 Reto 29
**Problema:**  Encuentra los artistas que aparecen en más de 3 géneros distintos y que tienen una popularidad media superior a 70 en total. Muestra nombre del artista, número de géneros, popularidad media y todo ordenado de mayor a menor popularidad.  

**Estructura de las tablas:**

spotify_tracks

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/24916495679f3d1fb4dd62aff4ebfa6e1cd386ad/tablas/29F.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT 
    artists, 
    COUNT(DISTINCT track_genre) as num_generos, 
    ROUND(AVG(popularity), 2) as popularidad_media
FROM spotify_tracks
GROUP BY artists
HAVING COUNT(DISTINCT track_genre) > 3 
   AND AVG(popularity) > 70
ORDER BY popularidad_media DESC;
```
</details>

### 📝 Reto 30
**Problema:**  Devuelve el nombre del usuario y la suma total de dinero que ha gastado en pedidos 'Entregados', pero solo para aquellos usuarios que hayan gastado más de 200€ en total en la plataforma.

**Estructura de las tablas:**

usuarios

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/usuarios.png)

pedidos 

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/pedidos.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT u.nombre, SUM(p.monto_total) as total_gastado
FROM usuarios u
JOIN pedidos p ON u.usuario_id = p.usuario_id
WHERE estado = 'Entregado'
GROUP BY u.nombre
HAVING SUM(p.monto_total) > 200
```
</details>

### 📝 Reto 31
**Problema:**  Haz una consulta que muestre el tipo_comida y el total de ingresos generados (pedidos 'Entregados') por cada categoría. Ordena el resultado para que el tipo de comida que más dinero genera aparezca primero.

**Estructura de las tablas:**

restaurantes

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/restaurantes.png)

pedidos 

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/pedidos.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT r.tipo_comida, SUM(p.monto_total) as ingresos_generados
FROM restaurantes r
JOIN pedidos p ON r.restaurante_id = p.restaurante_id
WHERE estado = 'Entregado'
GROUP BY r.tipo_comida
ORDER BY ingresos_generados DESC
```
</details>

### 📝 Reto 32
**Problema:**  Saca un listado que muestre el nombre del usuario, el nombre_restaurante donde compró y la fecha_pedido. Filtra la consulta para que solo aparezcan los pedidos realizados en restaurantes de tipo 'Asiática'.

**Estructura de las tablas:**

restaurantes

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/restaurantes.png)

pedidos 

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/pedidos.png)

usuarios

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/usuarios.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT u.nombre, r.nombre_restaurante, p.fecha_pedido
FROM usuarios u 
JOIN pedidos p ON p.usuario_id = u.usuario_id
JOIN restaurantes r ON r.restaurante_id = p.restaurante_id 
WHERE r.tipo_comida = 'Asiática'
```
</details>

### 📝 Reto 33
**Problema:**  El equipo de Operaciones quiere entender el tamaño de los "tickets" de compra. Clasifica los pedidos en tres categorías según su `monto_total`:

- 'Pequeño' (menos de 15€)
- 'Medio' (entre 15€ y 30€)
- 'Grande' (más de 30€)
Muestra el nombre de la categoría y **cuenta cuántos pedidos 'Entregados'** hay en cada una.

**Estructura de las tablas:**

pedidos 

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/pedidos.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT  
			CASE
				WHEN monto_total < 15 THEN 'Pequeño'
				WHEN monto_total BETWEEN 15 AND 30 THEN 'Medio'
				ELSE 'Grande'
				END AS categoria, 
				COUNT(*) as numero_pedidos_entregados
FROM pedidos
WHERE estado = 'Entregado'
GROUP BY categoria
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

### 📝 Reto 15
**Problema:** Realiza una consulta para clasificar los barrios dentro de cada distrito según el precio por m2 de más caro a mas barato. Debes mostrar el nombre del distrio, el barrio, el número de inmuebles disponibles, el precio por m2 redondeado a 2 decimales, la renta media y el índice de seguridad. Ordena los resultados según el distrito y el ranking.  
**Estructura de las tablas:**

inmuebles

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D.png)

dim_barrios

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D_2.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT
d.distrito,
f.barrio_oficial,
COUNT(f.id_inmueble) as oferta_disponible,
ROUND(AVG(f.precio_m2), 0) as precio_m2_medio,
d.renta_media,
d.seguridad_index,
RANK() OVER(PARTITION BY d.distrito ORDER BY AVG(f.precio_m2) DESC) as ranking_caro_distrito
FROM inmuebles f
JOIN dim_barrios d ON f.barrio_oficial = d.barrio_oficial
GROUP BY d.distrito, f.barrio_oficial, d.renta_media, d.seguridad_index
ORDER BY d.distrito, ranking_caro_distrito;
					
```
</details>

### 📝 Reto 16
**Problema:** Compara el precio medio de los pisos marcados como “Infravalorados” contra el precio medio real del barrio al que pertenecen.  Muestra barrio, precio real, precio de infravalorados, diferencia bruta y conteo de pisos infravalorados por barrio. Muestra el resultado ordenado de mayor a menor diferencia. 
**Estructura de las tablas:**

inmuebles

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D.png)

dim_barrios

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D_2.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT 
    f.barrio_oficial,
    -- Precio medio del mercado sin infravalorados
    ROUND(AVG(CASE WHEN f.infravalorado = FALSE THEN f.precio END), 0) as precio_mercado,
    
    -- Precio medio de los infravalorados
    ROUND(AVG(CASE WHEN f.infravalorado = TRUE THEN f.precio END), 0) as precio_oportunidad,
    
    -- Diferencia
    ROUND(
        AVG(CASE WHEN f.infravalorado = FALSE THEN f.precio END) - 
        AVG(CASE WHEN f.infravalorado = TRUE THEN f.precio END)
    , 0) as margen_bruto_medio,
    
    -- Conteo de infravalorados
    SUM(CASE WHEN f.infravalorado = TRUE THEN 1 ELSE 0 END) as n_chollos
FROM inmuebles f
GROUP BY f.barrio_oficial
HAVING SUM(CASE WHEN f.infravalorado = TRUE THEN 1 ELSE 0 END) > 0 
ORDER BY margen_bruto_medio DESC;
```
</details>

### 📝 Reto 17
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

### 📝 Reto 18
**Problema:**  Muestra los tres pisos con el precio_m2 cuadrado más barato dentro de cada barrio. Muestra titulo, barrio, precio_2 y posición en el ranking. 

**Estructura de las tablas:**

inmuebles

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH ranking_3 as (
SELECT titulo, barrio_oficial, precio_m2, 
		DENSE_RANK () OVER (PARTITION BY barrio_oficial ORDER BY precio_m2) as ranking
FROM inmuebles
)

SELECT titulo, barrio_oficial, precio_m2, ranking
FROM ranking_3
WHERE ranking < 4
```
</details>

### 📝 Reto 19
**Problema:**  Realiza una consulta para encontrar los barrios cuya media de precio se sus inmuebles sea superior a la media global del precio de los inmuebles. 

**Estructura de las tablas:**

inmuebles

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D.png)



<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT barrio_oficial, AVG(precio) as avg_precio
FROM inmuebles
GROUP BY barrio_oficial
HAVING AVG(precio) > (
			SELECT AVG(precio)
			FROM inmuebles
)
```
</details>

### 📝 Reto 20
**Problema:**  Realiza una consulta para dividir los barrios en dos grupos según su renta media (<25.000 → ‘Renta Alta’ y el resto ‘Renta Media / Baja). Para cada grupo, calcula el promedio del precio_m2 y el índice de seguridad promedio. 

**Estructura de las tablas:**

inmuebles

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D.png)

dim_barrios

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D_2.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT 
	AVG(i.precio_m2) as avg_precio_m2, AVG(b.seguridad_index) as avg_seg_index, 
	CASE 
	 WHEN b.renta_media > 25000 THEN 'Renta Alta'
	 WHEN b.renta_media <= 25000 THEN 'Renta Media / Baja'
	 END AS clasificacion_renta
FROM dim_barrios b
JOIN inmuebles i ON i.barrio_oficial = b.barrio_oficial
GROUP BY clasificacion_renta
```
</details>

### 📝 Reto 21
**Problema:**  Calcula el porcentaje de inmuebles que aporta cada barrio_oficial a su respectivo distrito. El resultado debe mostrar: Distrito, Barrio y Porcentaje. 
**Estructura de las tablas:**

inmuebles

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D.png)

dim_barrios

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D_2.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH conteo_por_barrio AS (
    SELECT 
        b.distrito, 
        b.barrio_oficial,
        COUNT(*) as total_inmuebles_barrio
    FROM inmuebles i
    JOIN dim_barrios b ON i.barrio_oficial = b.barrio_oficial
    GROUP BY b.distrito, b.barrio_oficial
)

SELECT 
    distrito,
    barrio_oficial,
    total_inmuebles_barrio,
    SUM(total_inmuebles_barrio) OVER(PARTITION BY distrito) as total_inmuebles_distrito,
    (total_inmuebles_barrio * 100.0 / SUM(total_inmuebles_barrio) OVER(PARTITION BY distrito)) as porcentaje
FROM conteo_por_barrio
ORDER BY distrito, porcentaje DESC;
```
</details>

### 📝 Reto 22
**Problema:**  Encuentra los barrios que tengan una seguridad_index superior a 80 pero cuya renta_media sea inferior a la media global de toda la ciudad.

**Estructura de las tablas:**

dim_barrios

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D_2.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT barrio_oficial, seguridad_index, renta_media
FROM dim_barrios
WHERE seguridad_index > 80
		AND renta_media < (
			SELECT AVG(renta_media)
			FROM dim_barrios
		)
```
</details>

### 📝 Reto 23
**Problema:** Muestra el top 3 de las habilidades más demandadas para cada ciudad. El resultado debe incluir: Ciudad, Habilidad y el número de ofertas que la piden. 

**Estructura de las tablas:**

dim_ofertas

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/fdef20618ac3329b0a485afd3624d63c0eaed4ba/tablas/23M.png)


fact_skills

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/fdef20618ac3329b0a485afd3624d63c0eaed4ba/tablas/23M_1.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH ranking_tech AS (
    SELECT 
        o.city, 
        s.skill, 
        COUNT(*) as numero_ofertas, 
        ROW_NUMBER() OVER(PARTITION BY o.city ORDER BY COUNT(*) DESC) as ranking 
    FROM dim_ofertas o
    JOIN fact_skills s ON o.job_id = s.job_id
    GROUP BY o.city, s.skill
)
SELECT city, skill, numero_ofertas
FROM ranking_tech
WHERE ranking <= 3
ORDER BY city, ranking;
```
</details>

### 📝 Reto 24
**Problema:**  Identifica para cada club la hora del día que ha generado el mayor ingreso total. El resultado debe mostrar nombre del club, hora e ingreso total a esa hora. 

**Estructura de las tablas:**

reservas_padel

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ea988461762d85764bbe086d33266c022769d8a9/tablas/24I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH ranking as (
	SELECT "Club", "Hora", SUM("Ingreso_Generado") as ingresos, 
		RANK () OVER(PARTITION BY "Club" ORDER BY SUM("Ingreso_Generado") DESC) as ranking
	FROM reservas_padel
	GROUP BY "Club", "Hora"
)
SELECT "Club", "Hora", ingresos
FROM ranking
WHERE ranking < 2
```
</details>

### 📝 Reto 25
**Problema:**  Calcula el ingreso total por cada día y añade una columna que muestre el ingreso acumulado (la suma del día actual más todos los anteriores). 

**Estructura de las tablas:**

reservas_padel

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ea988461762d85764bbe086d33266c022769d8a9/tablas/25I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH ingresos_por_dia AS (
    SELECT 
        Fecha, 
        SUM(Ingreso_Generado) as ingreso_del_dia
    FROM reservas_padel
    GROUP BY Fecha
)

SELECT 
    Fecha,
    ingreso_del_dia,
    SUM(ingreso_del_dia) OVER (ORDER BY Fecha) as acumulado_total
FROM ingresos_por_dia
ORDER BY Fecha;
```
</details>

### 📝 Reto 26
**Problema:**  Para cada track_genre, encuentra el nombre del artista y el nombre de la canción que tenga la popularidad más alta dentro de ese género. 

**Estructura de las tablas:**

spotify_tracks

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ea988461762d85764bbe086d33266c022769d8a9/tablas/26I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH rankings as(
	SELECT track_genre, artists, track_name, popularity,
		RANK() OVER(PARTITION BY track_genre ORDER BY popularity DESC) as ranking
	FROM spotify_tracks
)

SELECT track_genre, artists, track_name, popularity
FROM rankings
WHERE ranking = 1
```
</details>

### 📝 Reto 27
**Problema:**  Muestra el nombre de la canción, el género y su duración, pero solo de aquellas canciones que duren más que el promedio de su propio género. Además, añade una columna que calcule la diferencia en segundos respecto a esa media. 

**Estructura de las tablas:**

spotify_tracks

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ea988461762d85764bbe086d33266c022769d8a9/tablas/27I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH means AS (
    SELECT 
        track_name, 
        track_genre, 
        duration_ms, 
        AVG(duration_ms) OVER(PARTITION BY track_genre) as mean_genre
    FROM spotify_tracks
) 
SELECT 
    track_name, 
    track_genre, 
    duration_ms, 
    ROUND((duration_ms - mean_genre) / 1000.0, 2) as diff_seconds_over_mean
FROM means
WHERE duration_ms > mean_genre
ORDER BY diff_seconds_over_mean DESC;
```
</details>

### 📝 Reto 28
**Problema:**  Por cada género, muestra dos columnas: una con la popularidad media de las canciones explícitas y otra con la popularidad media de las no explícitas. 

**Estructura de las tablas:**

spotify_tracks

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ea988461762d85764bbe086d33266c022769d8a9/tablas/28I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT 
    track_genre, 
    AVG(CASE WHEN explicit = TRUE THEN popularity END) AS pop_explicita,
    AVG(CASE WHEN explicit = FALSE THEN popularity END) AS pop_limpia
FROM spotify_tracks
GROUP BY track_genre
ORDER BY track_genre
```
</details>

### 📝 Reto 29
**Problema:**  Calcula la popularidad media de cada género, pero añade una columna que muestre la diferencia entre esa media y la popularidad media global de todas las canciones del data set. Muestra una fila por cada género y ordena el resultado de mayor a menor diferencia.

**Estructura de las tablas:**

spotify_tracks

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/24916495679f3d1fb4dd62aff4ebfa6e1cd386ad/tablas/29I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT DISTINCT track_genre, 
		AVG(popularity) OVER(PARTITION BY track_genre) as avg_pop_genre, 
		AVG(popularity) OVER(PARTITION BY track_genre) - (SELECT AVG(popularity) FROM spotify_tracks) as avg_diff
FROM spotify_tracks
ORDER BY avg_diff DESC
```
</details>

### 📝 Reto 30
**Problema:**  Divide todas las canciones del dataset en 4 grupos iguales (cuartiles) basados en su energy (del más tranquilo al más enérgico). Para cada cuartil, muestra la popularidad media y la bailabilidad media. Muestra los cuartiles ordenados. 

**Estructura de las tablas:**

spotify_tracks

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/24916495679f3d1fb4dd62aff4ebfa6e1cd386ad/tablas/30I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH cuartiles as(
	SELECT popularity, danceability, NTILE(4) OVER(ORDER BY energy) as num_cuartil
	FROM spotify_tracks
)

SELECT num_cuartil, AVG(popularity) as avg_popularity, AVG(danceability) as avg_danceabiltiy
FROM cuartiles
GROUP BY num_cuartil
ORDER BY num_cuartil
```
</details>

### 📝 Reto 31
**Problema:**   Identifca las 10 canciones y sus artistas que aparecen en el mayor número de géneros distintos. Muestra el nombre de la canción, el artista y la lista de géneros en una sola celda ordenados alfabéticamente (se necesita una función especial). 

**Estructura de las tablas:**

spotify_tracks

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/24916495679f3d1fb4dd62aff4ebfa6e1cd386ad/tablas/31I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT track_name, artists, STRING_AGG(track_genre, ', ' ORDER BY track_genre) as lista_generos, 
		COUNT(DISTINCT track_genre) as total_generos
FROM spotify_tracks
GROUP BY track_name, artists
ORDER BY total_generos DESC
LIMIT 10
```
</details>

### 📝 Reto 32
**Problema:**   Identifica si hay Ids de canciones duplicados. Si la respuesta es afirmativa (vamos a suponer que la es), quedate con el registro que más popularidad tenga. El resto del dataset debe permanecer igual. 

**Estructura de las tablas:**

spotify_tracks

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/24916495679f3d1fb4dd62aff4ebfa6e1cd386ad/tablas/32I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
# Detección de duplicados 
SELECT track_id, COUNT(*)
FROM spotify_tracks
GROUP BY track_id
HAVING COUNT(*) > 1

# Selección del registro con mayor popularidad
WITH ranking_id as(
	SELECT *,
		ROW_NUMBER() OVER(PARTITION BY track_id ORDER BY popularity DESC) as ranking
	FROM spotify_tracks
)

SELECT *
FROM ranking_id
WHERE ranking = 1
```
</details>

### 📝 Reto 33
**Problema:**   Calcula el promedio del precio de cierre (close_price) de los últimos 7 días (el actual y los 6 anteriores). Muesta solo los resultados para el año 2024. Muestra date, close_price y el promedio (cuidado con los primeros días de enero). 

**Estructura de las tablas:**

spotify_tracks

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/24916495679f3d1fb4dd62aff4ebfa6e1cd386ad/tablas/33I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH calculo_media AS (
    SELECT 
        date, 
        close_price, 
        AVG(close_price) OVER(ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) as media_movil_7d
    FROM google_stock
)
SELECT * FROM calculo_media
WHERE date >= '2024-01-01' AND date <= '2024-12-31'
ORDER BY date;
```
</details>

### 📝 Reto 34
**Problema:**   Encuentra los días en los que el price_range fue un 50% mayor que el promedio de price_range de su mismo mes y año. Muestra la fecha, el price_range de ese día, el promedio mensual de ese mes específico y el % de exceso. 

**Estructura de las tablas:**

google_stock

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/24916495679f3d1fb4dd62aff4ebfa6e1cd386ad/tablas/34I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH promedios AS (
    SELECT 
        date, 
        price_range, 
        month, 
        year, 
        AVG(price_range) OVER(PARTITION BY year, month) as avg_mensual
    FROM google_stock
)
SELECT 
    date, 
    price_range, 
    ROUND(avg_mensual::numeric, 2) as avg_mensual,
    ROUND(((price_range - avg_mensual) / avg_mensual * 100)::numeric, 2) as pct_exceso_real
FROM promedios
WHERE price_range > (avg_mensual * 1.5)
ORDER BY pct_exceso_real DESC;
```
</details>

### 📝 Reto 35
**Problema:**   Calcula para cada día la media móvil del close_price de 20 días y la de 50 días. Muestra solo los dias del año 2023 donde la media de 20 sea mayor que la de 50.  

**Estructura de las tablas:**

google_stock

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/24916495679f3d1fb4dd62aff4ebfa6e1cd386ad/tablas/35I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH medias_moviles AS (
    SELECT 
        date, 
        close_price,
        AVG(close_price) OVER(ORDER BY date ROWS BETWEEN 19 PRECEDING AND CURRENT ROW) as ma20,
        AVG(close_price) OVER(ORDER BY date ROWS BETWEEN 49 PRECEDING AND CURRENT ROW) as ma50
    FROM google_stock
)
SELECT 
    date, 
    ROUND(close_price::numeric, 2) as precio,
    ROUND(ma20::numeric, 2) as ma20,
    ROUND(ma50::numeric, 2) as ma50
FROM medias_moviles
WHERE ma20 > ma50              
  AND date >= '2023-01-01'     
ORDER BY date;
```
</details>

### 📝 Reto 36
**Problema:**   Encuentra los días en los que el precio de apertura (open_price) de hoy fue más de un 2% superior al precio de cierre (close_price) del día anterior. 

**Estructura de las tablas:**

google_stock

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/24916495679f3d1fb4dd62aff4ebfa6e1cd386ad/tablas/36I.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH dia_anterior as(
	SELECT date, open_price,
		LAG(close_price) OVER(ORDER BY date) as lag_close_price
	FROM google_stock
)

SELECT date, open_price
FROM dia_anterior
WHERE open_price > (lag_close_price * 1.02)
```
</details>

### 📝 Reto 37
**Problema:**   Realiza una consulta para mostrar los 3 estados de Brasil (customer_state) donde los clientes están más insatisfechos (promedio de review_score más bajo). 

**Estructura de las tablas:**

![otra](https://github.com/Nachoide100/Practicar-SQL/blob/aee7cae800b9ee041eb9ffb1acf25edc300b91e8/tablas/37I_1.png)
![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/aee7cae800b9ee041eb9ffb1acf25edc300b91e8/tablas/37I.png)
![otramas](https://github.com/Nachoide100/Practicar-SQL/blob/aee7cae800b9ee041eb9ffb1acf25edc300b91e8/tablas/37I_2.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT customer_state, AVG(r.review_score) as avg_review_score
FROM olist_customers c
JOIN olist_orders o ON o.customer_id = c.customer_id
JOIN olist_order_reviews r ON o.order_id = r.order_id
GROUP BY c.customer_state
ORDER BY avg_review_score
LIMIT 3
```
</details>

### 📝 Reto 38
**Problema:**   Realiza una consulta que identifique a los clientes más fieles, es decir, aquellos que han realizado más de un pedido en toda la historia. Debes mostrar el id único del cliente, la cantidad total de pedidos realizados, la fecha y de su primer y último pedido y el tiempo que lleva siendo cliente.

**Estructura de las tablas:**

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/aee7cae800b9ee041eb9ffb1acf25edc300b91e8/tablas/37I.png)
![otramas](https://github.com/Nachoide100/Practicar-SQL/blob/aee7cae800b9ee041eb9ffb1acf25edc300b91e8/tablas/37I_2.png)

<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT 
    c.customer_unique_id, 
    COUNT(o.order_id) as total_pedidos,
    MIN(o.order_purchase_timestamp) as primer_pedido,
    MAX(o.order_purchase_timestamp) as ultimo_pedid
    AGE(MAX(o.order_purchase_timestamp), MIN(o.order_purchase_timestamp)) as tiempo_de_vida
FROM olist_customers c
JOIN olist_orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_unique_id
HAVING COUNT(o.order_id) > 1
ORDER BY total_pedidos DESC;
```
</details>

### 📝 Reto 39
**Problema:**   El equipo de fidelización quiere analizar la primera experiencia de cada cliente. Extrae un listado que muestre exclusivamente el primer pedido que hizo cada usuario en la plataforma. Devuelve el usuario_id, la fecha_pedido y el monto_total

**Estructura de las tablas:**

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/pedidos.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH primer_pedido AS(
	SELECT usuario_id, fecha_pedido, monto_total, 
				ROW_NUMBER() OVER (PARTITION BY usuario_id ORDER BY fecha_pedido) as fecha
	FROM pedidos
)

SELECT usuario_id, fecha_pedido, monto_total
FROM primer_pedido
WHERE fecha = 1
```
</details>

### 📝 Reto 40
**Problema:**   Calcula los ingresos totales (estado ‘Entregado’) agrupados por mes. Añade una columna que muestre los ingresos del mes inmediatamente anterior para poder ver la evolución. 

**Estructura de las tablas:**

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/pedidos.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH ingresos_por_mes AS(
		SELECT DATE_TRUNC('month', fecha_pedido) as mes, SUM(monto_total) as total_ingresos
		FROM pedidos
		WHERE estado = 'Entregado'
		GROUP BY DATE_TRUNC('month', fecha_pedido)
)

SELECT mes, total_ingresos, 
			LAG(mes, 1) OVER (ORDER BY mes) as ingresos_mes_anterior
FROM ingresos_por_mes
```
</details>

### 📝 Reto 41
**Problema:**  Para cada usuario, muestra su nombre y el nombre_restaurante en el que ha hecho mayor cantidad de pedidos. 

**Estructura de las tablas:**

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/pedidos.png)

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/restaurantes.png)

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/497c18b4eef62ef26d8d498f8cbb3d5c8bbbafc3/tablas/usuarios.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH ranking_rest AS(
	SELECT u.nombre as nombre_usuario, r.nombre_restaurante, COUNT(p.pedido_id) as num_pedidos,
				ROW_NUMBER() OVER(PARTITION BY u.usuario_id ORDER BY COUNT(p.pedido_id) DESC) as rnk_pedidos
	FROM usuarios u
	JOIN pedidos p ON u.usuario_id = p.usuario_id
	JOIN restaurantes r ON r.restaurante_id = p.restaurante_id
	GROUP BY u.nombre, r.nombre_restaurante
)

SELECT nombre_usuario, nombre_restaurante
FROM ranking_rest
WHERE rnk_pedidos = 1
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

### 📝 Reto 04
**Problema:**  Calcula cuántos años de sueldo íntegro necesita un vecino de un barrio para comprar un piso ahí. Clasifica los resultados en tres categorías según la cantidad de años: 

- Más de 15 → gentrificación
- Menos de 8 → oportunidad local
- Resto → Equlibrado

Ordena el resultado según los años de esfuerzo de mayor a menor.

**Estructura de las tablas:**

inmuebles

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D.png)

dim_barrios

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/ec80130904319fbb611206721694604a643c03cd/tablas/Reto15I%2C%2016I%2C%204D_2.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH metricas_barrio AS (
    SELECT 
        f.barrio_oficial,
        AVG(f.precio) as precio_medio_zona,
        d.renta_media
    FROM fact_inmuebles f
    JOIN dim_barrios d ON f.barrio_oficial = d.barrio_oficial
    GROUP BY f.barrio_oficial, d.renta_media
)
SELECT 
    barrio_oficial,
    ROUND(precio_medio_zona, 0) as precio_medio,
    renta_media,
    -- Cálculo de años de esfuerzo
    ROUND(precio_medio_zona / NULLIF(renta_media, 0), 1) as anos_esfuerzo_fiscal,
    CASE 
        WHEN (precio_medio_zona / renta_media) > 15 THEN 'Gentrificación'
        WHEN (precio_medio_zona / renta_media) < 8 THEN 'Oportunidad Local'
        ELSE 'Equilibrado'
    END as estado_mercado
FROM metricas_barrio
ORDER BY anos_esfuerzo_fiscal DESC
```
</details>

### 📝 Reto 05
**Problema:**   Se piensa que para conseguir un buen puesto hay que saber Python y SQL. Encuentra cuántas ofertas de trabajo requieren específicamente ambas habilidades simultáneamente. Muestra el nombre de la empresa y el título del puesto de aquellas que piden este ‘combo’. 

**Estructura de las tablas:**

dim_ofertas

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/fdef20618ac3329b0a485afd3624d63c0eaed4ba/tablas/23M.png)


fact_skills

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/fdef20618ac3329b0a485afd3624d63c0eaed4ba/tablas/23M_1.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT 
    o.company, 
    o.title
FROM dim_ofertas o
JOIN fact_skills s ON o.job_id = s.job_id
WHERE s.skill IN ('Python', 'SQL') -- Paso 1: Filtramos solo las filas que tengan una de las dos
GROUP BY o.job_id, o.company, o.title -- Paso 2: Agrupamos por oferta
HAVING COUNT(DISTINCT s.skill) = 2; -- Paso 3: Nos quedamos solo con las que tienen AMBAS
```
</details>

### 📝 Reto 06
**Problema:**   Para cada tipo de modalidad de trabajo (teleworking), calcula el porcentaje de ofertas que requieren la habilidad ‘Excel’. Compara así, si el teletrabajo tiene mayor dependencia de esta herramienta que el trabajo presencial.

**Estructura de las tablas:**

dim_ofertas

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/fdef20618ac3329b0a485afd3624d63c0eaed4ba/tablas/23M.png)


fact_skills

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/fdef20618ac3329b0a485afd3624d63c0eaed4ba/tablas/23M_1.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
SELECT 
    o.teleworking,
    COUNT(DISTINCT o.job_id) as total_ofertas,
    COUNT(DISTINCT CASE WHEN s.skill = 'Excel' THEN o.job_id END) as ofertas_con_excel,
    -- Cálculo del porcentaje
    (COUNT(DISTINCT CASE WHEN s.skill = 'Excel' THEN o.job_id END) * 100.0 / 
     COUNT(DISTINCT o.job_id)) as porcentaje_excel
FROM dim_ofertas o
LEFT JOIN fact_skills s ON o.job_id = s.job_id
GROUP BY o.teleworking;
```
</details>

### 📝 Reto 07
**Problema:**  Escribe una única consulta que devuelva una tabla con la siguiente información por cada Club:

- **Nombre del Club**.
- **Ingreso_Total**: Suma de todo el dinero generado.
- **Tasa_Ocupacion**: Porcentaje de slots 'Ocupada' sobre el total del club.
- **Ranking_Nacional**: Su posición (1º, 2º, 3º...) comparado con los demás clubes basándote en el ingreso total.
- **Pista_Estrella**: El nombre de la pista (`Nombre_Pista`) que **más dinero** ha generado dentro de ese club específico

Además: 

- **Filtro Obligatorio:** Solo deben aparecer en el informe final los clubes que hayan generado **más de 5.000€** en total.
- **Ordenación:** El informe debe estar ordenado por el `Ranking_Nacional` (el club que más gana, arriba).

**Estructura de las tablas:**

reservas_padel

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/be02886eb8916395656acd55528951ac27b4380e/tablas/7D.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH totales_club AS(
	SELECT "Club", SUM("Ingreso_Generado") as ingresos_totales, 
			(COUNT(CASE WHEN "Estado" = 'Ocupada' THEN 1 END) * 100.0 / COUNT(*)) as tasa_ocupacion,
			ROW_NUMBER() OVER(ORDER BY SUM("Ingreso_Generado") DESC) as ranking_nacional
	FROM reservas_padel
	GROUP BY "Club"
),
ranking_pistas AS(
	SELECT "Club", "Nombre_Pista", SUM("Ingreso_Generado") as ingresos_totales, 
			ROW_NUMBER() OVER(PARTITION BY "Club" ORDER BY SUM("Ingreso_Generado") DESC) as ranking_pistas
	FROM reservas_padel
	GROUP BY "Club", "Nombre_Pista"
)

SELECT r."Club", t."ingresos_totales", t."tasa_ocupacion", t."ranking_nacional", r."Nombre_Pista" as pista_estrella
FROM totales_club t
JOIN ranking_pistas r ON t."Club" = r."Club"
WHERE r."ranking_pistas" = 1 
		AND t."ingresos_totales" > 5000
ORDER BY t.ranking_nacional
```
</details>

### 📝 Reto 08
**Problema:**  Escribe una consulta que devuelva, para cada género, el nombre del artista que tiene le promedio de popularidad más alto, pero con una condición: el artista debe tener al menos 5 canciones dentro de ese género. Muestra género, artista, popularidad media y total de canciones. 

**Estructura de las tablas:**

spotify_tracks

![Tabla user_transactions](https://github.com/Nachoide100/Practicar-SQL/blob/be02886eb8916395656acd55528951ac27b4380e/tablas/8D.png)


<details>
  <summary><b>Ver Solución SQL 🔑</b></summary>
  
  ```sql
WITH stats_artistas AS (
    SELECT 
        track_genre, 
        artists, 
        AVG(popularity) as avg_popularity,
        COUNT(*) as total_canciones
    FROM spotify_tracks
    GROUP BY track_genre, artists
    HAVING COUNT(*) >= 5 
),
ranking_final AS (
    SELECT 
        track_genre, 
        artists, 
        avg_popularity, 
        total_canciones,
        RANK() OVER(PARTITION BY track_genre ORDER BY avg_popularity DESC) as rnk
    FROM stats_artistas
)
SELECT track_genre, artists, ROUND(avg_popularity, 2), total_canciones
FROM ranking_final
WHERE rnk = 1
```
</details>

