# S3 Encryption

## Topics Covered
- Why S3 Encryption Is Needed
- What S3 Encryption Actually Does
- Real Reasons Encryption Exists
- Encryption In Transit vs At Rest
- Understanding Keys in Encryption
- Bucket Default Encryption vs Object Encryption
- How Encryption Applies to New vs Existing Objects
- Types of S3 Encryption
  - SSE-S3
  - SSE-KMS
  - SSE-C
  - Client-Side Encryption
- Key Ownership and AWS Plaintext Visibility
- Changing Bucket Encryption and Its Impact
- Summary Table

---

## 1. Why S3 Encryption Is Needed

Encryption is needed to protect the confidentiality of data stored in Amazon S3.  
Without encryption, if someone gains access to the storage hardware, backups, snapshots, or intercepts data during upload/download, they can read the data.

Encryption ensures that sensitive data remains unreadable to unauthorized parties, even if they access the underlying storage.

---

## 2. What S3 Encryption Actually Does

Encryption converts readable data (plaintext) into unreadable data (ciphertext).  
Only someone with the correct key can decrypt it back to its original form.

Example:
- Input: image.png  
- Encrypted stored version: random binary ciphertext  
- Without the key, the object is useless.

---

## 3. Real Reasons Encryption Exists

1. Protection if AWS storage hardware is compromised.  
2. Prevent unauthorized internal access inside AWS.  
3. Compliance requirements (PCI-DSS, HIPAA, SOC2).  
4. Defense against IAM misconfigurations.  
5. Protection during network transfer (if using client-side or HTTPS).  
6. Zero-trust security models.

---

## 4. Encryption In Transit vs At Rest

Two entirely different layers:

### Encryption in Transit
- Protects data while it travels over the network.
- Achieved using HTTPS/TLS.
- Not related to SSE-S3, SSE-KMS, SSE-C.

### Encryption At Rest
- Protects data stored on AWS disks.
- Achieved using SSE-S3, SSE-KMS, SSE-C, or client-side encryption.
- This is what S3 "encryption" usually refers to.

---

## 5. Understanding Keys in Encryption

Every encryption method uses a key.

The key controls:
- How data is encrypted
- How data is decrypted

The different types of S3 encryption exist because different people want to manage the key differently:
- AWS manages the key
- AWS KMS manages the key
- Customer provides the key
- Customer keeps all keys and AWS never sees plaintext

---

## 6. Bucket Default Encryption vs Object Encryption

These two concepts must be clearly separated.

### Bucket Default Encryption
- A bucket-level setting.
- Forces all future uploads to use a chosen encryption type.
- Does not modify existing objects.
- Not a bucket policy.  
- Not JSON.  
- Not permissions.  
It is simply a default rule for encryption.

### Object Encryption
- Actual encryption applied to the data.
- Each object stores its own encryption metadata.
- Only occurs when uploading or rewriting the object.

---

## 7. How Encryption Applies to New vs Existing Objects

### Existing Objects
- Keep their original encryption permanently.
- Changing bucket default encryption does not modify them.
- To update encryption, the object must be re-uploaded or rewritten.

### New Objects
- Always follow the latest bucket default encryption configuration.
- If the bucket defaults are changed, all future uploads use the new method.

---

## 8. Types of S3 Encryption

### 8.1 SSE-S3 (Server-Side Encryption with S3-Managed Keys)
- Key managed by: AWS S3
- AWS sees plaintext: Yes
- Cost: Free
- Used for: Basic encryption needs
- Header: AES256

### 8.2 SSE-KMS (Server-Side Encryption with AWS KMS Keys)
- Key managed by: AWS KMS or customer-managed KMS key
- AWS sees plaintext: Yes
- Cost: KMS API calls
- Used for: Compliance, auditing, granular access control
- Header: aws:kms

### 8.3 SSE-C (Server-Side Encryption with Customer-Provided Keys)
- Key managed by: Customer
- AWS sees plaintext: Yes
- Customer must provide the key on every request
- AWS never stores the key
- Used for: BYOK requirements, on-prem key management

### 8.4 Client-Side Encryption
- Key managed by: Customer completely
- AWS sees plaintext: No
- Data is encrypted before upload and decrypted after download
- Used for: Zero-trust, highly sensitive workloads

---

## 9. Key Ownership and AWS Plaintext Visibility

| Encryption Type | Who Manages the Key | AWS Sees Plaintext | Use Case |
|-----------------|---------------------|---------------------|----------|
| SSE-S3 | AWS S3 | Yes | Basic encryption |
| SSE-KMS | AWS KMS or customer KMS key | Yes | Compliance, auditing |
| SSE-C | Customer | Yes | BYOK, strict key control |
| Client-Side | Customer | No | Maximum security, zero trust |

---

## 10. Changing Bucket Encryption and Its Impact

Example scenario:

1. Bucket default encryption = SSE-KMS  
   Uploaded objects → encrypted with SSE-KMS  
2. Bucket default encryption changed to SSE-C  
   New uploads → encrypted with SSE-C  
3. Old objects remain encrypted with SSE-KMS  
   They do not change automatically  
4. Only rewriting or re-uploading the object applies the new encryption

---

## 11. Summary

- Bucket default encryption is a rule for future uploads.
- Object encryption is the actual encryption.
- Changing bucket encryption does not modify existing objects.
- Different S3 encryption types exist because key ownership changes.
- Client-side encryption is the only method where AWS never sees plaintext.
- SSE-KMS is used in most real-world production environments due to audit logging and key control.

---
