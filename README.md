# Supplier Item Field Length Validation

## Overview
This repository contains a **sanitized engineering case study** describing how I fixed a Web UI validation gap where a “Supplier Item” input accepted values longer than the intended limit for a specific item type.  

---

## Problem
In a requisition-style Web UI, the **Supplier Item** field behaved inconsistently:

- For **non-inventory (ad-hoc)** items, users could enter **more than 30 characters**.
- For **inventory** items, the workflow already effectively enforced a **30-character constraint**.

This mismatch could lead to:
- Invalid or inconsistent data being stored,
- Confusing user experience (different rules for similar fields),
- Downstream submission or processing issues.

---

## Goal
Enforce a consistent rule:

- **Non-inventory (ad-hoc)** Supplier Item values must be **30 characters or fewer**.
- Attempts to enter **31+ characters** must be **blocked** with a clear validation error.
- **Inventory** item behavior must remain unchanged (no regressions).

---

## High-Level Solution
1. **Server-side validation**
   - Added a length validation check for the Supplier Item field **specifically for non-inventory (ad-hoc) items**.
   - If the value length is **greater than 30**, the request is rejected and a validation error is returned.

2. **User-facing error message**
   - Introduced a clear, consistent error message using the existing application message pattern.
   - Ensured the field name is properly displayed using the existing translation/i18n mechanism.

3. **Scoped impact**
   - Validation was intentionally scoped so that **inventory item flows** were not affected.

---

## Testing Checklist
### Boundary and negative tests
- Supplier Item length **29** → allowed.
- Supplier Item length **30** → allowed.
- Supplier Item length **31+** → rejected with validation error (non-inventory only).

### Functional scenarios
- Non-inventory (ad-hoc): new Supplier Item entry.
- Non-inventory (ad-hoc): existing Supplier Item entry.
- Inventory: ensure existing behavior remains unchanged.
- Full submission flow: verify the functional cycle succeeds when inputs are valid.

### Regression checks
- Confirm no UI regressions in requisition line creation/edit flows.
- Confirm error messaging is consistent across relevant screens.

---

## Result
After the change:
- The Web UI displays a validation error when a non-inventory Supplier Item exceeds 30 characters.
- Invalid submissions are prevented, improving data consistency and user experience.
