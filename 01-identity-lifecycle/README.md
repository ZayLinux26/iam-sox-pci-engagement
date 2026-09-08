# WS1 — Identity Lifecycle (Joiner / Mover / Leaver)

**Client:** Keystone Payments Group (KPG) — synthetic fintech engagement
**Environment:** Microsoft Entra ID (P2)
**Résumé bullet served:** *Identity Lifecycle Operations — executed joiner, mover, and leaver workflows; provisioned, modified, and deactivated accounts consistent with least-privilege RBAC.*

---

## Objective

Stand up the identity foundation for the KPG engagement: a role-based access model, a provisioned user population, and demonstrated joiner / mover / leaver (JML) operations — each provisioning decision made on a least-privilege basis and mapped to the control it supports.

This workstream also **plants the access conditions** that the WS2 access review is built to detect. Those conditions are created here as ordinary-looking access; they are not labeled as problems in the live environment. Identifying them is the work of WS2.

---

## Control mapping

| Control | Framework | How WS1 addresses it |
|---|---|---|
| Access to Programs and Data — provisioning | SOX ITGC | Accounts are created and granted access through a defined, role-based process rather than ad-hoc direct permissions. |
| Access to Programs and Data — deprovisioning | SOX ITGC | Leaver process disables access on employment change (runbook + demonstrated status change). |
| Least privilege / need-to-know | PCI-DSS Req 7 | Users receive only the role group(s) their job requires; CDE access is restricted to Card Systems Engineering. |
| Unique identification | PCI-DSS Req 8 | Every identity is a unique account; no shared logins. |
| Role-based access control | SOX / PCI | Access is granted by membership in role groups, not per-user permission grants. |

---

## Role catalog

Access is modeled as role groups (Entra security groups, `Assigned` membership, not role-assignable) using the convention `KPG-RL-<Function>`. A user's job function determines group membership; the group carries the access.

| Role group | Function | Framework note |
|---|---|---|
| KPG-RL-AP-Entry | Accounts Payable — invoice entry | SOX SoD: entry only |
| KPG-RL-AP-Approval | Accounts Payable — payment approval | SOX SoD: approval only |
| KPG-RL-GL-Accountant | General ledger / journal entry | SOX |
| KPG-RL-Financial-Reporting | ERP financial close | SOX |
| KPG-RL-Settlement | Settlement processing | SOX / PCI |
| KPG-RL-Reconciliation | Reconciliation | SOX / PCI |
| KPG-RL-Chargeback | Chargeback handling | PCI |
| KPG-RL-CDE-Engineer | Card Processing Platform (CDE) | PCI Req 7 |
| KPG-RL-CDE-Vault-Admin | Tokenization Vault (CDE, highest sensitivity) | PCI Req 7 |
| KPG-RL-Fraud-Analyst | Fraud monitoring | PCI Req 10 adjacency |
| KPG-RL-HR-Admin | HR system administration (JML source) | SOX |
| KPG-RL-Support-Rep | Customer support (least-privilege baseline) | — |

The privileged / administrative role groups (IAM provisioning, access certification, PAM engineering, systems administration) are intentionally **not** built in WS1. They carry elevated, role-assignable and PIM-governed configuration and are stood up in **WS3 (Privileged Access Management)**.

---

## JML operations demonstrated

**Joiner — Nicole Barrett (AP Clerk).** Account created manually, provisioned to `KPG-RL-AP-Entry` only (invoice entry, no approval). Demonstrates least-privilege onboarding to a single role.

**Mover — Diane Kohler (AP Clerk → AP Manager).** Promoted; granted `KPG-RL-AP-Approval` for her new role while retaining `KPG-RL-AP-Entry` from her prior role. This reproduces **privilege creep** — the most common real-world mover failure, where new access is added but prior access is not removed. The residual entry access is a seeded segregation-of-duties condition for WS2.

**Bulk provisioning — HR feed (15 users).** The remaining named population was provisioned via Entra bulk create from a CSV representing the HR joiner feed (`hr-feed/`). Demonstrates data-driven provisioning from an authoritative source, at scale, rather than manual account-by-account creation.

**Leaver — staged.** Leaver deprovisioning is documented as a runbook (`runbooks/`) and demonstrated as an account status change. The contingent-worker leaver condition (Roman Petrov, contract ended, account not disabled) is intentionally left un-remediated as a seeded orphaned-account condition for WS2.

---

## Seeded conditions planted in WS1

These are created here and detected in WS2. This list is orientation only; the authoritative answer key with entitlement-level detail lives in the WS2 seeded-anomaly manifest, which the detection process does not read.

| Identity | Condition | Framework relevance |
|---|---|---|
| Diane Kohler | AP invoice entry + payment approval (SoD) | SOX segregation of duties |
| Wesley Chu | Standard CDE engineer also holding Vault-Admin (excessive / need-to-know) | PCI-DSS Req 7 |
| Hannah Delgado | Support rep retaining general-ledger access (excessive cross-function) | SOX Access to Programs and Data |
| Roman Petrov | Contractor, engagement ended, account still enabled (orphaned) | SOX / PCI Req 8 |
| Beatrice Nguyen | On extended leave, account enabled, no recent sign-in (dormant) | SOX Access to Programs and Data |

Additional conditions at statistical volume are introduced in the WS2 synthetic dataset so the detection process can be evaluated for precision and recall against a large population. The live Entra cast illustrates; the synthetic dataset validates.

---

## Evidence index

Screenshots are captured from the live Entra environment and stored in `screenshots/`.

| # | File | Shows |
|---|---|---|
| 01 | `01-kpg-rl-ap-entry-group-created.png` | AP-Entry role group created with SoD description |
| 02 | `02-kpg-rl-ap-approval-group-created.png` | AP-Approval role group created with SoD description |
| 03 | `03-kpg-role-catalog-all-groups.png` | Full KPG role catalog |
| 04 | `04-joiner-nicole-barrett-created.png` | Joiner account provisioned (AP Clerk) |
| 05 | `05-joiner-nicole-barrett-ap-entry-member.png` | Joiner in AP-Entry only (least privilege) |
| 06 | `06-mover-diane-kohler-created.png` | Mover account (AP Manager) |
| 07 | `07-mover-diane-kohler-both-ap-groups.png` | Mover in both AP-Entry and AP-Approval (seeded SoD) |
| 08 | `08-bulk-create-kpg-population.png` | Bulk-provisioned population from HR feed |
| 09 | `09-seeded-wesley-chu-excessive-cde.png` | CDE engineer also in Vault-Admin (seeded excessive) |
| 10 | `10-seeded-hannah-delgado-residual-finance.png` | Support rep also in GL-Accountant (seeded excessive) |

---

## Scope boundary (honest statement of what is and isn't live)

- **Live and demonstrated:** all Entra-side work above — role catalog, manual joiner and mover, bulk provisioning, group-based least-privilege assignment, and the seeded conditions.
- **Staged, not live:** on-premises Active Directory provisioning. The résumé engagement spans AD, Entra, and SaaS; in this lab the AD domain controller is not yet stood up, so AD provisioning is delivered as domain-controller-ready PowerShell and runbooks rather than executed against a live DC. SaaS (SAML-federated app) provisioning is scoped for the same phase. These are marked clearly rather than represented as completed.

---

## Contents

```
01-identity-lifecycle/
├── README.md            # this file
├── hr-feed/             # HR joiner feed (passwords redacted)
├── runbooks/            # JML runbooks incl. leaver deprovisioning
├── scripts/             # DC-ready PowerShell / Graph provisioning (staged)
└── screenshots/         # evidence (01–10)
```
