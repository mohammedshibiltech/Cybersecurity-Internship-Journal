# Day 05 - Encryption, TLS & Hashing Fundamentals

## Overview

Today's learning focused on the fundamentals of cryptography and secure communication. The session covered encryption concepts, symmetric and asymmetric cryptography, Transport Layer Security (TLS), cryptographic hashing, password security mechanisms, and commonly used encryption algorithms.

Understanding these concepts is essential in cybersecurity because encryption and hashing form the backbone of secure communications, authentication systems, digital certificates, and data protection mechanisms used across modern applications and cloud environments.

---

## Topics Covered

### Encryption Fundamentals

Encryption is the process of converting human-readable data (**plaintext**) into an unreadable format known as **ciphertext**.

The primary objective of encryption is to protect sensitive information from unauthorized access during storage or transmission.

Encryption requires:

- A cryptographic algorithm
- An encryption key

#### Encryption Key

An encryption key is a string of characters used by an encryption algorithm to transform plaintext into ciphertext.

#### Encryption Algorithm

A mathematical procedure that uses an encryption key to alter data into an unreadable format.

---

### Types of Encryption

#### Symmetric Encryption

Symmetric encryption uses a **single key** for both encryption and decryption.

**Characteristics:**

- Faster than asymmetric encryption
- Suitable for encrypting large amounts of data
- Requires secure key distribution

**Examples:**

- AES
- 3DES

---

#### Asymmetric Encryption

Asymmetric encryption uses **two separate keys**:

- **Public Key** → Encryption
- **Private Key** → Decryption

**Characteristics:**

- Enables secure key exchange
- More computationally intensive
- Commonly used in TLS and digital certificates

**Examples:**

- RSA
- Elliptic Curve Cryptography (ECC)

---

### Objectives of Encryption

Encryption helps ensure:

- Confidentiality
- Privacy
- Security
- Data Integrity
- Regulatory Compliance

---

### Common Encryption Algorithms

#### AES (Advanced Encryption Standard)

AES is the most widely adopted encryption standard today.

**Features:**

- Symmetric encryption algorithm
- Uses a block cipher methodology
- Divides data into fixed-size blocks
- Fast and highly secure

**Common Use Cases:**

- HTTPS communication
- VPNs
- Full-disk encryption
- Cloud storage security

---

#### 3DES (Triple Data Encryption Standard)

An enhancement of the original DES algorithm.

**Encryption Sequence (EDE):**

1. Encrypt with Key 1
2. Decrypt with Key 2
3. Encrypt with Key 3

Although stronger than DES, it is largely being replaced by AES.

---

#### SNOW

SNOW is a family of stream ciphers commonly used in telecommunications and mobile network security.

---

### Common Asymmetric Encryption Algorithms

#### RSA

RSA is one of the earliest and most widely used public-key cryptographic systems.

**Used For:**

- Secure key exchange
- Digital signatures
- TLS certificates

---

#### Elliptic Curve Cryptography (ECC)

ECC provides strong security while using smaller key sizes than RSA.

**Advantages:**

- Faster cryptographic operations
- Reduced computational overhead
- Widely used in modern TLS implementations

---

### Brute Force Attacks

A brute force attack is a method where an attacker repeatedly attempts password or key combinations until the correct one is found.

Attackers may perform millions or billions of guesses to discover the decryption key.

**Mitigation Techniques:**

- Strong passwords
- Larger key sizes
- Multi-factor authentication (MFA)
- Account lockout policies
- Rate limiting

---

## Transport Layer Security (TLS)

### What is TLS?

Transport Layer Security (TLS) is a cryptographic protocol designed to provide secure communication over networks.

TLS is primarily used to secure communication between:

- Web browsers and web servers
- Applications and APIs
- Email services
- Cloud platforms

**Current Standard:** TLS 1.3

---

### HTTPS and TLS

HTTPS is HTTP running over TLS.

TLS encrypts network traffic while HTTPS applies TLS protection to web communications.

---

### Core Functions of TLS

#### Encryption

Protects transmitted data from unauthorized access.

#### Authentication

Verifies the identity of communicating parties using digital certificates.

#### Integrity

Ensures that transmitted data has not been modified during transmission.

---

### TLS Certificates

A TLS certificate is issued by a trusted Certificate Authority (CA).

The certificate contains:

- Domain information
- Organization details
- Public key
- Certificate validity information

Certificates are installed on servers to enable secure HTTPS communication.

---

### TLS Handshake Process

Before encrypted communication begins, a TLS handshake is performed.

**Handshake Steps:**

1. Client and server agree on a TLS version.
2. A cipher suite is selected.
3. The server presents its TLS certificate.
4. Certificate authenticity is verified.
5. Session keys are generated.
6. Secure communication begins.

---

## Hashing Fundamentals

### What is Hashing?

Hashing converts data into a fixed-length value known as a **hash** or **digest**.

Unlike encryption, hashing is a **one-way process**, meaning the original data cannot be recovered from the hash value.

---

### Characteristics of Hashing

- Fixed output length
- Deterministic results
- One-way operation
- Efficient integrity verification

---

### Encryption vs Hashing

| Encryption | Hashing |
|------------|----------|
| Two-way process | One-way process |
| Can be decrypted | Cannot be reversed |
| Uses encryption keys | Does not require decryption keys |
| Protects confidentiality | Verifies integrity |

---

### File Integrity Verification

Hashing can be used to verify whether a file has been altered.

If even a single bit changes:

- The hash value changes
- Integrity verification fails

**Applications:**

- Software verification
- Malware analysis
- Digital forensics
- File integrity monitoring

---

### Password Storage and Salting

Modern systems do not store passwords in plaintext.

Typical workflow:

1. Password is entered
2. Salt is added
3. Password is hashed
4. Hash is stored

During login:

1. Password is entered
2. Same salt is applied
3. New hash is generated
4. Hashes are compared

---

### Salting

Salting is the process of adding a unique random value to data before hashing.

**Benefits:**

- Prevents rainbow table attacks
- Produces unique hashes for identical passwords
- Strengthens password security

---

## Common Hashing Algorithms

### MD5 (Message Digest 5)

- Produces a 128-bit hash
- Fast computation
- Commonly used for file integrity verification

> Note: MD5 is no longer considered secure for cryptographic purposes.

---

### SHA-1 (Secure Hash Algorithm 1)

- Produces a 160-bit hash

> Note: SHA-1 is deprecated due to known vulnerabilities.

---

### SHA-2

Includes:

- SHA-256
- SHA-512

Widely used in modern security systems for:

- TLS
- Digital certificates
- Digital signatures
- Blockchain technologies

---

### SHA-3

The latest member of the SHA family.

Designed as an alternative hashing standard and serves as a fallback should vulnerabilities be discovered in SHA-2.

---

## Key Takeaways

- Encryption protects confidentiality by converting plaintext into ciphertext.
- Symmetric encryption uses a shared key, while asymmetric encryption uses public/private key pairs.
- TLS secures internet communications through encryption, authentication, and integrity verification.
- HTTPS is implemented using TLS.
- Hashing is a one-way function used for integrity verification and secure password storage.
- Salting strengthens password security by generating unique hashes.
- AES, RSA, ECC, TLS, and SHA-256 are among the most important technologies used in modern cybersecurity.

---

## Skills Gained

- Understanding cryptographic principles
- Differentiating symmetric and asymmetric encryption
- Understanding TLS architecture and handshake mechanisms
- Hashing and integrity verification concepts
- Password security and salting techniques
- Familiarity with common encryption and hashing algorithms

---

### Reflection

Today's session strengthened my understanding of how modern systems protect data during storage and transmission. Learning the relationship between encryption, hashing, TLS, certificates, and authentication mechanisms provided valuable insight into the security controls that underpin web applications, cloud services, and enterprise environments. These concepts form a critical foundation for future work in Vulnerability Assessment and Penetration Testing (VAPT), Cloud Security, and Security Architecture.