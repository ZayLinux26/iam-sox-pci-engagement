# Keystone Payments Group — Persona Roster

> **What this is:** the named identity population for the KPG engagement, presented as a realistic **current-state roster** — the access snapshot a consultant inherits at the start of an engagement. It already contains movers, leavers, and access drift, because a real review begins from exactly this kind of messy baseline.
>
> **What this is not:** an answer key. The detection engine in WS2 reads live Entra state (group memberships, attributes, sign-in activity) and applies policy — it never reads this file. The formal mapping of *which identity carries which planted condition* lives in the WS2 seeded-anomaly manifest, kept separate so the engine's evaluation is blind. See §5.

---

## 1. Conventions

| Convention | Value |
|---|---|
| Display name | `First Last` (KPG cast) |
| UPN pattern | `first.last@<lab-tenant>` (tenant domain is a lab artifact, not KPG's production domain) |
| `company` attribute | `Keystone Payments Group` |
| `department` attribute | Set per department below (drives ABAC/CDE scoping and review grouping) |
| Intended role | The RBAC role the identity *should* map to; becomes an Entra security group in WS1 |
| Employment status | `Active` · `On Leave` · `Contractor` · `Terminated` |
| Break-glass account | The tenant Global Administrator, excluded from all groups, CA policies, and workflows — never used for routine operations |

---

## 2. Engagement & IAM team (the operators)

These identities perform the engagement work. The most important design feature here is a **segregation-of-duties boundary built into the team itself**: the person who provisions access cannot certify it, and the person who certifies cannot provision. That separation is the SOX ITGC control ("no single identity both grants and reviews access") working *as designed* — and it stands in deliberate contrast to a seeded violation of the same control elsewhere in the population (see §5).

| Name | Title | Intended role | Status | Notes |
|---|---|---|---|---|
| Nathan Brooks | IAM Manager | RL-IAM-Manager | Active | Engagement lead; approves role design |
| Olivia Tran | Identity Operations Lead | RL-IAM-Provisioning | Active | **Provisions** JML; *cannot* certify |
| Derek Sullivan | Access Governance Analyst | RL-IAM-Certifier | Active | **Certifies** access reviews; *cannot* provision |
| Anita Desai | PAM Engineer | RL-PAM-Engineer | Active | Owns WS3 privileged access design |
| Greg Novak | Senior Systems Administrator | RL-Sysadmin | Active | Directory/infrastructure admin (privileged) |

---

## 3. Business personas (the subjects of JML and review)

### 3.1 Finance & Accounting — SOX SoD surface

The AP function is the primary segregation-of-duties surface: **invoice entry** and **payment approval** must never sit with the same identity.

| Name | Title | Intended role | Status | Manager |
|---|---|---|---|---|
| Nicole Barrett | AP Clerk | RL-AP-Entry | Active | Susan Alden |
| Victor Reyes | AP Manager | RL-AP-Approval | Active | Susan Alden |
| Diane Kohler | AP Manager (recently promoted from AP Clerk) | RL-AP-Approval | Active | Susan Alden |
| Paul Whitfield | GL Accountant | RL-GL-Accountant | Active | Susan Alden |
| Susan Alden | Controller | RL-Financial-Reporting | Active | Nathan Brooks (dotted) |

### 3.2 Payment Operations

| Name | Title | Intended role | Status | Manager |
|---|---|---|---|---|
| Kevin Doyle | Settlement Analyst | RL-Settlement | Active | Monica Frey |
| Monica Frey | Reconciliation Lead | RL-Reconciliation | Active | — |
| Andre Okafor | Chargeback Specialist | RL-Chargeback | Active | Monica Frey |

### 3.3 Card Systems Engineering — PCI CDE

Access to CDE systems is need-to-know and least-privilege (PCI Req 7). The Tokenization Vault is the most privilege-sensitive component in the environment.

| Name | Title | Intended role | Status | Manager |
|---|---|---|---|---|
| Brian Halvorsen | CDE Engineer | RL-CDE-Engineer | Active | — |
| Elena Vasquez | Tokenization Vault Admin | RL-CDE-Vault-Admin | Active | Brian Halvorsen |
| Wesley Chu | CDE Engineer | RL-CDE-Engineer | Active | Brian Halvorsen |

### 3.4 Fraud & Risk

| Name | Title | Intended role | Status | Manager |
|---|---|---|---|---|
| Grace Lin | Fraud Analyst | RL-Fraud-Analyst | Active | — |

### 3.5 Human Resources — authoritative source

HR owns the joiner/mover/leaver feed that drives WS1. A change in HR status is the trigger for every lifecycle action.

| Name | Title | Intended role | Status | Manager |
|---|---|---|---|---|
| Sandra Mills | HR Director | RL-HR-Admin | Active | — |
| Tyler Reeves | HR Analyst | RL-HR-Admin | Active | Sandra Mills |

### 3.6 Customer Support & contingent workforce

| Name | Title | Intended role | Status | Manager |
|---|---|---|---|---|
| Jamal Carter | Support Representative | RL-Support-Rep | Active | — |
| Hannah Delgado | Support Representative | RL-Support-Rep | Active | — |
| Beatrice Nguyen | Support Representative | RL-Support-Rep | On Leave | — |
| Roman Petrov | Support Contractor | RL-Support-Rep | Contractor | — |

---

## 4. Current-state notes (the inherited mess)

These are factual conditions in the roster as received. Whether any of them constitutes a *reportable* access finding is exactly what WS2 determines — the roster states the situation; the review applies the policy.

- **Diane Kohler** was promoted from AP Clerk to AP Manager. Her access was added to on promotion; whether prior entitlements were removed is unverified at handover.
- **Greg Novak** holds broad administrative access accumulated across infrastructure projects; the exact entitlement set is unverified at handover.
- **Wesley Chu** supports both CDE transaction troubleshooting and platform log operations.
- **Hannah Delgado** previously supported a finance systems rollout before moving fully to Customer Support.
- **Beatrice Nguyen** is on extended parental leave; her account remains enabled.
- **Roman Petrov** is a contractor whose engagement end date has passed; account status at handover is unverified.

None of the above is labeled here as a violation. That determination is the work product of WS2.

---

## 5. Seeded-condition methodology

This engagement uses a **seeded-anomaly-plus-manifest** design so the access review can be evaluated with real precision and recall, not just asserted to work.

- The **named roster above** carries a small set of human-readable, illustrative conditions (residual mover access, an over-privileged admin, a CDE toxic pairing, a dormant account, an expired contractor, cross-function residual access). These are the findings a reviewer can point to and explain in plain language.
- The **bulk KPG population (~600 identities)** carries additional conditions at statistical volume, so the engine is scored against enough positives and negatives to produce meaningful precision/recall — the named cast illustrates, the bulk population validates.
- The **WS2 seeded-anomaly manifest** is the formal answer key: it maps each planted condition to the exact identity, entitlements, and control violated (SOX Access to Programs and Data, SOX/ITGC SoD, or PCI Req 7). The manifest is stored with WS2 and **is never ingested by the detection engine** — the engine reads live Entra state, produces findings, and its output is scored against the manifest afterward.

This separation — messy roster as input, live directory state as what the engine reads, manifest as the blind answer key — is what lets the engagement claim an auditable detection result rather than a demonstration.

