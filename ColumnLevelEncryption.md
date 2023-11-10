# PostgreSQL Column-Level Encryption Examples

In this document, we'll explore how to implement column-level encryption in PostgreSQL for sensitive data. We'll provide examples for encrypting and decrypting data in two scenarios: credit card numbers and email addresses.
## Initial setup - pgcrypto extension

The "pgcrypto" extension is a popular extension in PostgreSQL that provides cryptographic functions and capabilities. It includes functions for performing various encryption and decryption operations, hashing, and more. It's commonly used when you need to work with encrypted data or perform cryptographic operations within the database.

Generally the module is available by default in any Postgre
```sql
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
Now, let's insert an encrypted credit card number into the table.  Note that encryption_key can be anything you like, any name suffices.  Where these keys are saved is very important and this will be discussed separately.

```sql
-- Insert encrypted credit card number
INSERT INTO customer_data (name, credit_card_number)
VALUES ('John Doe', pgp_sym_encrypt('1234-5678-9012-3456', 'encryption_key'));
```
If you now try to read the data, the credit card number column will be decrypted

```sql
SELECT * FROM customer_data;
```

To decrypt the credit card number when retrieving it, you can use the pgp_sym_decrypt function:

```sql
-- Retrieve and decrypt the credit card number
SELECT name, pgp_sym_decrypt(credit_card_number, 'encryption_key') AS decrypted_credit_card
FROM customer_data;
```
## Example 1: Encrypting Email Addresses
Suppose you have a table called `user_info` with an `email` column that you want to encrypt.
```sql
-- Create the table
CREATE TABLE user_info (
    id serial PRIMARY KEY,
    username varchar(255),
    email bytea -- Bytea type for storing encrypted data
);
```
Inserting an encrypted email address:
```sql
-- Insert encrypted email address
INSERT INTO user_info (username, email)
VALUES ('alice', pgp_sym_encrypt('alice@example.com', 'encryption_key'));
```
To retrieve and decrypt the email address:

```sql
-- Retrieve and decrypt the email address
SELECT username, pgp_sym_decrypt(email, 'encryption_key') AS decrypted_email
FROM user_info;
```



