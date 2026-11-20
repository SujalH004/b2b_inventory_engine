# Database Schema
## 1. users
Stores shop owners and staff who manage inventory and orders.

- `id`: `SERIAL PRIMARY KEY` (unique auto-incrementing ID)
- `name`: `TEXT NOT NULL`
- `email`: `TEXT UNIQUE NOT NULL` (prevents duplicate accounts)
- `password_hash`: `TEXT NOT NULL` (never store plain-text passwords)
- `role`: `TEXT NOT NULL CHECK (role IN ('admin', 'staff'))`
- `created_at`: `TIMESTAMPTZ DEFAULT NOW()`

## 2. products
Stores every item the shop sells, along with its price and current stock.

- `id`: `SERIAL PRIMARY KEY`
- `sku`: `TEXT UNIQUE NOT NULL` (stock keeping unit; two products must never share one)
- `name`: `TEXT NOT NULL`
- `price_cents`: `INTEGER NOT NULL CHECK (price_cents >= 0)` (money stored in cents to avoid floating-point errors)
- `stock_qty`: `INTEGER NOT NULL DEFAULT 0 CHECK (stock_qty >= 0)` (stock can never go negative)
- `created_at`: `TIMESTAMPTZ DEFAULT NOW()`

## 3. orders
Stores one row per customer order, created by a staff member or admin.

- `id`: `SERIAL PRIMARY KEY`
- `user_id`: `INTEGER NOT NULL REFERENCES users(id)` (`FOREIGN KEY`: who created the order)
- `status`: `TEXT NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'completed', 'cancelled'))`
- `total_cents`: `INTEGER NOT NULL CHECK (total_cents >= 0)`
- `created_at`: `TIMESTAMPTZ DEFAULT NOW()`

## 4. order_items
Stores the individual product lines inside an order (one order has many items).

- `id`: `SERIAL PRIMARY KEY`
- `order_id`: `INTEGER NOT NULL REFERENCES orders(id) ON DELETE CASCADE` (`FOREIGN KEY`: deleting an order removes its items)
- `product_id`: `INTEGER NOT NULL REFERENCES products(id) ON DELETE RESTRICT` (`FOREIGN KEY`: which product was sold; a product with sales history cannot be deleted)
- `quantity`: `INTEGER NOT NULL CHECK (quantity > 0)`
- `unit_price_cents`: `INTEGER NOT NULL CHECK (unit_price_cents >= 0)` (price at time of sale, so later price changes don't rewrite history)

## 5. notifications
Stores alerts about products, such as a Low Stock Alert when `stock_qty` drops below 5.

- `id`: `SERIAL PRIMARY KEY`
- `product_id`: `INTEGER NOT NULL REFERENCES products(id)` (`FOREIGN KEY`: which product triggered the alert)
- `message`: `TEXT NOT NULL` (e.g. "Low stock: SKU-123 has 3 left")
- `is_read`: `BOOLEAN NOT NULL DEFAULT FALSE`
- `created_at`: `TIMESTAMPTZ DEFAULT NOW()`
- Index: `CREATE UNIQUE INDEX one_unread_alert_per_product ON notifications (product_id) WHERE is_read = FALSE` (a partial unique index: at most one unread alert per product, so repeated stock drops cannot create duplicates)

## Relationships

- One `user` has many `orders`.
- One `order` has many `order_items`.
- One `product` appears in many `order_items`.
- One `product` can have many `notifications`.