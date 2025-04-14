# SQL-PROJECT
### MUSIC STORE DATA ANALYSIS

### Q1: Senior-most employee

SELECT first_name + ' ' + last_name AS name, title
FROM employee
ORDER BY levels DESC
LIMIT 1;

---

### Q2: Country with the most invoices


SELECT billing_country, COUNT(*) AS total_invoices
FROM invoice
GROUP BY billing_country
ORDER BY total_invoices DESC
LIMIT 1;

---

###  Q3: Top 3 invoice totals

SELECT DISTINCT total
FROM invoice
ORDER BY total DESC
LIMIT 3;

---

###  Q4: City with the highest total invoice value

SELECT billing_city, SUM(total) AS total_revenue
FROM invoice
GROUP BY billing_city
ORDER BY total_revenue DESC;

---

###  Q5: Best customer (spent the most)

SELECT C.customer_id, C.first_name + ' ' + C.last_name AS name, SUM(I.total) AS total_spent
FROM customer C
JOIN invoice I ON C.customer_id = I.customer_id
GROUP BY C.customer_id, C.first_name, C.last_name
ORDER BY total_spent DESC
LIMIT 1;

---

###  Q6A: Most popular genre in each country

SELECT billing_country, name AS genre, SUM(quantity) AS total_purchases
FROM invoice
JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id
JOIN track ON invoice_line.track_id = track.track_id
JOIN genre ON track.genre_id = genre.genre_id
GROUP BY billing_country, name
HAVING SUM(quantity) = (
  SELECT MAX(total_genre)
  FROM (
    SELECT billing_country AS c, name AS g, SUM(quantity) AS total_genre
    FROM invoice
    JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id
    JOIN track ON invoice_line.track_id = track.track_id
    JOIN genre ON track.genre_id = genre.genre_id
    GROUP BY billing_country, name
    HAVING c = billing_country
  ) AS temp
)
ORDER BY billing_country;

---

###  Q6B: All Rock music listeners

SELECT DISTINCT C.first_name, C.last_name, C.email
FROM customer C
JOIN invoice I ON C.customer_id = I.customer_id
JOIN invoice_line IL ON I.invoice_id = IL.invoice_id
JOIN track T ON IL.track_id = T.track_id
JOIN genre G ON T.genre_id = G.genre_id
WHERE G.name = 'Rock'
ORDER BY C.email;

---

###  Q7: Top 10 rock artists by number of tracks

SELECT A.name AS artist_name, COUNT(*) AS rock_tracks
FROM artist A
JOIN album AL ON A.artist_id = AL.artist_id
JOIN track T ON AL.album_id = T.album_id
JOIN genre G ON T.genre_id = G.genre_id
WHERE G.name = 'Rock'
GROUP BY A.name
ORDER BY rock_tracks DESC
LIMIT 10;

---

### Q8: Tracks longer than average

SELECT name, milliseconds
FROM track
WHERE milliseconds > (SELECT AVG(milliseconds) FROM track)
ORDER BY milliseconds DESC;

---

###  Q9: Amount spent by each customer on each artist


SELECT C.first_name + ' ' + C.last_name AS customer, A.name AS artist, SUM(IL.unit_price * IL.quantity) AS total_spent
FROM customer C
JOIN invoice I ON C.customer_id = I.customer_id
JOIN invoice_line IL ON I.invoice_id = IL.invoice_id
JOIN track T ON IL.track_id = T.track_id
JOIN album AL ON T.album_id = AL.album_id
JOIN artist A ON AL.artist_id = A.artist_id
GROUP BY C.first_name, C.last_name, A.name
ORDER BY total_spent DESC;

---

###  Q10: Top spender per country


SELECT billing_country, C.first_name + ' ' + C.last_name AS customer, SUM(I.total) AS total_spent
FROM customer C
JOIN invoice I ON C.customer_id = I.customer_id
GROUP BY billing_country, C.first_name, C.last_name
HAVING SUM(I.total) = (
  SELECT MAX(country_total)
  FROM (
    SELECT billing_country AS c, SUM(total) AS country_total
    FROM invoice
    WHERE billing_country = I.billing_country
    GROUP BY billing_country
  ) AS temp
)
ORDER BY billing_country;
```


