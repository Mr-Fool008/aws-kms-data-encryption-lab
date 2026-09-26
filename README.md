# AWS KMS Data Encryption Lab

## Project Overview

This project demonstrates how to protect sensitive data at rest using **AWS Key Management Service (KMS)**, **Amazon DynamoDB**, and **AWS Identity and Access Management (IAM)**.

The goal of the lab was to:

- Create a **customer-managed KMS key**
- Use that key to encrypt a DynamoDB table
- Create a test IAM user
- Validate that a user with DynamoDB access but without KMS decrypt permissions cannot read the encrypted data
- Grant KMS key usage permissions and confirm that access is restored

This lab helped me understand how encryption and access control work together in AWS.

---

## AWS Services Used

- **AWS Key Management Service (KMS)**
- **Amazon DynamoDB**
- **AWS Identity and Access Management (IAM)**

---

## Key Concepts

### Encryption

Encryption converts readable data into a protected format so that unauthorized users cannot read it.

Encryption keys control how data is encrypted and decrypted.

In this project, I used a **symmetric KMS key**, meaning the same key is used for encryption and decryption.

---

## Creating the KMS Key

I created a **customer-managed AWS KMS key** for the lab.

A customer-managed key gives more control and visibility than AWS-owned or AWS-managed keys because the customer controls the key policy and who is allowed to use the key.

The key was then used to protect data stored in DynamoDB.

---

## Encrypting the DynamoDB Table

I configured a DynamoDB table to use the customer-managed KMS key for encryption at rest.

DynamoDB supports multiple encryption options, including:

- AWS-owned keys
- AWS-managed keys
- Customer-managed keys

For this lab, I selected the **customer-managed key** option so I could directly control the KMS permissions associated with the table.

---

## Understanding Data Visibility

Even though the DynamoDB table was encrypted, I could still view the table data while using an authorized account.

This demonstrated **transparent encryption and decryption**.

DynamoDB handles the encryption and decryption process automatically when the requesting identity has the required permissions.

The important lesson was that having access to the DynamoDB table alone is not always enough. The user also needs permission to perform the required KMS actions.

---

## Testing Access with an IAM User

To test the security controls, I created a separate IAM user.

The test user had access to DynamoDB but did **not** initially have permission to use the KMS key for decryption.

When I attempted to access the encrypted DynamoDB data using this user, AWS returned an **Access Denied** error.

This confirmed that DynamoDB permissions and KMS permissions work together.

A user can have access to the database resource while still being unable to decrypt the protected data.

---

## Granting KMS Access

Next, I updated the KMS key permissions and added the test user as a key user.

This allowed the user to perform actions including encryption and decryption with the key.

After updating the permissions, I retried access to the DynamoDB table.

The test user could now view the encrypted data successfully.

This validated that KMS key permissions were the control preventing access during the earlier test.

---

## Access-Control Flow

```text
Test IAM User
      |
      | DynamoDB permissions
      v
DynamoDB Table
      |
      | encrypted with
      v
Customer-Managed KMS Key
      |
      | KMS decrypt permission?
      |
   +--+--+
   |     |
  No    Yes
   |     |
   v     v
Denied  Data Visible
```

---

## Security Takeaways

This project reinforced several important cloud-security concepts:

- **Encryption protects data at rest**
- **Resource access and data decryption are separate controls**
- A user can have access to a DynamoDB table but still be blocked from reading encrypted data if they lack the required KMS permissions
- KMS policies control what actions identities can perform with a key
- Customer-managed keys provide more control over permissions and key usage
- Encryption can be combined with IAM and other AWS security controls to create multiple layers of protection

---

## What I Learned

The most important lesson from this lab was understanding the difference between **accessing a resource** and **being authorized to decrypt the data stored in that resource**.

Before completing the lab, I thought of encryption mostly as a feature that simply protects a service or database.

This project showed me that KMS works as a separate security layer. IAM permissions can control access to the AWS resource, while KMS permissions determine whether an identity is authorized to use the encryption key.

That separation is important when designing secure cloud environments.

---

## Project Result

By the end of the lab, I successfully:

- Created a customer-managed AWS KMS key
- Used the key to encrypt a DynamoDB table
- Created a separate IAM test user
- Verified that DynamoDB access without KMS decrypt permission resulted in an access-denied error
- Updated the KMS key permissions
- Confirmed that authorized access to the encrypted data was restored

---

## Future Improvements

Possible next steps for this lab include:

- Enabling and reviewing AWS CloudTrail logs for KMS activity
- Testing least-privilege KMS policies
- Comparing customer-managed keys with AWS-managed keys
- Testing key rotation
- Adding monitoring and alerting around KMS key usage
