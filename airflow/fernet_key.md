# What is fernet key?

A Fernet key, in the context of Apache Airflow and other systems, is typically used for encryption and decryption purposes. 
Fernet is a symmetric key encryption algorithm, meaning the same key is used for both encrypting and decrypting data. 
Fernet is known for its simplicity, speed, and security.

In the context of Apache Airflow, the Fernet key is often used to encrypt sensitive information such as connection passwords stored in the metadata database. Here's a brief overview of how it works:

- **Key Generation:** A Fernet key is generated. 
This key is essentially a secret key that is used for both encryption and decryption.

- **Encryption:** Sensitive information, like passwords, is encrypted using the Fernet key. 
This ensures that even if someone gains unauthorized access to the metadata database, the stored passwords are not in plain text.

- **Decryption:** When the encrypted information needs to be used (for example, when connecting to a database), the Fernet key is used to decrypt the information.

- **Security:** Fernet provides a high level of security because it uses symmetric key cryptography. 
However, it's important to protect the Fernet key itself because anyone with access to the key can decrypt the sensitive information.

Example: here is a sample file named generate_key.sh

```bash
#!/usr/bin/env bash

# Generate fernet key
FERNET_KEY="$(python3 -c "from cryptography.fernet import Fernet; FERNET_KEY = Fernet.generate_key().decode(); print(FERNET_KEY)")"

# Store it in the image so that it can be used in entrypoint
echo "$FERNET_KEY" > /usr/local/etc/airflow_fernet_key
```