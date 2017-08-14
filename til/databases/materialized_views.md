# Materialized Views

A database is nothing without tables, and views have long simplified my life. But it was only recently that I discovered, thanks to the guidance of a work colleague (h/t [Michael Schaefermeyer](https://github.com/mschae)), the wonders of materialized views.

Materialized views give you all the benefits of speed and immediate access that a table on disk will give you, while also providing the simplification that a view can offer. While views alias a more complicated query, they still synchronously plan and execute the underlying query when called. _Materialized_ views, by contrast, can store the results of the underlying query on disk, such that when you need those results, you can have them immediately. Moreover, you can refresh them asynchronously, so your application never needs to wait on those complex queries to finish.

In postgres, we can instantiate a materialized view as simply as

```sql
CREATE MATERIALIZED VIEW orders_today AS (
  SELECT
    customers.id AS customer_id,
    customers.name AS name,
    customers.address AS address,
    orders.product_id AS product_id,
    orders.quantity AS quantity,
  FROM customers
  JOIN orders
    ON customers.id == orders.customer_id
  WHERE orders.created_at > now() - interval '24 hours';
)
```

Now say we get a new order after we've already instantiated our `orders_today` materialized view. To refresh the materialized view to include the new order, we need to call
```sql
REFRESH MATERIALIZED VIEW orders_today;
```

One downside of this is that we won't be able to query `orders_today` until the `REFRESH` query has finished.
To enable this kind of "concurrent" querying, we need to first create a unique index on our view, as such:

```sql
CREATE UNIQUE INDEX orders_today_customer_id ON orders_today (customer_id);
```

Then, assuming we're willing to pay a performance penalty, we can instead run
```sql
REFRESH MATERIALIZED VIEW CONCURRENTLY orders_today;
```

Now we'll be able to query our view as we simultaneously refresh it in the background, and life is simple:

```sql
SELECT name, address, product_id, quantity FROM orders_today;
```
