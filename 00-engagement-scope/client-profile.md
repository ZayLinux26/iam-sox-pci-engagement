# Keystone Payments Group — Client Profile

> **Engagement type:** Representative IAM consulting engagement, executed in a lab environment.
> **Purpose:** Demonstrate end-to-end identity lifecycle, access certification / segregation-of-duties, and privileged access management for a regulated fintech client, with every control mapped to **SOX ITGC** and **PCI-DSS v4.0.1** requirements.
>
> Keystone Payments Group (KPG) is a fictional organization. It is modeled to be representative of a real mid-market payment processor so that the controls, findings, and evidence produced here mirror actual consulting deliverables. No real client data is used or implied.

---

## 1. Why this client profile

The compliance framing of an IAM engagement is not cosmetic — it dictates which controls are in scope, how access is reviewed, and what evidence auditors expect. KPG is deliberately constructed so that **two frameworks named in real payment-industry engagements both apply at once**:

- It is **publicly traded**, which places **Sarbanes-Oxley (SOX)** IT General Controls over financially-relevant systems in scope.
- It **stores, processes, and transmits cardholder data** in a defined Cardholder Data Environment (CDE), which places **PCI-DSS v4.0.1** in scope.

A single organization carrying both mandates lets one engagement demonstrate access governance against the two control regimes most common in payments and financial services, without conflating them.

---

## 2. Organization snapshot

| Attribute | Value |
|---|---|
| Legal name | Keystone Payments Group, Inc. (KPG) |
| Industry | Payment processing / merchant acquiring (fintech) |
| Ownership | Publicly traded |
| Headcount | ~600 employees |
| Regulatory drivers | SOX (Section 404 ICFR), PCI-DSS v4.0.1 |
| Identity provider | Microsoft Entra ID (primary), Active Directory (on-premises) |
| Business model | Authorizes, clears, and settles card transactions on behalf of merchant clients; operates fraud and risk monitoring over that transaction flow. |

KPG's business model is what makes it a clean fit: as an acquirer/processor it runs a live **CDE** (PCI scope) *and*, as a public company, produces financial statements dependent on IT systems (SOX scope). Access governance sits at the intersection of both.

---

## 3. Organizational structure

Department sizing is modeled to produce realistic access patterns — concentrations of financially-sensitive roles (Finance, Payment Operations) and CDE-privileged roles (Card Systems Engineering) that the access reviews in WS2 must reason about.

| Department | Headcount | Primary framework relevance |
|---|---:|---|
| Executive & Finance Leadership | 15 | SOX — financial reporting ownership, sign-off authority |
| Finance & Accounting | 90 | SOX — AP/AR/GL, financial close; **primary SoD surface** |
| Payment Operations | 120 | SOX + PCI — settlement, reconciliation, chargebacks |
| Card Systems Engineering (CDE) | 60 | PCI — CDE administrators and engineers |
| Fraud & Risk | 45 | PCI Req 10 adjacency — transaction monitoring |
| IT & IAM | 55 | SOX + PCI — provisioning, directory admin, PAM |
| Information Security | 30 | SOX + PCI — control ownership, log review |
| Human Resources | 20 | Authoritative source for the joiner/mover/leaver feed |
| Legal & Compliance | 15 | Control attestation, audit liaison |
| Customer Support | 110 | Least-privilege baseline population |
| Sales & Partnerships | 40 | Least-privilege baseline population |
| **Total** | **600** | |

---

## 4. Systems in scope

The engagement does not attempt to model every system KPG runs. It scopes the systems where identity and access controls are audit-relevant under SOX, PCI, or both.

| System | Type | Framework scope | Role in this engagement |
|---|---|---|---|
| **Microsoft Entra ID** | Cloud identity provider | SOX + PCI | Primary IdP; MFA and Conditional Access enforcement; SAML/OIDC federation to SaaS. Live in this lab. |
| **Active Directory** | On-premises directory | SOX + PCI | On-prem authentication source; JML target for AD-resident accounts. Domain controller stand-up pending (see §6). |
| **Financial Reporting System (ERP)** | Financial application | SOX ITGC | General ledger and financial close; the anchor system for "Access to Programs and Data" controls. |
| **Accounts Payable module** | Financial application | SOX | Invoice entry and payment execution — the **primary segregation-of-duties surface** (WS2). |
| **Card Processing Platform** | CDE system | PCI Req 7 / 8 / 10 | Transaction authorization and settlement switch; access here is need-to-know restricted. |
| **Tokenization Vault** | CDE system | PCI | Protects stored cardholder data; the most privilege-sensitive CDE component. |
| **Fraud Monitoring Platform** | Security application | PCI Req 10 adjacency | Consumes transaction and access logs; monitored population. |
| **Cloud Expense & Procurement SaaS** | SaaS application | SOX | SAML-federated to Entra; the third-party JML target for WS1 (financially relevant, so provisioning/deprovisioning here is SOX-scoped). |

---

## 5. Compliance drivers (the "why" behind every control)

### 5.1 Sarbanes-Oxley (SOX)

Because KPG is publicly traded, management must attest to the effectiveness of Internal Control over Financial Reporting (ICFR) under Section 404. Where financial reporting depends on IT systems, auditors test **IT General Controls (ITGCs)**. This engagement targets the ITGC domain most owned by IAM:

- **Access to Programs and Data** — access to financially-relevant systems (ERP, AP module) is granted on a least-privilege basis, provisioned through an approved process, reviewed periodically, and revoked promptly on termination. WS1 and WS2 produce the evidence for this control.
- **Segregation of Duties (SoD)** — no single identity can both initiate and approve a financial transaction, or both grant access and certify it. WS2's toxic-combination detection is the direct control here.

### 5.2 PCI-DSS v4.0.1

Because KPG operates a CDE, PCI-DSS applies to every system component in and connected to that environment. This engagement targets the access-governance requirements:

- **Requirement 7 — Restrict access by business need to know.** Access to the Card Processing Platform and Tokenization Vault is role-based and least-privilege. WS1 provisioning and WS2 reviews enforce and evidence this.
- **Requirement 8 — Identify users and authenticate access.** Unique IDs, no shared accounts, and MFA for access into the CDE. WS1 (unique-identity provisioning) and WS3 (MFA on privileged access) cover this.
- **Requirement 10 — Log and monitor all access.** Access to CDE systems and cardholder data is logged and reviewable. The evidence trail from WS2/WS3 supports this, and the SoD matrix treats CDE-data-access combined with log-administration as a toxic pair.

---

## 6. Environment note (honest scope boundary)

This lab is executed inside an existing Microsoft Entra ID tenant. KPG identities are modeled within that tenant using display-name, company, and department attributes; the tenant's own domain suffix is a lab artifact and not KPG's production domain. The tenant Global Administrator account is treated as **break-glass only** and is excluded from all groups, Conditional Access policies, and provisioning workflows — it never performs routine operations in this engagement.

**Active Directory execution is staged.** Entra-side identity lifecycle is performed live and is fully demonstrable. The on-premises AD domain controller has not yet been stood up, so AD provisioning is delivered as **domain-controller-ready PowerShell and documented runbooks** rather than live execution, until the DC is online. This boundary is stated plainly rather than papered over — the scripts and runbooks are the honest artifact until the AD side goes live.

---

## 7. Engagement workstreams

| WS | Name | Résumé bullet served | Frameworks |
|---|---|---|---|
| **WS1** | Identity Lifecycle (Joiner / Mover / Leaver) | Identity Lifecycle Operations | SOX (Access to Programs and Data), PCI Req 7 / 8 |
| **WS2** | Access Reviews & Audit Readiness | Access Reviews & Audit Support | SOX (Access to Programs and Data, SoD), PCI Req 7 |
| **WS3** | Privileged Access Management | PAM Engagement Support | SOX (privileged access), PCI Req 7 / 8 |

Each workstream carries its own README, execution artifacts, and evidence. A consolidated case study is produced at the close of the engagement.

