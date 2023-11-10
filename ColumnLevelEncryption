# PostgreSQL Column-Level Encryption Examples

In this document, we'll explore how to implement column-level encryption in PostgreSQL for sensitive data. We'll provide examples for encrypting and decrypting data in two scenarios: credit card numbers and email addresses.

## Example 1: Encrypting Credit Card Numbers

Let's say you have a table called `customer_data` with a column `credit_card_number` that you want to encrypt.

```sql
-- Create the table
CREATE TABLE customer_data (
    id serial PRIMARY KEY,
    name varchar(255),
    credit_card_number bytea -- Bytea type for storing encrypted data
);
