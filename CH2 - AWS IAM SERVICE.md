# AWS IAM & CLI Essentials (Exam Focused)

IAM is a **global service** — configurations apply across all regions.

---

## 🧱 IAM Core Concepts

| Concept      | Description                                                              | Exam Tips                                                       |
|--------------|--------------------------------------------------------------------------|-----------------------------------------------------------------|
| Root Account | Super-admin account. Avoid daily use. Enable MFA and delete unused keys. | ✅ Always enable MFA for root                                    |
| IAM Users    | Human or app entities. Created with no permissions by default.           | ✅ Never share credentials; use roles for cross-account access   |
| Groups       | Collection of users with common permissions (e.g., Admins, Developers).  | ✅ Groups ≠ Roles; only users in groups                          |
| Roles        | Provide temporary credentials to AWS services, cross-account, EC2, etc.  | ✅ Used by services, auto-rotate creds, no long-term access keys |

---

## 🔐 IAM Policies

IAM Policies are written in JSON. Key structure:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*",
      "Condition": {
        "IpAddress": {
          "aws:SourceIp": "192.0.2.0/24"
        }
      }
    }
  ]
}
```

### Types of Policies

- **Managed Policies**: AWS-predefined (e.g., `AmazonS3ReadOnlyAccess`)
- **Inline Policies**: Attached directly to one user/group/role (not reusable)
- **Evaluation Logic**:
    - `Explicit Deny` overrides `Allow`
    - No permission = Implicit Deny

---

## 🛡️ Security Tools & Best Practices

| Tool/Feature      | Purpose                                                          | CLI Command                              |
|-------------------|------------------------------------------------------------------|------------------------------------------|
| Credential Report | List all users + MFA/keys usage status                           | `aws iam generate-credential-report`     |
| Access Advisor    | Shows service usage + last access info                           | View in AWS Console only                 |
| Password Policy   | Enforce complexity rules and rotation                            | `aws iam update-account-password-policy` |
| Account Alias     | Custom login URL for AWS Console                                 | `aws iam create-account-alias`           |
| MFA               | Use for privileged users (virtual/device MFA preferred over SMS) | N/A                                      |

---

## 🔐 Access Methods & Security Notes

| Method      | Use Case           | Security Tip                                             |
|-------------|--------------------|----------------------------------------------------------|
| Console     | Human login        | Use strong password + MFA                                |
| CLI         | Scripts/Automation | Use `aws configure`; rotate keys every 90 days           |
| SDK         | App integration    | Use IAM roles (EC2/Lambda); avoid hardcoding credentials |
| CloudShell  | In-browser CLI     | Auto-authenticated with temp credentials                 |
| Access Keys | For CLI/SDK only   | ⚠️ Never use with root; delete unused keys               |

---

## 🛡️ Shared Responsibility Model

| AWS Responsibility                            | Your Responsibility                                   |
|-----------------------------------------------|-------------------------------------------------------|
| Infra security (Regions, AZs, Edge Locations) | IAM users, groups, and roles                          |
| IAM service uptime                            | Permission policies (Least Privilege Principle)       |
| Global infra replication                      | Credential rotation, MFA enforcement                  |
| Policy syntax/validation                      | Logging & monitoring (CloudTrail, credential reports) |

---

## ⚙️ AWS CLI Pro Tips

# Assume IAM Role (for cross-account access)
```bash
aws sts assume-role \
  --role-arn arn:aws:iam::123456789012:role/DevRole \
  --role-session-name "dev-session"
```
# Rotate access keys
```bash
aws iam create-access-key --user-name rajan 
aws iam delete-access-key --user-name rajan --access-key-id OLD_KEY_ID
```
# Check effective permissions
```bash
aws iam simulate-principal-policy \
  --policy-source-arn arn:aws:iam::123456789012:user/rajan \
  --action-names "s3:GetObject"
```
# 🧠Exam Cheat Sheet
🔑 Access Keys: Only for CLI/SDK. Never create for root user.

⚠️ Inline Policies: Avoid if possible; use managed policies for easier auditing.

🔄 Rotation: Roles rotate automatically; access keys must be rotated manually.

📜 Policy Structure:

Version → Statement → Effect, Action, Resource, Condition

🔍 Security Tools: Use in order → Credential Report → Access Advisor → CloudTrail

✅ Real-World Tip: Use profiles to switch accounts/environments:
```bash
aws configure --profile dev
```
