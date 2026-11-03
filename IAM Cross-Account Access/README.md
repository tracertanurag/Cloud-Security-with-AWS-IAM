 Cross-Account IAM Role Architecture


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

