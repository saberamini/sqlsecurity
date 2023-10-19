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
2. Validating Data Formats
```sql
CREATE OR REPLACE FUNCTION validate_phone_number()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.phone_number !~* '^\d{3}-\d{3}-\d{4}$' THEN
        RAISE EXCEPTION 'Invalid phone number format';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER validate_phone_trigger
BEFORE INSERT OR UPDATE ON contacts
FOR EACH ROW
EXECUTE FUNCTION validate_phone_number();

## Range and Data Type Validation
```sql
CREATE OR REPLACE FUNCTION validate_inventory_quantity()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.quantity < 0 THEN
        RAISE EXCEPTION 'Quantity cannot be negative';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER validate_inventory_quantity_trigger
BEFORE INSERT OR UPDATE ON inventory
FOR EACH ROW
EXECUTE FUNCTION validate_inventory_quantity();

## Custom Business Rules
```sql
CREATE OR REPLACE FUNCTION check_order_credit_limit()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.order_amount > (SELECT credit_limit FROM customers WHERE id = NEW.customer_id) THEN
        RAISE EXCEPTION 'Order amount exceeds credit limit';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER check_order_credit_limit_trigger
BEFORE INSERT ON orders
FOR EACH ROW
EXECUTE FUNCTION check_order_credit_limit();


# Preventing Unauthorized Modifications
## Access Control Logic

Sample scenario: Using a trigger to block non-administrative users from updating certain records in a personnel table.
CREATE OR REPLACE FUNCTION prevent_unauthorized_update()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.role != 'admin' THEN
        RAISE EXCEPTION 'Only administrators can update personnel records';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER prevent_unauthorized_update_trigger
BEFORE UPDATE ON personnel
FOR EACH ROW
EXECUTE FUNCTION prevent_unauthorized_update();



