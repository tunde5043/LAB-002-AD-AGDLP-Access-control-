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

## 3. Technical Implementation

### 3.1 Identity Provisioning

Provisioned two Active Directory user accounts in the `tunde.lab` domain to simulate employees from different departments.

| User | Department | AD Organizational Unit | Purpose |
|---|---|---|---|
| Alice Johnson (`ajohnson`) | Finance | `Finance/Users` | Authorized Accounts Payable employee |
| Michael Brown (`mbrown`) | Human Resources | `Human Resources/Users` | Unauthorized access test |

Both accounts were enabled and used to validate access from the domain-joined Windows 11 workstation.

### 3.2 Security Group Configuration

Created two Active Directory security groups:

- **`GG_Finance_AP`** — Global security group representing the Finance Accounts Payable role.
- **`DL_Invoices_Modify`** — Domain Local security group representing Modify access to the invoice repository.

Added `ajohnson` to `GG_Finance_AP` and nested `GG_Finance_AP` inside `DL_Invoices_Modify`.

This separates business-role membership from resource permissions and simplifies access administration.

### 3.3 NTFS Permissions

Configured the following folder on the domain controller:

`C:\LabShares\Finance\Invoices`

- Disabled inherited permissions and removed broad access granted to `TUNDE\Users`.
- Granted `DL_Invoices_Modify` Modify permissions.
- Retained administrative and SYSTEM access.

### 3.4 SMB Share Configuration

Published the folder as:

`\\DC01\FinanceInvoices`

Configured SMB share permissions:

| Principal | Share permissions |
|---|---|
| `DL_Invoices_Modify` | Change, Read |
| `Everyone` | Removed |

The effective access to the shared folder is constrained by both SMB share permissions and NTFS permissions.

### 3.5 Authorization Testing

Performed positive and negative authorization tests from `WIN11-01`.

| Test | Expected result | Actual result |
|---|---|---|
| Alice opens Finance share | Allowed | Passed |
| Alice creates a file | Allowed | Passed |
| Alice modifies and saves a file | Allowed | Passed |
| Alice deletes a file | Allowed | Passed |
| Michael opens Finance share | Denied | Passed |

The tests demonstrated that the authorized Finance user could perform the required file operations while the HR test user was denied access.

**Testing limitation:** This validation covered the configured access paths. Group-membership revocation, audit-event collection, and additional permission paths were not tested.
