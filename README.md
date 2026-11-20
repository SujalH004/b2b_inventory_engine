# B2B Inventory & Order Engine

An internal backend and operations dashboard built for shop owners and staff to manage product catalogs, track real-time stock levels, and process orders safely. Unlike a public consumer storefront, this system focuses on **data integrity**, **concurrency control**, and **relational reporting**.

## Why PostgreSQL & ACID Transactions?

Money and inventory require strict rules. If two staff members or buyers attempt to order the last unit of a product at the exact same millisecond, naive application checks can oversell stock. This project uses PostgreSQL transactions (`BEGIN` / `COMMIT` / `ROLLBACK`), row-level locking (`SELECT ... FOR UPDATE`), and database-level `CHECK` constraints (`stock_qty >= 0`) to guarantee stock never goes negative.

## Tech Stack

* **Frontend:** React, Tailwind CSS
* **Backend:** Node.js, Express
* **Database:** PostgreSQL (raw SQL migrations, Foreign Keys, and `JOIN` reporting)

## Core Features (MVP)

* **Inventory Management:** Create, update, and search products by unique SKU with integer-based pricing (`price_cents`) to avoid floating-point rounding errors.
* **Atomic Order Processing:** Deduct stock and record order line items inside a single ACID transaction with row-level locks.
* **Low-Stock Alert System:** Automatically trigger an admin notification when a product's `stock_qty` drops below `5`, using a partial unique index to prevent duplicate unread alerts.
* **Sales Reporting:** Multi-table SQL `JOIN` queries for revenue, order history, and product movement summaries.

## Out of Scope

To keep the architecture focused on backend data integrity, the following consumer e-commerce features are intentionally excluded:

* Public customer-facing storefront or shopping cart
* Online payment gateway integrations (e.g., Stripe, Razorpay)
* Customer product reviews, wishlists, or discount codes

## Documentation

* [Database Schema Blueprint](docs/schema.md) — Relational design for `users`, `products`, `orders`, `order_items`, and `notifications`.
* [Daily Engineering Learnings](LEARNINGS.md) — Running log of concepts and lessons learned during development.

## License

Licensed under the [MIT License](LICENSE).