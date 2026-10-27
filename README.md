# 🔐 AWS IAM Security 

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

<img width="1876" height="295" alt="Screenshot 2026-10-25 103200" src="https://github.com/user-attachments/assets/53923abf-05ff-49c3-9608-deba27fb8d1e" />


## IAM Policies

### What I did in this step

In this step, wiil create an IAM policy that gives access to the development instance

### Understanding IAM policies

IAM policy is a rule for who can do what with your AWS resources. It's all about giving permissions to IAM users, groups, or roles, saying what they can or can't do on certain resources

### The policy I set up

For this project, I’ve set up a policy using JSON method.You can create and edit AWS policies in the visual editor or JSON.

### Policy effect

I’ve created a policy  allows some actions (like starting, stopping, and describing EC2 instances) for instances tagged with "Env = development" while denying the ability to create or delete tags for all instances.

### Understanding Effect, Action, and Resource

The Effect, Action, and Resource attributes of a JSON policy means...

Version

‍This means  2012-10-17 is the date of the latest policy version. This tells you whether the policy is up to date with the latest standards and practices.

‍Statement

‍The main part of the policy structure and defines a list of permissions.

‍Effect

‍This can have two values - either Allow or Deny - to indicate whether the policy allows or denies a certain action. Deny has priority. Looking at the first statement, "Effect": "Allow" means this statement is trying to allow for an action.

‍Action

‍A list of the actions that the policy allows or denies. In this case, "Action": "ec2:*" means all actions that you could possibly take on EC2 instances are allowed. Woohoo!

‍Resource

‍Which resources does this policy apply to? Specifying "*" means all resources within the defined scope (see the next point).

Condition Block (optional)

‍The circumstances under which the policy is in action. In this case, the condition is that the resource is tagged Env - development. This means specifying "Resource": "*" in the line above means all resources with the Env - development tag are impacted by your statement



## 🎯 Key Learning Outcome
Learned how to manage AWS identities, apply least-privilege permissions, organize EC2 resources using tags, and implement environment-based access control.
