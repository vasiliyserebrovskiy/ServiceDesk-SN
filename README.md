# ServiceDesk-SN

ServiceNow scoped application for ServiceDesk integration.

## Purpose

This repository contains a custom ServiceNow application built to integrate with the ServiceDesk system. The application handles incident synchronization between the ServiceDesk platform and ServiceNow.

## Features

- Custom incident table (`servicedesk incident`) extending Task, with external numbering (SDINC prefix)
- Custom fields to map ServiceDesk data: external incident number, requester, category, subcategory
- Category and subcategory choice lists with dependency between them
- Priority matrix table (extends Data Lookup Matcher Rules) for automatic priority calculation based on Impact × Urgency
- UI Policy making the calculated Priority field read-only
- Business Rule deactivating an incident automatically when its state changes to Closed
- Application menu with modules for creating, viewing, filtering (Open/Closed) incidents

## Related Repositories

- [ServiceDesk-back](https://github.com/vasiliyserebrovskiy/ServiceDesk-back) — Spring Boot backend
- [ServiceDesk-front](https://github.com/vasiliyserebrovskiy/ServiceDesk-front) — React frontend

## Status

🚧 Work in progress. This file will be updated as development progresses.
