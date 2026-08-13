# ServiceDesk-SN
ServiceNow scoped application for ServiceDesk integration.

## Purpose
This repository contains a custom ServiceNow application built to integrate with the ServiceDesk system. The application handles incident synchronization between the ServiceDesk platform and ServiceNow, and orchestrates the full incident lifecycle inside ServiceNow via Flow Designer.

## Features

### Data model
- Custom incident table (`servicedesk incident`) extending Task, with external numbering (SDINC prefix)
- Custom fields to map ServiceDesk data: external incident number, requester, category, subcategory
- Category and subcategory choice lists with dependency between them
- Priority matrix table (extends Data Lookup Matcher Rules) for automatic priority calculation based on Impact × Urgency, with the calculated Priority field made read-only via UI Policy
- Custom State Model (Open → In Progress → On Hold ↔ In Progress → Resolved/Rejected → Closed) with transition rules configured via App Engine Studio State Management
- Dictionary override on `state` (`default_close_state`, `default_work_state`, `close_states` attributes) so the platform's own close-state resolution (used by the OOB Task Closer business rule) matches the custom choice values instead of falling back to stale defaults

### Incident process (Flow Designer)
Orchestrating flow ("SD Incident Flow") calling separate flows/subflows per lifecycle phase:
- **Phase 1 — Pickup and Assignment**: 30-minute default-group assignment + notification, 60-minute manager escalation, if the incident stays unassigned
- **Phase 2 — In Progress Resolution**: branches on Resolved / On Hold / Rejected, with in-phase escalation if an incident sits in In Progress over an hour; recursively resumes itself after an On Hold cycle completes
- **Phase 3 — Exiting On Hold**: standalone triggered flow reacting specifically to a new customer reply in Additional comments (journal field), returns the incident to In Progress
- **Phase 4 — Auto-closure**: timed auto-close from Resolved/Rejected to Closed, called directly from Phase 2's terminal branches
- Reusable "Sync to External App (Stub)" subflow standing in for the real outbound integration call, logging the payload contract (`status`, `comment`, `timestamp`) until the backend endpoint exists
- Email notifications to the assignment group, group manager, requester, and assignee at each relevant transition

### Field validation & data integrity
- Business Rules enforcing required fields at key transitions: Assignment group + Assigned to before entering In Progress, Close notes before Resolved/Rejected — with matching UI Policies so the same fields show as mandatory in the form
- Actual start / Actual end timestamps set automatically (once, non-destructively) on first entry to In Progress and on resolution/rejection
- UI Policy making all fields read-only once an incident reaches Closed

### Navigation
- Application menu with modules for creating, viewing, filtering (Open/Closed) incidents
- "My Incidents" and "Assigned to me" modules, filtered dynamically by the current logged-in user

## Related Repositories
- [ServiceDesk-back](https://github.com/vasiliyserebrovskiy/ServiceDesk-back) — Spring Boot backend
- [ServiceDesk-front](https://github.com/vasiliyserebrovskiy/ServiceDesk-front) — React frontend

## Status
🚧 Work in progress. The full incident lifecycle (creation → assignment → resolution → auto-closure) is implemented and tested end-to-end within ServiceNow. Outbound sync to the external backend is currently a logging stub — real integration (endpoint + auth) is the next milestone. This file will be updated as development progresses.
