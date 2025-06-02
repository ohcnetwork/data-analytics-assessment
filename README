# Task

### **SQL Assessment – Data Analytics Role**

Design a *single SQL script* that a candidate can run end-to-end:

1. **Create & populate the schema (done).**
2. **Answer a set of 5 analytical questions.**

---

## 1 ️⃣ Schema & seed data

```sql
----------------------------------------------------------
--  A. Core dimension tables
----------------------------------------------------------
CREATE TABLE customers (
    customer_id      SERIAL PRIMARY KEY,
    full_name        TEXT        NOT NULL,
    country          TEXT        NOT NULL
);

CREATE TABLE products (
    product_id       SERIAL PRIMARY KEY,
    product_name     TEXT        NOT NULL,
    category         TEXT        NOT NULL,
    unit_price_usd   NUMERIC(8,2)
);

----------------------------------------------------------
--  B. Fact table with nested JSONB
--     • order_meta keeps flexible fields (payments, promo, etc.)
----------------------------------------------------------
CREATE TABLE orders (
    order_id         SERIAL PRIMARY KEY,
    customer_id      INT         REFERENCES customers(customer_id),
    order_date       DATE        NOT NULL,
    order_meta       JSONB       NOT NULL               -- nested JSONB
    /* example structure
       {
         "payment": { "method": "card", "card_type": "VISA" },
         "promo":   { "code": "NEW10", "discount_usd": 5.0 },
         "ship":    { "mode": "air",  "cost_usd": 12.5 }
       }
    */
);

----------------------------------------------------------
--  C. Bridge table (many-to-many) – quantity & price may vary per order
----------------------------------------------------------
CREATE TABLE order_lines (
    order_id         INT REFERENCES orders(order_id),
    product_id       INT REFERENCES products(product_id),
    qty              INT  NOT NULL,
    line_price_usd   NUMERIC(10,2)  NOT NULL,
    PRIMARY KEY (order_id, product_id)
);

----------------------------------------------------------
--  D. Sample data (minimal but covers edge cases)
----------------------------------------------------------
INSERT INTO customers (full_name, country) VALUES
  ('Alice Smith',   'USA'),
  ('Bala Iyer',     'India'),
  ('Chen Wei',      'Singapore');

INSERT INTO products (product_name, category, unit_price_usd) VALUES
  ('Blood Glucose Monitor',  'Medical Devices',  40.00),
  ('N95 Mask Box (20)',      'PPE',             25.00),
  ('Tele-consult Credit',    'Services',        15.00);

-- Order 1 – has promo + shipping
INSERT INTO orders (customer_id, order_date, order_meta) VALUES
  (1, '2025-05-01',
   '{
      "payment": { "method": "card", "card_type": "AMEX" },
      "promo":   { "code": "NEW10", "discount_usd": 5 },
      "ship":    { "mode": "ground", "cost_usd": 8.5 }
    }'::jsonb);

-- Order 2 – no promo, air shipping
INSERT INTO orders (customer_id, order_date, order_meta) VALUES
  (2, '2025-05-02',
   '{
      "payment": { "method": "upi", "provider": "GPay" },
      "ship":    { "mode": "air", "cost_usd": 15 }
    }'::jsonb);

-- Order lines
INSERT INTO order_lines VALUES
  (1, 1, 2, 80.00),  -- Alice bought 2 glucose monitors
  (1, 3, 1, 15.00),  -- and 1 tele-consult credit
  (2, 2, 4, 100.00); -- Bala bought 4 PPE boxes

```

---

## 2 ️⃣ Analytical questions the candidate must answer

> Instructions: Write one SQL query for each question. Use aliases (AS), JOINs, WITH CTEs, and JSONB operators (->, ->>, #>>) where appropriate.
> 

---

### Q1. Total sales with shipping & promo impact

Return, per `order_id`,

- **gross_line_total** (sum of `line_price_usd`)
- **promo_discount** (value inside `order_meta → promo → discount_usd`; treat missing as 0)
- **shipping_cost** (value inside `order_meta → ship → cost_usd`; treat missing as 0)
- **net_total** = gross_line_total – promo_discount + shipping_cost

---

### Q2. Top-selling product category (by revenue)

Using all tables, compute total revenue **after discounts** for each product **category** and return the category that generated the **highest** revenue.

---

### Q3. Customers with > 1 card brand used

Extract card brand (`order_meta → payment → card_type`) for card payments only. Return every `customer_id` who has placed ≥ 2 orders with **different** card brands (e.g., one Visa, one Amex).

---

### Q4. Monthly order funnel view

Using a **CTE**, build an intermediate table `monthly_orders` that:

| month | orders | distinct_customers | total_gross | total_net |

(Use the same net calculation as Q1.) Then `SELECT *` from that CTE for May 2025.

---

### Q5. Country-level basket analysis (correlated sub-query or CTE)

For each `country`, list:

- **avg_items_per_order** – average `SUM(qty)` per order
- **preferred_payment_method** – the JSONB payment method that appears most often for that country (break ties alphabetically)

Hint: Unnest JSONB inside a sub-query or lateral join.

---

### Deliverables

- A single `.sql` file that:
    1. **Creates & seeds** the schema above (copy/paste allowed).
    2. Contains each answer query clearly labelled `- Q1 …` through `- Q5 …`.

Candidates should **not** alter seed values; instead, write queries that would generalise to larger data.

---

### Evaluation focus

| Skill | Evidence in questions |
| --- | --- |
| Basic selects & aliases | Q1, Q2 |
| Multi-table joins | Q1, Q2 |
| CTE (`WITH`) usage | Q4, Q5 |
| JSONB navigation (nested) | Q1, Q3, Q5 |
| Analytical reasoning (aggregations, ranking, tie-break) | Q2, Q5 |

*Happy querying!*
