# Structure
```sql
    CREATE TABLE `orders` (
      `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
      `user_id` int(10) unsigned NOT NULL,
      `status` varchar(100) NOT NULL DEFAULT 'pending'
      PRIMARY KEY (`id`)
    );

    CREATE TABLE `users` (
      `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
      `name` varchar(100) NOT NULL,
      `country_code` char(2) NOT NULL,
      PRIMARY KEY (`id`)
    );

    CREATE TABLE `products` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
      `name` varchar(255) NOT NULL,
      `price` decimal(16,2) NOT NULL,
      PRIMARY KEY (`id`)
    );

    CREATE TABLE `products2orders` (
      `order_id` int(11) unsigned NOT NULL,
      `product_id` int(11) unsigned NOT NULL,
      `quantity` int(11) unsigned NOT NULL,
      PRIMARY KEY (`product_id`,`order_id`)
    );
```

# Query examples
## Orders by country
```sql
    SELECT u.country_code, count(*) as cnt
    FROM orders AS o
    INNER JOIN users AS u ON u.id = o.user_id
    GROUP BY u.country_code
    ORDER BY cnt DESC;
```
## Delivered orders by country
```sql
    SELECT u.country_code, count(*) as cnt
    FROM orders AS o
    INNER JOIN users AS u ON u.id = o.user_id
    WHERE o.status = 'delivered'
    GROUP BY u.country_code
    ORDER BY cnt DESC;
```
## All products bought by user 1
```sql
    SELECT p.name
    FROM orders AS o
    INNER JOIN products2orders AS p2o ON p2o.order_id = o.id
    INNER JOIN products AS p ON p.id = p2o.product_id
    WHERE o.user_id = 1
    GROUP BY p.name;
```
## All products bought by user 1, with count
```sql
    SELECT p.name, COUNT(*) AS cnt
    FROM orders AS o
    INNER JOIN products2orders AS p2o ON p2o.order_id = o.id
    INNER JOIN products AS p ON p.id = p2o.product_id
    WHERE o.user_id = 1
    GROUP BY p.name;
```
## All money spent by user 1
```sql
    SELECT SUM(p.price * p2o.quantity)
    FROM orders AS o
    INNER JOIN products2orders AS p2o ON p2o.order_id = o.id
    INNER JOIN products AS p ON p.id = p2o.product_id
    WHERE o.user_id = 1;
```
## Find top 10 spenders with names
```sql
    SELECT o.user_id, u.name, SUM(p.price * p2o.quantity) as total
    FROM orders AS o
    INNER JOIN products2orders AS p2o ON p2o.order_id = o.id
    INNER JOIN products AS p ON p.id = p2o.product_id
    INNER JOIN users AS u ON u.id = o.user_id
    GROUP BY user_id
    ORDER BY total DESC;
```