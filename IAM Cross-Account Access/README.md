# Cross-Account IAM Role Architecture


##  Overview
This project demonstrates how to implement cross-account access properly, the way it's done in production environments.

This architecture demonstrates how to implement **cross-account IAM role access using AWS Security Token Service (STS)** across multiple AWS accounts within an AWS Organization.

### AWS Organization Architecture

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                           AWS ORGANIZATION                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌────────────────────────────┐       ┌────────────────────────────┐        │
│  │    SECURITY/ADMIN ACCOUNT  │       │     WORKLOAD ACCOUNT      │        │
│  │    Account: 111111111111   │       │     Account: 222222222222 │        │
│  │                            │       │                            │        │
│  │  ┌──────────────────────┐  │       │  ┌──────────────────────┐  │        │
│  │  │ IAM User / Role      │  │       │  │ Cross-Account Role   │  │        │
│  │  │ SecurityAdmin        │──┼───────┼─▶│ SecurityAuditRole    │  │        │
│  │  │                      │  │  STS  │  │                      │  │        │
│  │  │  ┌────────────────┐  │  │ Assume│  │  ┌────────────────┐  │  │        │
│  │  │  │ AssumeRole     │  │  │ Role  │  │  │ Trust Policy   │  │  │        │
│  │  │  │ Permission     │  │  │       │  │  │                │  │  │        │
│  │  │  └────────────────┘  │  │       │  │  │ Principal:     │  │  │        │
│  │  └──────────────────────┘  │       │  │  │ 111111111111   │  │  │        │
│  │                            │       │  │  │                │  │  │        │
│  │                            │       │  │  │ Conditions:    │  │  │        │
│  │                            │       │  │  │ ExternalId     │  │  │        │
│  │                            │       │  │  │ MFA Required   │  │  │        │
│  │                            │       │  │  └────────────────┘  │  │        │
│  │                            │       │  └──────────────────────┘  │        │
│  └────────────────────────────┘       └────────────────────────────┘        │
│                                                                             │
│  ┌────────────────────────────┐       ┌────────────────────────────┐        │
│  │      LOGGING ACCOUNT       │       │     DEV/TEST ACCOUNT      │        │
│  │      Account: 333333333333 │       │     Account: 444444444444 │        │
│  │                            │       │                            │        │
│  │  ┌──────────────────────┐  │       │  ┌──────────────────────┐  │        │
│  │  │ Cross-Account Role   │  │       │  │ Cross-Account Role   │  │        │
│  │  │ LogArchiveRole       │  │       │  │ DevAccessRole        │  │        │
│  │  └──────────────────────┘  │       │  └──────────────────────┘  │        │
│  └────────────────────────────┘       └────────────────────────────┘        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```
# AWS Cross-Account IAM Role Assumption Workflow

## 1. Architecture Diagram

This workflow demonstrates how a user or IAM role in the Security Account securely assumes an IAM role in the Workload Account using AWS STS.

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                      CROSS-ACCOUNT ROLE ASSUMPTION                          │
└─────────────────────────────────────────────────────────────────────────────┘

     SECURITY ACCOUNT                              WORKLOAD ACCOUNT
     ═══════════════                               ════════════════

  ┌──────────────────┐                          ┌──────────────────┐
  │  1. User/Role    │                          │                  │
  │  authenticates   │                          │                  │
  │  with MFA        │                          │                  │
  └────────┬─────────┘                          │                  │
           │                                    │                  │
           ▼                                    │                  │
  ┌──────────────────┐                          │                  │
  │  2. Calls STS    │                          │                  │
  │  AssumeRole      │─────────────────────────▶│  3. Trust Policy │
  │  with:           │                          │     Evaluated    │
  │  - Role ARN      │                          │                  │
  │  - ExternalId    │                          │  ┌────────────┐  │
  │  - Session name  │                          │  │ ✓ Account  │  │
  └────────┬─────────┘                          │  │ ✓ ExtId    │  │
           │                                    │  │ ✓ MFA      │  │
           │                                    │  └────────────┘  │
           │                                    │                  │
           │◀───────────────────────────────────│  4. Temporary    │
           │                                    │     Credentials  │
           ▼                                    │     Returned     │
  ┌──────────────────┐                          └──────────────────┘
  │  5. Use temp     │
  │  credentials     │
  │  (1 hour example)│
  └──────────────────┘
```
# A Implementation Guide

## Step 1: Set Up the Trust Relationship

The trust policy is configured inside the **Workload Account (222222222222)**.

It defines WHO is allowed to assume the IAM role.

### Trust Policy Anatomy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::111111111111:root"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "sts:ExternalId": "UniqueSecretValue"
        },
        "Bool": {
          "aws:MultiFactorAuthPresent": "true"
        }
      }
    }
  ]
}
```

### Understanding the Trust Policy

| Element | Purpose |
|---|---|
| Effect | Allows the specified action |
| Principal | Defines WHO can assume the role |
| AWS | Identifies the trusted AWS account |
| Action | Specifies `sts:AssumeRole` |
| ExternalId | Helps prevent the confused deputy problem |
| MFA | Requires MFA authentication |

**Important:** Using `arn:aws:iam::111111111111:root` as Principal delegates trust to the Security Account. It does not mean only the root user can assume the role. The source identity must also have permission to assume it.

---

## Step 2: Create the Permission Policy

Apply the principle of least privilege by granting only the permissions required.

### Least Privilege Approach

**BAD – Overprivileged Policy**

```json
{
  "Effect": "Allow",
  "Action": "ec2:*",
  "Resource": "*"
}
```

Why is this bad?

- Grants access to all EC2 actions.
- Allows unnecessary administrative operations.
- Increases the potential impact of compromised credentials.

**GOOD – Least Privilege Policy**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:DescribeVpcs",
        "ec2:DescribeSubnets"
      ],
      "Resource": "*"
    }
  ]
}
```

Why is this better?

- Grants only required read-only EC2 permissions.
- Does not allow creating or deleting EC2 resources.
- Follows the principle of least privilege.

**Note:** These EC2 Describe actions require `"Resource": "*"` because they do not support resource-level permissions.

---

## Step 3: Configure the Assuming Role

Configure this permission policy in the **Security Account (111111111111)**.

It defines WHICH destination IAM roles the source identity can assume.

### AssumeRole Permission Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": [
        "arn:aws:iam::222222222222:role/SecurityAuditRole",
        "arn:aws:iam::333333333333:role/LogArchiveRole"
      ]
    }
  ]
}
```

### Understanding the Policy

| Element | Purpose |
|---|---|
| Effect | Allows the action |
| Action | `sts:AssumeRole` |
| Resource | Specifies the destination IAM role ARNs |

**Security Best Practice:**

- Explicitly list the permitted destination role ARNs.
- Avoid using `"Resource": "*"` unnecessarily.
- Grant this policy only to authorized IAM users or roles.

---

## Step 4: Key Decisions – Why Role Assumption Over Long-Lived Credentials?

### Credentials Comparison

| Long-Lived Access Keys | Cross-Account Role Assumption |
|---|---|
| Can remain valid indefinitely until revoked or rotated | Temporary credentials expire |
| Higher exposure if leaked | Shorter credential lifetime |
| Requires manual rotation | Automatically expires |
| Requires separate MFA enforcement | MFA can be required through policy conditions |
| Difficult to manage at scale | Centralized access management |
| Requires secret storage | No permanent target-account access keys needed |

### Benefits of Cross-Account IAM Roles

1. **Temporary Credentials:** AWS STS generates temporary security credentials.
2. **Improved Security:** Reduces reliance on long-lived access keys.
3. **Centralized Access:** Manage access across multiple AWS accounts.
4. **Auditing:** CloudTrail records role assumption events.
5. **MFA Support:** Enforce additional authentication requirements.
6. **Least Privilege:** Restrict access to specific IAM roles and AWS resources.

---

## Step 5: Complete Cross-Account Access Flow

```text
SECURITY ACCOUNT (111111111111)
          |
          |
          ▼
  IAM User / IAM Role
          |
          |
          ▼
  AssumeRole Permission Policy
          |
          |
          ▼
  AWS STS AssumeRole Request
          |
          |
          ▼
WORKLOAD ACCOUNT (222222222222)
          |
          |
          ▼
  Trust Policy Evaluation
          |
          |
          ▼
  External ID + MFA Validation
          |
          |
          ▼
  Temporary Credentials Issued
          |
          |
          ▼
  Access Permitted AWS Resources
```

## Key Takeaway

Cross-account IAM role assumption provides a secure way to access resources across multiple AWS accounts.

The three important components are:

- **Trust Policy:** WHO can assume the role?
- **Permission Policy:** WHAT can the assumed role do?
- **AssumeRole Permission:** WHICH destination role can the source identity assume?

**Technologies Used:**
- AWS Organizations
- AWS IAM
- AWS STS
- Cross-Account IAM Roles
- IAM Trust Policies
- IAM Permission Policies
- MFA
- External ID
- AWS CloudTrail
