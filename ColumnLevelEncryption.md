# PostgreSQL Column-Level Encryption Examples

In this document, we'll explore how to implement column-level encryption in PostgreSQL for sensitive data. We'll provide examples for encrypting and decrypting data in two scenarios: credit card numbers and email addresses.
## Initial setup - pgcrypto extension

The "pgcrypto" extension is a popular extension in PostgreSQL that provides cryptographic functions and capabilities. It includes functions for performing various encryption and decryption operations, hashing, and more. It's commonly used when you need to work with encrypted data or perform cryptographic operations within the database.

Generally the module is available by default in any Postgre
```sql
-- Create the table
CREATE EXTENSION IF NOT EXISTS pgcrypto;
```
Once the extension is created, you can use its functions, such as pgp_sym_encrypt and pgp_sym_decrypt, to perform cryptographic operations within your PostgreSQL database.

## Example 1: Encrypting Credit Card Numbers

Let's say you have a table called `customer_data` with a column `credit_card_number` that you want to encrypt.

```sql
-- Create the table
CREATE TABLE customer_data (
    id serial PRIMARY KEY,
    name varchar(255),
    credit_card_number bytea -- Bytea type for storing encrypted data
);
```
