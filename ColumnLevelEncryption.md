# PostgreSQL Column-Level Encryption Examples

In this document, we'll explore how to implement column-level encryption in PostgreSQL for sensitive data. We'll provide examples for encrypting and decrypting data in two scenarios: credit card numbers and email addresses.
## Initial setup - pgcrypto extension

The "pgcrypto" extension is a popular extension in PostgreSQL that provides cryptographic functions and capabilities. It includes functions for performing various encryption and decryption operations, hashing, and more. It's commonly used when you need to work with encrypted data or perform cryptographic operations within the database.

Generally the module is available by default in any Postgre
```sql
CREATE EXTENSION IF NOT EXISTS pgcrypto;
```
Once the extension is created, you can use its functions, such as pgp_sym_encrypt and pgp_sym_decrypt, to perform cryptographic operations within your PostgreSQL database.

## Example 2: Encrypting Credit Card Numbers

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
## Using triggers
Often it is better to hide the direct callling of the ecnryption and decryption functions and use triggers to implement them.
Suppose you have a table named sensitive_data with columns id, name, and credit_card_number. You want to automatically encrypt the credit_card_number column before inserting records and decrypt it when retrieving records.

1. ** Create the sensitive data **

```sql
CREATE TABLE sensitive_data (
    id serial PRIMARY KEY,
    name varchar(255),
    credit_card_number bytea
);
```

2. ** Create a trigger function to encrypt data before insertion.*

 This function will be called before an INSERT operation on the sensitive_data table:

```sql
CREATE OR REPLACE FUNCTION encrypt_credit_card()
RETURNS TRIGGER AS $$
BEGIN
    NEW.credit_card_number := pgp_sym_encrypt(NEW.credit_card_number::text, 'encryption_key');
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

3. Create a BEFORE INSERT trigger that invokes the encrypt_credit_card function:
```sql
CREATE TRIGGER encrypt_credit_card_trigger
BEFORE INSERT ON sensitive_data
FOR EACH ROW
EXECUTE FUNCTION encrypt_credit_card();
```
This trigger ensures that the credit_card_number is encrypted before it is inserted into the sensitive_data table.

4. Create a trigger function to decrypt data after retrieval. This function will be called after a SELECT operation on the sensitive_data table:

```sql
CREATE OR REPLACE FUNCTION decrypt_credit_card()
RETURNS TRIGGER AS $$
BEGIN
    NEW.credit_card_number := pgp_sym_decrypt(NEW.credit_card_number, 'encryption_key');
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```
5. Create an AFTER SELECT trigger that invokes the decrypt_credit_card function:
```sql
CREATE TRIGGER decrypt_credit_card_trigger
AFTER SELECT ON sensitive_data
FOR EACH ROW
EXECUTE FUNCTION decrypt_credit_card();
```
This trigger ensures that the credit_card_number is decrypted after it is retrieved from the sensitive_data table

