# Enforcing Database Security Policies with PostgreSQL Triggers

## Introduction

- Overview of database security and the importance of enforcing policies.
- Introduction to triggers as a tool for enforcing security policies in PostgreSQL.

## Data Validation

1. **Use of `BEFORE` Triggers**

   - Sample scenario: Enforcing email format validation before inserting records into a `users` table.
   
   ```sql
   CREATE OR REPLACE FUNCTION validate_email()
   RETURNS TRIGGER AS $$
   BEGIN
       IF NEW.email !~* '^[A-Za-z0-9.\%-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,4}$' THEN
           RAISE EXCEPTION 'Invalid email format';
       END IF;
       RETURN NEW;
   END;
   $$ LANGUAGE plpgsql;
   
   CREATE TRIGGER validate_email_trigger
   BEFORE INSERT OR UPDATE ON users
   FOR EACH ROW
   EXECUTE FUNCTION validate_email();
