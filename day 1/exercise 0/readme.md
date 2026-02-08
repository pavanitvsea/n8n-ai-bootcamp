# First Workflow – Simple Expense Approval Logic

## Overview

This n8n workflow demonstrates a simple **expense approval decision flow** for learning purposes.
It evaluates an expense amount and category, then automatically marks the expense as **approved** or **rejected** based on predefined rules.

The workflow is useful as a foundational example for:

* Approval automations
* Business rule validation
* Conditional branching in n8n

---

## Trigger

### When clicking **Execute workflow**

* **Type:** Manual Trigger
* **Description:**
  Starts the workflow manually from the n8n editor.
  This trigger is mainly used for testing and demonstration purposes.

---

## Workflow Steps

### 1. Edit Fields

* **Node type:** Set
* **Purpose:**
  Defines the input data used by the workflow.

#### Fields Set

| Field Name | Type   | Value             | Description                    |
| ---------- | ------ | ----------------- | ------------------------------ |
| expense    | Number | 25                | Expense amount to be evaluated |
| category   | String | `office_supplies` | Expense category               |

---

### 2. If

* **Node type:** IF
* **Purpose:**
  Evaluates whether the expense meets approval conditions.

#### Conditions (ALL must be true)

| Condition      | Rule                  |
| -------------- | --------------------- |
| Expense amount | Less than `100`       |
| Category       | Not equal to `travel` |

* **True branch:** Expense is approved
* **False branch:** Expense is rejected

---

### 3. Approved

* **Node type:** Set
* **Executed when:** IF conditions are **true**

#### Fields Set

| Field Name | Type   | Value    |
| ---------- | ------ | -------- |
| decision   | String | approved |

---

### 4. Rejected

* **Node type:** Set
* **Executed when:** IF conditions are **false**

#### Fields Set

| Field Name | Type   | Value    |
| ---------- | ------ | -------- |
| decision   | String | rejected |

