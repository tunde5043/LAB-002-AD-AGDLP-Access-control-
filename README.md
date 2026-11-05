# LAB-002-AD-AGDLP-Access-control-
Active Directory identity provisioning and role-based access control using AGDLP, NTFS, and SMB permissions.
## 1. Business Scenario

**Organization:** Northbridge Technology  
**Environment:** Active Directory (`tunde.lab`)  
**Department:** Finance — Accounts Payable

### Business Requirement

Northbridge Technology requires secure access to its Finance invoice repository.

Alice Johnson, an Accounts Payable employee, must be able to create, read, modify, and delete invoice documents as part of her daily responsibilities.

Employees outside the authorized Finance group, including Human Resources personnel, must be restricted from accessing confidential financial records.

### IAM Objective

Design and implement role-based access control using Active Directory security groups and the AGDLP model while enforcing least privilege.

### Success Criteria

- Authorized Finance employees can access and modify invoice files.
- Unauthorized employees are denied access.
- Access permissions are managed through security groups rather than individual user accounts.
- Positive and negative authorization tests confirm the security controls.

*Project context: Simulated enterprise IAM implementation conducted in a Windows Server Active Directory home lab.*

## 2. IAM Architecture and AGDLP Design

### Access Control Model

Northbridge Technology implemented the Active Directory AGDLP model to manage access to its Finance invoice repository.

AGDLP stands for:

- **A — Accounts:** Individual employee identities.
- **G — Global Groups:** Organize employees according to business roles.
- **DL — Domain Local Groups:** Represent access requirements for specific resources.
- **P — Permissions:** Define the actions authorized users can perform on those resources.

### Group Membership and Permission Flow

```text
Alice Johnson (ajohnson)
         |
         v
GG_Finance_AP
(Global Security Group)
         |
         v
DL_Invoices_Modify
(Domain Local Security Group)
         |
         v
\\DC01\FinanceInvoices
NTFS: Modify
SMB: Change + Read
```

### Technical Configuration

| Component | Configuration |
|---|---|
| Active Directory domain | tunde.lab |
| Domain controller | DC01 |
| Client workstation | WIN11-01 |
| Authorized user | ajohnson (Finance) |
| Unauthorized test user | mbrown (Human Resources) |
| Global security group | GG_Finance_AP |
| Domain local security group | DL_Invoices_Modify |
| Shared resource | \\DC01\FinanceInvoices |
| NTFS permission | Modify |
| SMB share permission | Change + Read |

### Security Design Decisions

**Role-Based Access Control (RBAC):** Access is managed through security groups associated with business roles rather than individual user permissions.

**Principle of Least Privilege:** Finance employees receive the permissions necessary to perform their assigned duties without being granted unnecessary administrative privileges.

**Separation of Duties:** Human Resources employees are not automatically authorized to access Finance invoice records.

**Centralized Access Management:** When an employee joins or leaves the Accounts Payable role, administrators can update their global group membership instead of modifying permissions directly on the shared folder.
