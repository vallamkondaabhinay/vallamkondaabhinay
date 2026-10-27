# Employee Information Assistant

**By Abhinay Vallamkonda**  
**Portfolio update: 27 October 2026**  
**Status: development and validation in progress**

A case study of my work on a conversational employee-information workflow using n8n, structured JSON and PostgreSQL.

## What I am building

The goal is to turn a natural-language request into a structured employee-information action, check the required fields, and route sensitive changes through an approval process.

Instead of treating every sentence as an immediate database command, the workflow needs to distinguish between collecting information, validating a request, requesting approval and applying an approved change.

## My work and focus

- Designing a conversational flow for employee-information requests.
- Mapping natural-language inputs into structured fields.
- Working on mandatory-field checks and follow-up prompts for missing information.
- Validating email, mobile and date inputs before record creation.
- Developing request and approval handling.
- Testing workflow branches with JSON requests and reviewing responses.
- Investigating duplicate handling, optional-field storage and partial updates.

These describe work areas, not a claim that every path has passed end-to-end testing.

## Intended approval flow

1. An authorized requester submits an employee-creation request.
2. The workflow checks required information and asks for missing fields.
3. A complete request is stored as pending approval.
4. An authorized reviewer approves or rejects it.
5. Only an approved request should create the employee record.
6. The workflow returns a clear status to the requester.

The permission design also aims to restrict ordinary users to their own information and reserve approval or broader access for authorized roles. These permissions must be enforced using verified identity and server-side checks.

## Technology

| Component | Purpose |
| --- | --- |
| n8n | Workflow orchestration and request routing |
| Natural-language processing | Convert user sentences into structured requests |
| JSON and webhooks | Request and response format |
| PostgreSQL | Persistent records and request state |
| Postman | Exercise request scenarios during testing |

## Illustrative request

The following example is entirely synthetic and is not an execution log:

> Create an employee named Alex Example, employee ID DEMO-001, email alex@example.com, joining on 01-11-2026, with the job title Developer.

A request like this may need follow-up questions for other mandatory fields. It must not imply that approval has been granted or that a record has already been created.

## Validation priorities

| Area | What needs to be established |
| --- | --- |
| Required fields | Missing values produce clear follow-up questions |
| Dates and input formats | Invalid or ambiguous values are handled consistently |
| Approval | Pending and rejected requests do not create employee records |
| Identity and permissions | Access is based on verified identity, not client-supplied role claims |
| Duplicate prevention | Concurrent requests cannot create duplicate records |
| Reporting manager | Assignment resolves to an existing authorized record |
| Optional fields | Supported values are saved and retrieved correctly |
| Updates | Each supported field is tested end to end |
| Audit trail | Request, approval and record-change events remain traceable |

## Progress update — 27 October 2026

- Improved field-level validation feedback so rejected inputs can identify the field, explain the validation issue and suggest a correction.
- Tested invalid email and mobile inputs; the rejection path returned the expected validation response in the test performed.
- Reorganized the workflow layout for readability without intentionally changing workflow logic, queries or parameters.
- Reviewed database access boundaries. Write permissions for the production employee workflow were not available in the reviewed connection, so production writes were not enabled or tested.
- The workflow remains inactive for production use; these checks do not establish full end-to-end readiness.

## Current status and limitations

Implementation and testing are ongoing. This page does not certify production readiness or claim a completed test pass rate. Full authorization, approval-to-creation behaviour, duplicate prevention and supported field handling require end-to-end validation.

My next step is to document reproducible tests with synthetic data and explicit expected outcomes, then distinguish passed checks from remaining issues.

## What I am learning

- Successful workflow execution alone does not prove that the intended record was saved correctly.
- Request acceptance, pending approval and completed creation need different response states.
- Mandatory-field checks and permission checks solve different problems.
- Database constraints and transaction handling matter for concurrent requests.
- Test evidence should include persisted values and failure cases, not only a success message.

## Publication scope

This repository contains a high-level portfolio case study only. It does not include employer workflow exports, internal SQL, database schemas, endpoints, credentials, employee records or company-specific identifiers.
