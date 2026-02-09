# P3 – Build a simple AI-powered text classifier

**Tip:** Start by copying the workflow from P1 and then modify it as described below.

## Workflow Overview

**Purpose:** Collect IT support requests, classify them as *Urgent* or *Not urgent* using an LLM, and create a ticket file in GitHub with the classification result.

**Trigger:** Form submission (`Form Trigger`)

**Nodes:**

1. On form submission
2. Edit Fields
3. Basic LLM Chain

   3.1 Google Gemini Chat Model
   3.2 Structured Output Parser

5. Create a file

---

#### Node 1: On form submission

**Type:** `Form Trigger (n8n-nodes-base.formTrigger)`

**Purpose:** Collect user input for IT support requests.

| Parameter            | Value                                                              |
| -------------------- | ------------------------------------------------------------------ |
| **Form Title**       | IT Service Request                                                 |
| **Form Description** | Submit your issue here                                             |
| **Form Fields**      | - Issue description (textarea, required)<br>- Your Name (required) |
| **Submit Message**   | IT support will be in touch shortly!                               |

**Sample Submission:**

| Field                 | Value                       |
| --------------------- | --------------------------- |
| **Your Name**         | Sabrina                     |
| **Issue description** | I can't log into my account |

---

#### Node 2: Edit Fields

**Type:** `Set (n8n-nodes-base.set)`

**Purpose:** Generate a unique ticket ID from the form submission timestamp.

| Parameter      | Value                                                                 |
| -------------- | --------------------------------------------------------------------- |
| **Field Name** | ID                                                                    |
| **Expression** | `={{ $json.submittedAt.toDateTime().ts.toString(36).toUpperCase() }}` |
| **Type**       | string                                                                |

---

#### Node 3: Basic LLM Chain

**Type:** `LLM Chain (@n8n/n8n-nodes-langchain.chainLlm)`

**Purpose:** Classify the IT issue as *Urgent* or *Not urgent* based on predefined rules.

| Parameter      | Value                                                                 |
| -------------- | --------------------------------------------------------------------- |
| **Prompt Source** | Define below                                                       |
| **Require Output Format** | Toggle On |

**Prompt (User Message)**
````markdown
Issue:

```
{{ $('On form submission').item.json['Issue description'] }}
```
````

**System Prompt**
```markdown
### **Role**

You are a text classification system for an IT support helpdesk.
Your task is to read an incoming support ticket and classify it as either **"Urgent"** or **"Not urgent"** based on the content.

### Classification Rules:

* **Urgent** if the ticket describes:

  * Complete system or network outages.
  * Inability to perform critical work tasks.
  * Security breaches or data loss.
  * Hardware failure affecting essential operations.
  * Issues impacting multiple users or key business functions.
  * Time-sensitive problems needing immediate attention.

* **Not urgent** if the ticket describes:

  * Minor bugs or usability issues.
  * Requests for information, training, or feature changes.
  * Scheduled maintenance or non-critical updates.
  * Problems with available workarounds.
  * Single-user issues not blocking essential tasks.

### Output Format:

Respond **only** with one of the following labels:

* `Urgent`
* `Not urgent`
```

#### Subnode 3.1: Google Gemini Chat Model

**Type:** `Google Gemini Chat Model (@n8n/n8n-nodes-langchain.lmChatGoogleGemini)`

**Purpose:** Provide the LLM backend for classification.

| Parameter      | Value                              |
| -------------- | ---------------------------------- |
| **Credential** | Google Gemini (PaLM) API account 3 |

#### Subnode 3.2: Structured Output Parser

**Type:** `Structured Output Parser (@n8n/n8n-nodes-langchain.outputParserStructured)`

**Purpose:** Parse the model’s output into a structured JSON format.

| Parameter          | Value                  |
| ------------------ | ---------------------- |
| **Schema Example** | `{ "prio": "Urgent" }` |

---

#### Node 4: Create a file

**Type:** `GitHub (n8n-nodes-base.github)`

**Purpose:** Create a ticket file in GitHub including classification, submitter info, and issue details.

| Parameter          | Value                                                                                                                                                                                                                                            |
| ------------------ | -------------------------------------------------------- |
| **Authentication** | oAuth2                                                                                                                                                                                                                                           |
| **Resource**          | File                                                                                                                                                                                                                                          |
| **Operation**     | Create                                                    |
| **File Path**      | `day 1/project 3/tickets/{{ $('Edit Fields').item.json.ID }}.txt`                                                                                                                                                                                      |
| **Commit Message** | new ticket                                                                                                                                                                                                                                       |

**File Content**
```markdown

Prio: {{ $json.output.prio }}

Name: {{ $('On form submission').item.json['Your Name'] }}

Submitted: {{ $('On form submission').item.json.submittedAt }}

Description:
{{ $('On form submission').item.json['Issue description'] }}
```

