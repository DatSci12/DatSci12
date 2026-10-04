# Tutorial: From “Ask My Data” to an “AI Coworker” with Databricks Genie One

## Overview

Databricks is pushing Genie One beyond simple conversational analytics.

The old mental model was:

> **Ask a question about your data and get an answer.**

The emerging model is:

> **Give AI business context, governed data, documents, organizational instructions, and recurring work—and let it operate more like an AI coworker.**

This tutorial is designed as a **30–40 minute classroom demo** for students, using Databricks Free Edition where possible.

> **Important note:** not every capability shown in the announcement image is guaranteed to be available in Databricks Free Edition or in every workspace. Some capabilities depend on workspace rollout, preview access, or enterprise administration.

---

# Learning Objectives

By the end of the session, students should understand how Genie One is evolving across five dimensions:

- Business context
- Governed enterprise data
- Documents and files
- Organizational instructions
- Recurring and agentic workflows

The architecture to keep in mind is:

```text
Question
   ↓
Business Context
   ↓
Governed Data
   ↓
Documents
   ↓
Reasoning
   ↓
Action / Recurring Work
```

Compare that with traditional BI:

```text
Question
   ↓
SQL
   ↓
Answer
```

---

# Part 1 — Open With the Big Idea

## Time: 3 minutes

Start by showing the attached Genie One image.

Then introduce the shift:

> A year ago, one of the easiest ways to explain Genie was:
>
> “Ask your enterprise data questions using English instead of SQL.”
>
> That explanation is becoming incomplete.
>
> Genie One is moving toward becoming a business-aware AI coworker.
>
> It does not simply need to know where tables live. It increasingly needs to understand what your organization means by concepts such as revenue, capacity, patient encounter, backlog, active customer, utilization, or overtime opportunity.

Imagine an enterprise environment containing:

```text
orders
inventory
facilities
capacity
staffing
revenue
```

A normal data assistant may be able to find these tables.

But now ask:

> **Which locations are likely to lose revenue next week because demand exceeds available capacity?**

That requires more than column discovery.

The AI needs to understand:

- What does "demand" mean?
- How is "capacity" calculated?
- Which facilities are related?
- Which source is authoritative?
- What time period should be used?
- What data is the current user allowed to access?

That is the strategic shift.

---

# Part 2 — Build a Small Demo Dataset

## Time: 5 minutes

Use a simple Radiology operational scenario.

The goal is not advanced SQL. The goal is to demonstrate how business meaning changes what an AI system can do.

Run the following in Databricks.

```sql
CREATE CATALOG IF NOT EXISTS genie_demo;

CREATE SCHEMA IF NOT EXISTS genie_demo.radiology;

CREATE OR REPLACE TABLE genie_demo.radiology.imaging_operations AS
SELECT * FROM VALUES
  ('Manhattan', 'MRI', DATE'2026-10-01', 120, 100, 92, 450, 40),
  ('Manhattan', 'MRI', DATE'2026-10-02', 130, 100, 96, 450, 40),
  ('Manhattan', 'MRI', DATE'2026-10-03', 145, 100, 98, 450, 40),

  ('Brooklyn', 'MRI', DATE'2026-10-01', 85, 110, 72, 400, 35),
  ('Brooklyn', 'MRI', DATE'2026-10-02', 82, 110, 70, 400, 35),
  ('Brooklyn', 'MRI', DATE'2026-10-03', 80, 110, 68, 400, 35),

  ('Queens', 'MRI', DATE'2026-10-01', 95, 95, 88, 375, 30),
  ('Queens', 'MRI', DATE'2026-10-02', 105, 95, 91, 375, 30),
  ('Queens', 'MRI', DATE'2026-10-03', 118, 95, 94, 375, 30)

AS t(
   facility,
   modality,
   service_date,
   orders,
   available_slots,
   booked_slots,
   revenue_per_scan,
   overtime_cost_per_slot
);
```

Preview the data:

```sql
SELECT *
FROM genie_demo.radiology.imaging_operations
ORDER BY service_date, facility;
```

Tell the students:

> We intentionally created a small dataset.  
> The interesting part of this lesson is not SQL complexity.  
> It is what happens when business meaning gets layered on top of enterprise data.

---

# Part 3 — Start With Traditional Conversational Analytics

## Time: 4 minutes

Open Genie or the available natural-language analytics experience.

Ask:

```text
Show MRI demand by facility over the last three days.
```

Then:

```text
Which facility appears most constrained?
```

Then:

```text
Compare orders with available capacity.
```

This demonstrates the familiar model:

```text
Natural Language
      ↓
Generated SQL
      ↓
Governed Query
      ↓
Result
      ↓
Visualization
```

Now ask a harder question:

```text
Where should we authorize overtime tomorrow
to maximize incremental revenue?
```

Pause.

Ask the class:

> Did we define what "constrained" means?

No.

> Did we tell the system what our overtime policy is?

No.

> Did we tell it to consider available capacity at other facilities first?

No.

This exposes the limitation of purely conversational BI.

---

# Part 4 — Introduce Genie Ontology

## Time: 5 minutes

This is the most important conceptual section of the tutorial.

Use this architecture:

```text
                  BUSINESS MEANING
                         │
                         ▼
                  Genie Ontology
                         │
        ┌────────────────┼────────────────┐
        │                │                │
        ▼                ▼                ▼

   Metric Views       Tables         Documents

        │                │                │
        ▼                ▼                ▼

 Revenue Rules      Operational       Policies
 Capacity KPIs         Data          Definitions

        └────────────────┬────────────────┘
                         │
                         ▼
                     Genie One
```

A useful teaching distinction is:

## Unity Catalog answers

> **What data are you allowed to use?**

## Ontology helps answer

> **What does the data mean?**

That business meaning can include:

- Business definitions
- Relationships
- Trusted sources
- Metrics
- Business terminology
- Entity relationships
- Organizational context

---

# Demo Business Definitions

Introduce definitions such as:

```text
Demand
= number of imaging orders

Utilization
= booked slots / available slots

Capacity Gap
= orders - available slots

Revenue Opportunity
= unserved orders × revenue per scan
```

Then define a business rule:

```text
Overtime should only be considered when:

capacity_gap > 10

AND

utilization > 90%
```

Now ask:

```text
Which facility should receive overtime tomorrow?

Explain why.
```

The teaching point is important:

> Enterprise AI should not continually invent the meaning of business terminology.

Ideally, it should reason using organization-approved semantics.

---

# Part 5 — Workspace Instructions

## Time: 4 minutes

Ontology addresses:

> **What does our business mean?**

Workspace instructions address:

> **How should our organization expect AI to behave?**

For example:

```markdown
# Radiology Analytics Instructions

Always use the most recent available date unless the user
specifies otherwise.

Define high utilization as utilization >= 90%.

Do not recommend overtime if another facility has at least
15% unused capacity.

Whenever recommending an operational action:

1. State the finding.
2. Provide supporting metrics.
3. Identify potential alternatives.
4. State any assumptions.
```

Now ask:

```text
What should we do about MRI capacity tomorrow?
```

The conceptual flow becomes:

```text
User Question
      ↓
Workspace Instructions
      ↓
Business Context
      ↓
Governed Data
      ↓
Reasoning
      ↓
Consistent Response
```

The value is not merely better answers.

The value is **repeatability**.

---

# Part 6 — Bring Your Own Files

## Time: 4 minutes

Enterprise knowledge does not live exclusively inside databases.

Important context may live in:

```text
PDF
Word
Excel
CSV
PowerPoint
Images
```

Imagine that Radiology has a policy document.

Create something simple for the demonstration.

```text
Radiology Capacity Policy

1. Overtime may be considered when utilization exceeds 90%.

2. Before overtime is authorized, available capacity at nearby
   facilities must be reviewed.

3. Expected incremental revenue should exceed overtime cost
   by at least 3:1.

4. Patient access requirements take priority over utilization
   targets.
```

Upload the file if that capability is available in your workspace.

Then ask:

```text
Using the operational data and the attached Radiology Capacity
Policy, should Manhattan MRI receive overtime?

Explain the recommendation.
```

The AI now has access to multiple forms of context:

```text
STRUCTURED DATA
      +
DOCUMENT
      +
BUSINESS CONTEXT
      +
ORGANIZATIONAL INSTRUCTIONS
```

This is very different from simply asking questions against a table.

---

# Part 7 — Query Governed Unity Catalog Data

## Time: 3 minutes

Explain the enterprise architecture.

```text
                 User
                   │
                   ▼

              Genie One
                   │
                   ▼

             Genie Context
                   │
                   ▼

             Unity Catalog

       ┌───────────┼───────────┐
       │           │           │
       ▼           ▼           ▼

     Tables       Views     Metric Views

                   │
                   ▼

       Permissions / Lineage /
       Governance / Discovery
```

One important misconception to address:

> Genie does not simply dump every table the user can access into an LLM prompt.

Instead, the system attempts to identify relevant governed assets and context for the question being asked.

This distinction matters enormously for:

- Scale
- Cost
- Security
- Relevance
- Performance
- Hallucination reduction

---

# Part 8 — Scheduled Tasks

## Time: 4 minutes

Now move from analytics toward work.

Ask students:

> What happens if somebody has to perform this same analysis every morning?

Traditional process:

```text
Human
   ↓
Open Dashboard
   ↓
Inspect Backlog
   ↓
Compare Capacity
   ↓
Review Policy
   ↓
Make Recommendation
   ↓
Repeat Tomorrow
```

An AI-assisted recurring workflow could look like:

```text
Schedule
   ↓
Gather Context
   ↓
Query Governed Data
   ↓
Apply Business Rules
   ↓
Analyze
   ↓
Generate Summary
   ↓
Return Result
```

Create a recurring-analysis prompt:

```text
Every weekday morning:

Review MRI order demand, utilization and unused capacity
across all facilities.

Identify facilities above 90% utilization where demand exceeds
available slots.

Before recommending overtime, determine whether another
facility has at least 15% available capacity.

For each affected facility provide:

1. Capacity issue
2. Demand level
3. Available alternative capacity
4. Recommended action
5. Expected financial impact
```

This is the point where Genie begins to feel less like a chatbot and more like a workflow participant.

---

# Part 9 — Genie One MCP

## Time: 5 minutes

This may be the most strategically significant part of the lesson.

Show this architecture:

```text
                       Genie Ontology
                             │
                             ▼

                       Genie One
                             │
                             ▼

                        MCP Service
                             │

          ┌──────────────────┼──────────────────┐
          │                  │                  │
          ▼                  ▼                  ▼

       ChatGPT             Claude             Cursor
          │                  │                  │
          └──────────────────┼──────────────────┘
                             │
                             ▼

                       Business User
```

The important idea is:

> The user may not have to leave their preferred AI assistant.

Instead, Genie can become a governed provider of enterprise context.

For example, an external AI assistant could ask:

```text
What caused yesterday's MRI backlog?
```

The workflow might conceptually become:

```text
External Assistant
        ↓
Genie One MCP
        ↓
Business Context
        ↓
Relevant Governed Data
        ↓
Generated Query
        ↓
Execution
        ↓
Grounded Result
        ↓
External Assistant
```

This means organizations can potentially separate:

## The AI interface

```text
ChatGPT
Claude
Cursor
Other Agents
```

from:

## The enterprise context layer

```text
Genie
Unity Catalog
Business Semantics
Metrics
Permissions
Governance
```

That is a major architectural shift.

---

# Architecture Summary

Use this as one of your main teaching slides.

```text
                     ┌─────────────────────┐
                     │        USERS        │
                     └──────────┬──────────┘
                                │

              ┌─────────────────┼─────────────────┐
              │                 │                 │
              ▼                 ▼                 ▼

          Genie One          ChatGPT           Claude
              │                 │                 │
              │            Genie One MCP         │
              │                 │                 │
              └─────────────────┼─────────────────┘
                                │
                                ▼

                        GENIE ONTOLOGY

                                │

              ┌─────────────────┼─────────────────┐
              │                 │                 │
              ▼                 ▼                 ▼

        Metric Views        Documents       Definitions

              │                 │                 │
              └─────────────────┼─────────────────┘
                                │
                                ▼

                         UNITY CATALOG

                                │
                                ▼

                    Governed Enterprise Data
```

A useful teaching summary is:

```text
Models provide intelligence.

Ontology provides meaning.

Unity Catalog provides governance.

MCP provides reach.

Scheduled workflows provide action.
```

---

# Can This Be Demonstrated in Databricks Free Edition?

## Mostly—but do not make every capability mandatory for the lab

Design the class so the foundational demo works even if the newest enterprise capabilities are unavailable.

| Capability | Recommended Classroom Approach |
|---|---|
| Create sample tables | ✅ Hands-on |
| SQL queries | ✅ Hands-on |
| Notebook analysis | ✅ Hands-on |
| Unity Catalog concepts | ✅ Hands-on / discussion |
| Genie natural-language questions | ✅ Try hands-on |
| AI/BI dashboards | ✅ Good hands-on activity |
| Genie Code | ✅ Try hands-on, subject to usage limits |
| Genie Ontology | 🟡 Demonstrate if available |
| File upload into Genie | 🟡 Demonstrate if enabled |
| Workspace instructions | 🟡 May require workspace/admin capabilities |
| Scheduled Genie tasks | 🟡 Demonstrate if exposed |
| Enterprise identity scenarios | ❌ Not appropriate for a Free Edition lab |
| Full enterprise permission architecture | ❌ Better as instructor discussion |
| Genie One MCP | 🟡 Best treated as instructor demo or architecture discussion |
| MCP into ChatGPT/Claude | 🟡 Do not make this required for students |

A good structure is therefore:

```text
FREE EDITION LAB
        +
INSTRUCTOR DEMO
        +
ENTERPRISE ARCHITECTURE DISCUSSION
```

---

# Recommended 35-Minute Class

| Time | Activity |
|---:|---|
| 0–3 min | Show the Genie One announcement and explain the AI coworker shift |
| 3–7 min | Build the Radiology dataset |
| 7–11 min | Ask basic Genie questions |
| 11–16 min | Introduce business semantics and ontology |
| 16–20 min | Add organizational instructions |
| 20–24 min | Add a policy document |
| 24–28 min | Explain Unity Catalog governance |
| 28–31 min | Design a recurring workflow |
| 31–35 min | Reveal the Genie One MCP architecture |
| 35–40 min | Enterprise AI discussion |

---

# Recommended Demo Prompt Sequence

Do not demonstrate the features independently.

Use one question that becomes progressively more sophisticated.

## Prompt 1 — Basic Analytics

```text
Show MRI demand by facility.
```

---

## Prompt 2 — Interpretation

```text
Which facility appears constrained?
```

---

## Prompt 3 — Decision

```text
Which facility should receive overtime?
```

---

## Prompt 4 — Add Business Semantics

```text
Use our organization's definition of high utilization and
capacity gap.

Which facility requires intervention?
```

---

## Prompt 5 — Add Policy

```text
Apply the attached Radiology Capacity Policy.

Should overtime be authorized?
```

---

## Prompt 6 — Add Cross-Facility Reasoning

```text
Before recommending overtime, determine whether another
facility has available capacity.
```

---

## Prompt 7 — Produce a Business Artifact

```text
Produce a short operational briefing explaining:

- the recommendation
- supporting metrics
- financial impact
- alternative options
- assumptions
```

---

## Prompt 8 — Turn It Into Work

```text
Perform this analysis every weekday morning and identify
the three most important operational actions.
```

The progression becomes:

```text
QUESTION
   ↓
DATA
   ↓
SEMANTICS
   ↓
DOCUMENTS
   ↓
RULES
   ↓
GOVERNANCE
   ↓
REASONING
   ↓
RECURRING ACTION
```

That is the real lesson.

---

# Traditional Genie vs Emerging Genie One

| Traditional Mental Model | Emerging Genie One Model |
|---|---|
| Ask a question | Delegate an analytical task |
| Generate SQL | Understand business context |
| Query a table | Discover governed enterprise data |
| Return an answer | Apply organizational rules |
| Visualize metrics | Combine data and documents |
| Human repeats analysis | Schedule recurring analysis |
| Genie is the interface | Genie can become a context service |
| User must work inside Genie | External agents may access Genie through MCP |

---

# The Strategic Question for Students

Finish the class with:

## Who owns the enterprise AI advantage?

### Is it the model provider?

```text
GPT
Claude
Gemini
Other Models
```

Or is it the organization that owns:

```text
Definitions
Relationships
Metrics
Documents
Lineage
Permissions
Policies
Workflows
Historical Context
```

Then pose the argument:

> Foundation models will continue improving rapidly.
>
> For many enterprise workloads, models may also become increasingly interchangeable.
>
> What is far harder to replace is the accumulated context of the organization.

That leads to the central takeaway.

# The Moat May Not Be the Model

## The moat may be governed enterprise context.

And that helps explain why Genie One, Genie Ontology, Unity Catalog, scheduled work, and MCP belong in the same strategic conversation.

The bigger architecture is becoming:

```text
                      AI EXPERIENCE
                           │
                           ▼

              ChatGPT / Claude / Genie
                           │
                           ▼

                    AGENT / MCP LAYER
                           │
                           ▼

                 ENTERPRISE CONTEXT
                           │
                           ▼

                     ONTOLOGY
                           │
                           ▼

                    UNITY CATALOG
                           │
                           ▼

                  GOVERNED DATA
```

The important evolution is therefore not simply:

```text
Better chatbot
```

It is:

```text
AI
+
Enterprise Meaning
+
Governed Data
+
Organizational Knowledge
+
Workflow
```

## Final Message

> **Yesterday:**  
> AI answered questions about enterprise data.
>
> **Today:**  
> AI increasingly understands enterprise context.
>
> **Tomorrow:**  
> AI participates in governed enterprise workflows.

And that is why Genie One is becoming much more interesting than just another natural-language analytics interface.
