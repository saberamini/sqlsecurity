# Asymmetric Encryption in PostgreSQL using pgcrypto

In PostgreSQL, you can perform asymmetric encryption using the `pgcrypto` extension. Asymmetric encryption, also known as public-key cryptography, involves a pair of keys: a public key for encryption and a private key for decryption. This allows you to securely exchange information or verify the authenticity of data.

## Getting Started

1. **Generate Key Pairs**:
   - Generate a key pair (public and private keys) using external tools like OpenSSL or built-in functions in your preferred programming language.
   - Keep the private key confidential and securely manage it.
   - Share the public key with entities that need to encrypt data for you.

2. **Import Public Key**:
   - Import the public key into PostgreSQL using the `pgp_pub_encrypt` function.

```sql
   -- Import public key
   SELECT pgp_pub_encrypt('-----BEGIN PGP PUBLIC KEY BLOCK-----
   ...
   -----END PGP PUBLIC KEY BLOCK-----', 'key_name');
```
## Encrypting Data
1. **Encrypt Data with the Public Key**:
   - Encrypt data using the public key. The data can only be decrypted with the corresponding private key.
```sql
-- Encrypt data with the public key
SELECT pgp_pub_encrypt('Sensitive data', 'key_name');
```

## Decrypting Data
1. **Decrypt Data with the Private Key:**
- Decrypting data encrypted with the public key requires the private key and the associated passphrase.
```sql
-- Decrypt data with the private key
SELECT pgp_priv_decrypt(encrypted_data, 'private_key_passphrase') AS decrypted_data;
```

