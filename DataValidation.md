# Enforcing Database Security Policies with PostgreSQL Triggers

## Introduction
Triggers are very important part of PL/SQL and can do many things.  In this section, we look at how to use triggers for various uses as part of a database management.


## Data Validation
Data validation involves the process of verifying that data conforms to a set of predefined rules or criteria before it is accepted into a database. This validation ensures that the data is accurate, consistent, and reliable for use in applications and reporting. Data validation is crucial for maintaining data integrity, preventing errors, and enhancing the overall quality of a database.

1. **Use of `BEFORE` Triggers**

   Sample scenario: Enforcing email format validation before inserting records into a `users` table.
   
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

Testing the trigger
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(255) NOT NULL
);
-- a correct insertion
INSERT INTO users (name, email) VALUES ('John Doe', 'john.doe@example.com');
-- A wrong insertion
INSERT INTO users (name, email) VALUES ('Jane Doe', 'jane.doe@');

2. Validating Data Formats

Sample scenario: Ensuring that phone numbers in a contacts table follow a specific format.

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
```
3. Range and Data Type Validation

Sample scenario: Preventing the insertion of negative quantities in an inventory table.

~~~sql
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
~~~
4. Custom Business Rules

Sample scenario: Implementing a custom rule to restrict order amounts based on customer credit limits.

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

```
## Preventing Unauthorized Modifications
1.0 Access Control Logic

Sample scenario: Using a trigger to block non-administrative users from updating certain records in a personnel table.
```sql
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
```
2.0 Row-Level Security

Sample scenario: Implementing row-level security to restrict access to sensitive HR data.


```sql
CREATE OR REPLACE FUNCTION hr_security_policy()
RETURNS TRIGGER AS $$
BEGIN
    IF (SELECT role FROM users WHERE id = current_user_id()) != 'hr' THEN
        RAISE EXCEPTION 'Access denied to HR data';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE POLICY hr_security ON employee_data
FOR SELECT USING (hr_security_policy());
```
3.0 Auditing Unauthorized Access

Sample scenario: Logging unauthorized access attempts to a login_attempts table.

```sql
CREATE OR REPLACE FUNCTION log_login_attempt()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO login_attempts (username, timestamp)
    VALUES (NEW.username, NOW());
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER log_login_attempt_trigger
BEFORE INSERT ON login_attempts
FOR EACH ROW
EXECUTE FUNCTION log_login_attempt();
```
## Data Masking and Redaction
1.0 Masking Sensitive Data

Sample scenario: Masking credit card numbers in a transactions table for non-administrative users.

```sql
CREATE OR REPLACE FUNCTION mask_credit_card()
RETURNS TRIGGER AS $$
BEGIN
    NEW.credit_card_number = 'XXXX-XXXX-XXXX-' || RIGHT(NEW.credit_card_number, 4);
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER mask_credit_card_trigger
BEFORE INSERT OR UPDATE ON transactions
FOR EACH ROW
EXECUTE FUNCTION mask_credit_card();
```

2.0 Redaction of PII

Sample scenario: Redacting personally identifiable information (PII) in a customer_info table.

```sql
CREATE OR REPLACE FUNCTION redact_pii()
RETURNS TRIGGER AS $$
BEGIN
    NEW.first_name = 'Redacted';
    NEW.last_name = 'Redacted';
    NEW.email = 'Redacted';
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER redact_pii_trigger
BEFORE INSERT OR UPDATE ON customer_info
FOR EACH ROW
EXECUTE FUNCTION redact_pii();
```
# Customized Security Policies
1.0 Dynamic Policies

Sample scenario: Implementing dynamic security policies that restrict access to records based on their status.

```sql
CREATE OR REPLACE FUNCTION dynamic_security_policy()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.status = 'archived' AND current_user_id() != NEW.owner_id THEN
        RAISE EXCEPTION 'Access denied to archived records';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER dynamic_security_policy_trigger
BEFORE SELECT ON records
FOR EACH ROW
EXECUTE FUNCTION dynamic_security_policy();
```
2.0 Custom Authentication

Sample scenario: Using a trigger to implement custom authentication logic for specific user groups.
```sql
CREATE OR REPLACE FUNCTION custom_authentication()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.user_group = 'special' AND NEW.custom_token != 'secret' THEN
        RAISE EXCEPTION 'Authentication failed for special user group';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER custom_authentication_trigger
BEFORE INSERT ON users
FOR EACH ROW
EXECUTE FUNCTION custom_authentication();
```

# Alerting and Reporting
1.0 Security Violation Alerts

Sample scenario: Creating alerts and notifications when a trigger detects a security violation.
```sql
CREATE OR REPLACE FUNCTION security_violation_alert()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO security_alerts (event, timestamp)
    VALUES ('Security Violation', NOW());
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER security_violation_alert_trigger
AFTER INSERT ON security_logs
FOR EACH ROW
EXECUTE FUNCTION security_violation_alert();
```


