# AI_AG_1
أنت محق! اعتذر عن التغيير في الترتيب. إليك ملف README بنفس ترتيب المستند الأصلي تماماً:

---

# Agentic AI Engineering Program Agentic HR System

**Team Members:** Ghaida Turki Aljahmi | Aeshah Aloraini | Shahad Busaleh | Amal Albaraiki

---

## PROJECT: Agentic HR System

### Project Overview

Human Resources departments frequently handle simple daily requests, such as checking remaining leave balances, understanding internal policies, and submitting limited administrative requests. Although each request is simple on its own, the repeated volume of these requests can delay employee responses and take up the HR team's time with tasks that could be partially automated.

The challenge is not only about providing a text-based answer. Some requests require access to personal data, others require checking company policies, and some may result in actual changes to stored data. Therefore, using a single agent with full access and permissions increases the risk of unnecessary data access or unauthorized actions.

This project addresses the issue by separating responsibilities and permissions across specialized agents, supported by an independent security layer.

### Problem Statement

HR requests often require checking employee data, policies, and eligibility rules. Manual processing can be slow and inconsistent, while standard chatbots may respond without verification. Our three-agent system separates data handling, policy analysis, and final review. Actions are recorded as proposals and checked by a security layer; exceptions require human approval before execution.

### Target Users

- HR employees who handle routine employee requests and information checks
- HR specialists and consultants who need policy-based analysis
- HR managers who supervise decisions and ensure policy compliance
- Employees who interact directly with agents

### Objectives / Goals

- Automate routine HR requests
- Reduce HR workload and improve response time
- Provide accurate policy-based answers
- Protect sensitive employee data and actions
- Require human approval for high-risk requests
- Maintain clear logs for decisions and actions

---

## 1. Manager Agent — Understanding & Planning

### Function:
- Understand the employee's request
- Convert the request into structured JSON
- Identify the intent and entities
- Determine which agents are required
- Decide the order in which agents should be called
- Synthesize the results using explicit logical rules

### Tools:
- LLM API + Structured Output — Extract intent, entities, and required agents
- Pydantic — Validate the JSON structure and inputs/outputs
- Python — Orchestrate the workflow and sequence between agents without LangGraph
- Logging — Record decisions, steps, and results

---

## 2. HR Execution Agent — Tool Execution

### Function:
- Access the employee database
- Execute HR operations such as checking leave balance, submitting leave requests, and updating employee information
- No actual changes are executed until the request passes through the Security Layer

### Tools:
- SQLite — Store employees and leave data
- Pydantic — Validate operation inputs
- FastAPI — Expose the tools through an API layer
- Python — Execute database operations and business logic
- Logging — Record proposed and executed actions

---

## 3. Consultant Agent — RAG

### Function:
- Answer questions about company policies
- Search only within the HR policy document
- Has no access to employee data
- Returns the general policy rule only

### Tools:
- LangChain — Manage the complete RAG pipeline
- LangChain Text Splitter — Split the policy document into chunks
- FAISS — Store embeddings and perform similarity search
- Sentence Transformers / OpenAI Embeddings — Convert text into embeddings
- LLM API — Generate answers based only on the retrieved context

---

## 4. Security Layer — Decision & Human Approval

**Important:** This is not a fourth agent. It is a security layer between the Manager/HR agents and execution.

### Function:
- Determine the risk level of an action
- Decide whether the action is:
  - Low Risk: Automatic Execution
  - Sensitive/High Risk: Human Approval Required
- Prevent sensitive actions from being executed without approval
- Log all decisions and actions

### Tools:
- Python if/else Rules — Classify risk levels using predefined rules
- SQLite — Store pending approvals audit log
- Streamlit — Provide an Approve/Reject interface for the human approver
- Python — Apply security rules and verify approval status

---

## Workflow

In this project, the workflow explains how an employee request is received, classified by the Manager Agent, handled by the appropriate agent, checked by the Security Layer, and completed with or without human approval.

| Step | Agent collaboration and human interaction | Tools |
|------|-------------------------------------------|-------|
| 1 | The employee submits a request. The Manager validates the request and asks the employee for clarification if required information is missing; no tool or action runs until the request is complete. | Streamlit, FastAPI, LLM API, Structured Output, Pydantic, Logging |
| 2 | The Manager extracts intent and entities into structured JSON, selects the required agent(s), and determines their execution order. | LLM API, Pydantic, Python orchestration, Logging |
| 3 | The Manager routes to the HR Execution Agent, the Consultant Agent, or both in parallel. HR reads only the authenticated employee's data or creates a proposed action. The Consultant searches only approved policy documents and has no employee-data access. | HR: FastAPI, SQLite, Python, Pydantic; Policy: LangChain, Text Splitter, Embeddings, FAISS, LLM API |
| 4 | Both agents return structured results to the Manager. The Manager checks completeness and consistency, applies explicit logical rules, and prepares either a verified information response or an action proposal. | Pydantic, explicit Python rules, Logging |
| 5 | Information-only requests return directly to the employee. Action requests are first stored as proposed_action and sent to the Security Layer for identity, ownership, permission, action-type, and risk checks. | Python if/else rules, Pydantic, SQLite, Logging |
| 6 | A compliant low-risk action is authorized and executed by the HR Execution Agent. A sensitive, high-risk, or exception request is stored in pending_approvals and pauses for an HR human decision. | FastAPI, SQLite, Python, Streamlit approval interface |
| 7 | The HR approver selects Approve or Reject. Approval returns the request for execution; rejection produces no data change. The Manager sends the final status to the employee. | Streamlit, FastAPI, SQLite, Logging |
| 8 | Every agent decision, tool call, security result, approval/rejection, error, and final result is written to audit_log for traceability. | SQLite audit_log, Python Logging |

**Human checkpoint 1** — Employee clarification: The Manager asks the employee when dates, request type, or required fields are missing.

**Human checkpoint 2** — HR approval: Sensitive or exception requests cannot execute until an authorized HR reviewer approves them.

**Key control:** The Security Layer is not a fourth agent. It is a deterministic control layer placed before execution.

---

## Project Timeline (5 Weeks)

| Week | Activities |
|------|------------|
| Week 1 | Project planning and requirements gathering |
| Week 2 | Database setup and agent development |
| Week 3 | RAG implementation and integration |
| Week 4 | Security layer and approval interface |
| Week 5 | Testing, deployment, and documentation |

---

## Data Source

- Synthetic Internal HR Policy Manual (Unpublished project document)
- Saudi HR & Workforce Dataset 2025-2026

---

## References

1. Bureau of Experts at the Council of Ministers. (n.d.). *Saudi Labor Law*. Kingdom of Saudi Arabia. https://laws.boe.gov.sa/Boelaws/Laws/LawDetails/08381293638848e28ad2a9a700f2aa94/1

2. Saudi Data and Artificial Intelligence Authority. (n.d.). *Personal Data Protection Law*. https://dgp.sdai.gov.sa/wps/portal/pdp/knowledgecenter/details/PDPL

3. Ministry of Human Resources and Social Development. (n.d.). *Remote Work and HR Services*. https://www.hrsd.gov.sa/individuals

4. Prime Levels. (2025). *Saudi HR & Workforce Dataset 2025-2026*. https://prime-levels.com/ar/data-hub/saudi-hr-workforce-2025-2026

5. Agentic HR Project Team. (2026). *Synthetic Internal HR Policy Manual*. Unpublished project document.

6. Saudi HR & Workforce Dataset 2025-2026 | 4 Linked Tables | Prime Levels
