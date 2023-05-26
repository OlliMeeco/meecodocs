# Keys

The Key component of our platform comprises a service for storing and retrieving encryption keys, along with a library that assists in the data encryption process. These tools are designed to enhance security and privacy in data management across different states - at rest, in transit, or during exchange. Our system works well with standard cryptography libraries, providing both flexibility and ease of integration.

## Keystore

The Keystore service is a practical tool designed to assist users and organizations in storing and retrieving encrypted keys. Its primary function is to provide convenience, ensuring that your keys are accessible wherever you need them. It simplifies the task of secure key management, making it easier to access your keys across various platforms and devices. 

## Key Hierarchy

In any secure communication system, the role of cryptographic keys is paramount. They facilitate the encryption and decryption of data, ensuring the privacy and integrity of the information exchanged. Within our system, we use a specific hierarchy of cryptographic keys, each with a distinct purpose and responsibility. The keys' functionality is designed in line with the guidelines from the National Institute of Standards and Technology (NIST), thus providing a reliable and secure architecture. Let's delve into the roles and responsibilities of these keys.

- **Master Encryption Key (MEK)**: Derived from a user-generated passphrase using a secure key derivation function, the MEK is responsible for encrypting the Key Encryption Key (KEK). It is kept solely by each user and never shared or transmitted.
- **Key Encryption Key (KEK)**: KEKs are keys that are used for encrypting and decrypting other encryption keys, except for the MEK. They play a critical role in key management processes, including secure key storage and key distribution.
- **Keypair**: Comprising a private and a public key, a keypair is used for asymmetric encryption and digital signature purposes. It controls a Decentralized Identifier (DID), with the private key kept confidential and the public key shared openly.
- **Data Encryption Key (DEK)**: DEKs are used specifically for encrypting and decrypting data. There are two types of DEKs:
  - DEK for personal data: Utilized to safeguard the user's personal data by encrypting and decrypting data exchanged with the Vault.
  - DEK for sharing data: Used for encrypting and decrypting data that is intended for sharing or exchange with another user.

In our system, we utilize a well-structured key hierarchy for effective key management and secure access to encrypted data. The hierarchy operates on a tiered principle, meaning access to encrypted data depends on obtaining each key in the sequence.

Here is how it works:

When it comes to storing data securely, the process begins with a Data Encryption Key (DEK) and a Keypair. These keys are necessary for encrypting and securing the data. Following this, a Key Encryption Key (KEK) is generated. The KEK is a new symmetric key, and its purpose is to encrypt the DEK and the private key of the Keypair.

To ensure the security of the KEK, it is further encrypted by a symmetric key known as the Master Encryption Key (MEK). The MEK is derived from the user's input via a process called a Password-Based Key Derivation Function (PBKDF2). This user input is known as the Passphrase/Password Derived Key (PDK).

In essence, this hierarchy provides a structured and secure method for managing keys and accessing encrypted data, with each key playing a critical role in the overall process.

## Cryppo

The final element of our key services is Cryppo, a library available in different languages, designed to facilitate the encryption and decryption of data. More than just a tool for data protection, Cryppo is also a crucial ally in maintaining data consistency.

Cryppo's main role is to ensure that data, when encrypted and decrypted, adheres to the format expected by the Vault. By standardizing the data structure, Cryppo ensures that all information is stored uniformly, enhancing the reliability and accessibility of the data.

Essentially, Cryppo serves as a bridge between users and the Vault, overseeing the data's secure transition while maintaining its consistency. This results in a seamless interaction with the Vault, enabling users and organizations to protect their sensitive information without compromising its availability.
