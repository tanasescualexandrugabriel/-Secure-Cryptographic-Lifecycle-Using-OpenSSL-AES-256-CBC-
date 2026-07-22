# Implementation of an End-to-End Symmetric Cryptographic Pipeline Using OpenSSL (AES-256-CBC)

This repository serves as a practical, step-by-step demonstration of implementing standard cryptographic operations to protect data at rest. The workflow establishes a comprehensive symmetric encryption lifecycle, highlighting the high risks associated with plaintext data exposure and demonstrating the deployment of Advanced Encryption Standard (AES) in Cipher Block Chaining (CBC) mode using the OpenSSL engine.

---

🔑### 📂 Step 1: Enumeration and Assessment of Plaintext Vulnerabilities 🔑

This initial phase focuses on discovering and evaluating an insecure data file (`userdetails`) residing on a target environment. Accessing the raw storage parameters demonstrates that high-value system assets—such as administrative credentials, usernames, contact records, and corporate emails—are actively maintained without any access controls or cryptographic protection.

In modern cybersecurity architectures, this structure represents a catastrophic weakness. Any internal adversary, misconfigured process, or lateral movement within the network grants an attacker immediate visibility into the credentials, allowing further authentication bypasses without the need to crack or reverse complex structures.

🔑```bash
echo -e "Username: admin\nPassword: P@ssw0rd123\nEmail: admin@example.com\nPhone: 123-456-7890" > userdetails
cat userdetails 
```🔑

#### Technical Reference Diagram
![Plaintext Exposure and Vulnerability Assessment](images/photo1.png)

---

### 🔑Step 2: Generation of a Cryptographically Secure Symmetric Secret 

To correct the vulnerability that I've identified in the previous step, a trusted cryptographic primitive must be generated. Utilizing the OpenSSL pseudo-random number generator wrapper (`openssl rand`), a high-entropy 256-bit (32-byte) secret is derived. The resulting key material is encoded using Base64 format to guarantee architectural compatibility and safe handling across different text formats, before being piped directly into a dedicated key storage file (`passkey`).

System directory inspection is subsequently performed to guarantee that the operating system has committed the secret to the storage medium with the exact expected byte configuration and file system metadata.

🔑```bash
openssl rand -base64 32 > passkey && ls -la passkey 
```🔑

#### Technical Reference Diagram
![Cryptographic Key Material Generation](images/photo2.png)

---

### 🔒 Step 3: Deployment of Block-Cipher Encryption via AES-256-CBC 

The unprotected asset is passed through the standard OpenSSL encryption architecture, transforming the raw data into an obfuscated binary block layout. This phase enforces the deployment of the AES cipher with a 256-bit key requirement operating within the Cipher Block Chaining (CBC) block mode, utilizing the previously compiled `passkey` file as the secure password source.

Crucially, the encryption process invokes the `-salt` security flag. Incorporating an explicit salt ensures that a random byte sequence is combined with the passphrase derivation mechanism. This behavior guarantees that identical data inputs always produce entirely separate, non-deterministic ciphertexts across multiple executions, effectively disabling structural mapping or pre-computed Rainbow Table correlation attacks.

🔑```bash
openssl enc -aes-256-cbc -salt -in userdetails -out userdetails_encrypt -pass file:./passkey && ls -la userdetails_encrypt 
```🔑

#### Technical Reference Diagram
![Symmetric Encryption Engine Execution](images/photo3.png)

---

### 👁️ Step 4: Structural Inspection and Entropy Validation of the Ciphertext 

An explicit diagnostic read operation is performed against the newly compiled binary asset (`userdetails_encrypt`) to evaluate the overall effectiveness of the encryption engine. The system terminal returns a heavily randomized, unreadable stream composed of non-ASCII indicators and binary symbols, safely prefixed by the standardized `Salted__` magic header signature.

This specific output successfully indicates that high-entropy data scrambling has taken place. The configuration proves that even if the storage layer is compromised or exfiltrated by a malicious actor, the absolute confidentiality of the operational data remains entirely protected against unauthorized reverse engineering.

🔑```bash
cat userdetails_encrypt
```🔑

#### Technical Reference Diagram
![High-Entropy Ciphertext State Verification](images/photo4.png)

---

### 🔓 Step 5: Invoking Authorized Decryption and Target Payload Restoration 

To establish the feasibility of legitimate data recovery, the symmetric process is executed in reverse. By initializing the OpenSSL engine with the explicit decryption flag (`-d`), pointing to the encrypted data block, and feeding the exact matching symmetric key from the `passkey` asset, the system reconstructs the ciphertext back into its aligned state.

The restored content is immediately piped into an independent output file named `userdetails_decrypt`. A fast directory evaluation verifies the runtime generation of the decrypted file alongside its final size allocation metrics on disk.

🔑```bash
openssl enc -d -aes-256-cbc -in userdetails_encrypt -out userdetails_decrypt -pass file:./passkey && ls -la userdetails_decrypt
```🔑

#### Technical Reference Diagram
![Inversed Cryptographic Decryption Engine](images/photo5.png)

---

### 🎯 Step 6: Cryptographic Integrity Validation and Parity Checks 

The final phase demands a definitive assessment to prove that no byte corruption, alignment truncations, or formatting damages occurred during the symmetric lifecycle. Interrogating the `userdetails_decrypt` file shows a perfect, byte-for-byte matching sequence compared to the raw infrastructure parameters generated at the beginning of the workflow.

This successful match simultaneously validates both the data confidentiality and data integrity layers of the execution, confirming that the symmetric architecture securely processed, held, and restored the original assets with zero loss or disruption.

🔑```bash
cat userdetails_decrypt
```🔑

#### Technical Reference Diagram
![Final Data Integrity and Parity Check](images/photo6.png)
