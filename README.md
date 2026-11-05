# LAB-002-AD-AGDLP-Access-control-
Active Directory identity provisioning and role-based access control using AGDLP, NTFS, and SMB permissions.
## 1. Business Scenario

**Organization:** Northbridge Technologies  
**Environment:** Active Directory (`tunde.lab`)  
**Department:** Finance — Accounts Payable

### Business Requirement

Northbridge Technologies requires secure access to its Finance invoice repository.

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
