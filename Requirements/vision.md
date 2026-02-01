# Vision and Scope Document — Toscana Manager 2.0 (V5.0)

## 1. Introduction

This document describes the vision and scope of “Toscana Manager 2.0”, a system designed to support the operations and administrative control of “La Toscana”, focusing on security, traceability, sales, catalog, suppliers, and reporting—reducing the risks of tampering and losses caused by lack of control.

## 2. Business context

### 2.2 Company needs

| ID | Need description |
| --- | --- |
| NEC-01 | Manage multiple branches (12) with information separated by branch, while maintaining corporate standardization. |
| NEC-02 | Control system access through roles and permissions, including branch-level permissions (e.g., a cashier from Branch A cannot view Branch B). |
| NEC-03 | Log sensitive actions (sales, cancellations, discounts, adjustments, cash closes) with date, time, and responsible user (audit trail). |
| NEC-04 | Record sales in a way that prevents alterations or unregistered sales. |
| NEC-05 | Allow sales with multiple payment methods (debit/credit cards and cash) and control discounts under authorization. |
| NEC-06 | Generate a ticket/receipt as evidence for each transaction. |
| NEC-07 | Manage the product catalog (creates, logical deletes, updates, and queries) in a reliable and traceable way. |
| NEC-08 | Manage suppliers (registration, updates, and logical deletes) by centralizing information. |
| NEC-09 | Enable the owner/executive team to view reports and analytics for oversight and decision-making. |
| NEC-10 | Automatically generate weekly reports (per branch and corporate consolidated). |
| NEC-11 | (Desirable) Consolidate sales/cash information per branch at the corporate level for audit and follow-up. |

## 3. Solution vision

### 3.1 Vision statement (aligned to corporate)

> “Toscana Manager 2.0 will be a corporate point-of-sale and operational control platform that connects the 12 branches, with role-based access and auditing to ensure traceability, and with automated weekly reporting to support oversight and executive decision-making.”

### 3.2 Key future processes (TO-BE) — multi-branch adjustments

| Process ID | Process name | Description and main steps |
| --- | --- | --- |
| TOBE-01 | Controlled branch sales (POS) | The sale is recorded at the branch, a unique transaction/folio is generated, payments are captured, discounts are applied according to permissions, and a ticket is printed/generated. |
| TOBE-02 | Corporate access control | Users and permissions can be defined with branch-level scope and by role. |
| TOBE-03 | Corporate auditing | Sensitive actions are logged and can be consulted by management/executives. |
| TOBE-04 | Central catalog with branch applicability | Products can be managed centrally and made available per branch (according to rules). |
| TOBE-05 | Automated weekly reporting | Every week, the system generates branch-level reports and a corporate consolidated report. |

### 3.3 High-level capabilities (Epics) — integrating multi-branch and reports

| ID | Epic | Priority | Notes |
| --- | --- | --- | --- |
| EP-01 | Multi-branch management | High | Create/edit branches, minimal configuration, data segmentation. |
| EP-02 | User, role, and permission management (per branch) | High | RBAC + branch-level scope (who can see what). |
| EP-03 | Audit of sensitive actions | High | Critical event log (discounts, cancellations, adjustments, closes, user changes). |
| EP-04 | Sales, payments, and ticketing | High | Includes multiple payment methods and controlled discounts. |
| EP-05 | Sales queries and executive oversight | High | Filters by date, cashier, folio, payment method, total, branch. |
| EP-06 | Product catalog (CRUD + logical delete + traceability) | High | Maintain history and consistency. |
| EP-07 | Suppliers | Medium | Registration/update/logical delete. |
| EP-08 | Automated weekly reports (per branch and corporate) | High | Scheduled weekly report, exportable. |

## 4. Project scope

### In scope (priority)

- Multi-branch (12): data separation and filtering by branch
- Roles/permissions per branch
- Sales with unique folio, payments, controlled discounts, ticket/receipt
- Sales queries by branch and consolidated
- Audit trail for sensitive actions
- Automated weekly reports (per branch and corporate)

### Out of scope

- Shift/attendance control
- Electronic invoicing (if not planned)
- E-commerce / online store

## 5. System context

### Stakeholders summary

| Role | Description | Responsibilities |
| --- | --- | --- |
| General Director | Sponsor | Approve strategy |
| Branch manager | Key user | Day-to-day operations |
| Cashier | End user | Record sales |
| Supervisor | Control | Validate cash closes |
| IT Administrator | Technical | Support and maintenance |

### Operating environment

- Users: ~250
- Hours: 6:00 to 23:00
- Platform: Web + Tablet
- Architectural Model (Local-First): To mitigate internet outages, the system will operate primarily against a lightweight local database on each branch server. This ensures sales never stop.

**Synchronization Protocol:**

- **Up-Sync (Upload):** Transactions (sales, cash closes) will be sent to the cloud via asynchronous message queues. In case of disconnection, the system will automatically retry every 5 minutes until receipt is confirmed.
- **Down-Sync (Download):** Catalog or price updates will be downloaded on demand or during nightly maintenance windows to avoid locking conflicts during operations.
- **Conflict Resolution:** In case of discrepancies between cloud and local inventory, the transaction with the most recent timestamp recorded by the local server will prevail.
- **Integrations:** POS terminals, printers, barcode scanners (optional for the future, if shift control, electronic invoicing, e-commerce, fraud detection, etc. are implemented).

### Quality attributes

- **Performance:** The system must respond in under 2 seconds for sales transactions. Weekend report generation should take under 30 seconds.
- **Security:** Role- and permission-based access control; branch-level segregation; audit logging of critical actions.
- **Availability:**
  - 99.9% availability is required during operating hours (6:00 to 23:00).
  - **RTO (Recovery Time Objective):** In case of a total local server failure, operations must be restorable on a secondary tablet or terminal in under 15 minutes.
- **Usability:** Efficient interface for cashiers and supervisors (few steps to complete a sale, clear filters for reports), enabling basic sales actions and inventory-related tasks to be learned in under 5 minutes.
