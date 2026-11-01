# 🔐 AWS IAM Security 
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
# 🔐 IAM Policies

---

## 📌 What I did in this step

In this step, wiil create an IAM policy that gives access to the development instance

---

## 📖 Understanding IAM policies

IAM policy is a rule for who can do what with your AWS resources. It's all about giving permissions to IAM users, groups, or roles, saying what they can or can't do on certain resources

---

## ⚙️ The policy I set up

For this project, I’ve set up a policy using JSON method.You can create and edit AWS policies in the visual editor or JSON.

---

## 🛡️ Policy effect

I’ve created a policy allows some actions (like starting, stopping, and describing EC2 instances) for instances tagged with "Env = development" while denying the ability to create or delete tags for all instances.

---

## 🧩 Understanding Effect, Action, and Resource

The Effect, Action, and Resource attributes of a JSON policy means...

### 🔹 Version

‍This means 2012-10-17 is the date of the latest policy version. This tells you whether the policy is up to date with the latest standards and practices.

### 🔹 Statement

‍The main part of the policy structure and defines a list of permissions.

### 🔹 Effect

‍This can have two values - either Allow or Deny - to indicate whether the policy allows or denies a certain action. Deny has priority. Looking at the first statement, "Effect": "Allow" means this statement is trying to allow for an action.

### 🔹 Action

‍A list of the actions that the policy allows or denies. In this case, "Action": "ec2:\*" means all actions that you could possibly take on EC2 instances are allowed. Woohoo!

### 🔹 Resource

‍Which resources does this policy apply to? Specifying "\*" means all resources within the defined scope (see the next point).

### 🔹 Condition Block (optional)

‍The circumstances under which the policy is in action. In this case, the condition is that the resource is tagged Env - development. This means specifying "Resource": "\*" in the line above means all resources with the Env - development tag are impacted by your statement

## 📌 My JSON Policy

<img width="1066" height="756" alt="Screenshot 2026-10-25 115022" src="https://github.com/user-attachments/assets/a66229bd-74f5-492a-9cf2-cb52a38b8a85" />




---
# 🚀 AWS IAM — Account Alias, Users & Groups

---



## 1️⃣ Account Alias

### 📝 What I did in this step

**Understanding account aliases**

In this step,  will Simplify user login to your AWS account using an Account Alias.

An Account Alias is a friendly name for your AWS account that you can use  
instead of your account ID (which is usually a bunch of digits) to sign in to the  
AWS Management Console.

<img width="1127" height="521" alt="image" src="https://github.com/user-attachments/assets/2fdd7fea-230d-4e85-8a24-ffdc317747c5" />


---

## 2️⃣ IAM Users and User Groups

### 📝 What I did in this step

- Understanding user groups
- Attaching policies to user groups
- Understanding IAM users

In this step, I will. Set up a dedicated IAM group , so you can  
manage all interns' permissions from one place.

Set up a dedicated IAM user , to log in.

IAM users are the people that will get access to your resources/AWS account, 
whereas user groups are the collections/folders of users for easier user 
management.

I attached the policy I created to this user group, which means...grant them the 
permissions associated with that group.

IAM users  are which we can create of a particular user for access the AWS 
resources.

---

## 3️⃣ Logging in as an IAM User

### 📝 Sharing sign-in details

The first way is...Send the sign-in details by email to the new user.

Share the sign-in details manually by providing the username and temporary 
password directly to the user through a secure channel

---

## 🎯 AWS IAM Learning Journey

**Topics Covered**

- Account Alias
- IAM Users
- IAM User Groups
- Attaching Policies to User Groups
- Logging in as an IAM User
- Sharing Sign-in Details

### Observations from the IAM user dashboard

![Image](https://nextwork.ai/passionate_purple_swift_fig/uploads/51a5fd24-7458-58a6-8653-9b90119a0e4a_6f2ab446)

## Testing IAM Policies

### What I did in this step

In this step, will  Log into AWS using the intern's IAM user.

Test the intern's access to your production and development instance.

### Testing policy actions

I tested my JSON IAM policy by...starting the ec2 instance and stoping the ec2 instance

### Stopping the production instance

When I tried to stop the production instance... This was because...IAM policy is set for accessing development EC2 instance

![Image](https://nextwork.ai/passionate_purple_swift_fig/uploads/51a5fd24-7458-58a6-8653-9b90119a0e4a_0e7a9d6a)

### Stopping the development instance

Next, when I tried to stop the development instance... This was because...policy was created that user

![Image](https://nextwork.ai/passionate_purple_swift_fig/uploads/51a5fd24-7458-58a6-8653-9b90119a0e4a_1811801c)

## IAM Policy Simulator

To extend my project, I'm going to... I'm doing this because...

### Understanding the IAM Policy Simulator

The IAM Policy Simulator is... It's useful for... test user access

### How I used the simulator

I set up a simulation for... The results were... I had to adjust..Under the Select service drop-down, select EC2. - Under the Select actions drop-down, select DeleteTags and StopInstances.

![Image](https://nextwork.ai/passionate_purple_swift_fig/uploads/51a5fd24-7458-58a6-8653-9b90119a0e4a_069d8a621)

---






## 🎯 Key Learning Outcome
Learned how to manage AWS identities, apply least-privilege permissions, organize EC2 resources using tags, and implement environment-based access control.
