# 🔐 AWS IAM Security Best Practices

## 📌 Project Overview
This repository focuses on implementing **AWS Identity and Access Management (IAM) security best practices** to secure cloud resources and manage access effectively.

The project demonstrates hands-on implementation of IAM users, groups, roles, policies, permission boundaries, and the **Principle of Least Privilege**. It explores identity management, secure authentication, policy evaluation, and access control to reduce security risks and build a secure AWS environment.

## 🛠️ Tools and Concepts
- **AWS Services:** IAM, Amazon EC2
- **Key Concepts:** IAM Users, Groups, Roles, Policies, Permission Boundaries, Least Privilege, Resource Tagging, Access Control

## 🚀 Implementation Steps

- Launch two EC2 instances representing Development and Production environments.
- Configure EC2 resource tags for easy identification.
- Create IAM policies to control EC2 access based on the environment.
- Create an IAM user and assign the user to the appropriate group with the required permissions.
- Test the IAM user's access to verify that permissions work as expected.

## 🏷️ Understanding Resource Tags

Tags are key-value pairs attached to AWS resources to help identify, organize, and manage them efficiently.

**My EC2 Tag Configuration:**

| Key | Value |
|---|---|
| `ENV` | `Development` |
| `ENV` | `Production` |

These tags help distinguish Development and Production instances and support environment-based access control when used with appropriate IAM policies.

## 🎯 Key Learning Outcome
Learned how to manage AWS identities, apply least-privilege permissions, organize EC2 resources using tags, and implement environment-based access control.
