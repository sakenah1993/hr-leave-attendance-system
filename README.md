<div align="center">

# HR Leave & Attendance Management System

### Leave requests · Policy-driven balances · Attendance · Approvals

An Arabic-first (RTL) web platform that runs the full leave and attendance lifecycle for a multi-company workforce, from the employee's request to the final executive decision.

![React](https://img.shields.io/badge/React-20232A?style=flat&logo=react&logoColor=61DAFB)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat&logo=typescript&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=nodedotjs&logoColor=white)
![Express](https://img.shields.io/badge/Express-000000?style=flat&logo=express&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat&logo=postgresql&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-06B6D4?style=flat&logo=tailwindcss&logoColor=white)
![PWA](https://img.shields.io/badge/PWA-5A0FC8?style=flat&logo=pwa&logoColor=white)
![Status](https://img.shields.io/badge/Status-In_production-2ea44f?style=flat)

<img src="screenshots/02-employee-dashboard.png" alt="Employee dashboard" width="900">

</div>

---

## Contents

- [At a Glance](#at-a-glance)
- [The Problem](#the-problem)
- [Key Features](#key-features)
- [How a Request Flows](#how-a-request-flows)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Roles & Permissions](#roles--permissions)
- [Engineering Practices](#engineering-practices)
- [Screenshots](#screenshots)
- [My Role](#my-role)
- [Source Code](#source-code)

---

## At a Glance

| | |
|---|---|
| **Domain** | Human resources: leave, balances, attendance and approvals |
| **Users** | Employees, direct managers, site coordinators, HR officers, executives and IT |
| **Organization model** | Branches, external sites and multiple operating companies, each with its own leave policy |
| **Approval flow** | Up to 5 stages, depending on the employee's type and role |
| **Platforms** | Desktop web and installable mobile app (PWA) |
| **Language** | Arabic, right-to-left interface |
| **Environments** | Development → Staging → Production, with versioned releases |

---

## The Problem

The organization places its employees with several **operating companies**, and each company has its own leave policy: annual entitlement, monthly accrual, probation period, carry-over rules, and when the leave year ends. Employees also work from **branches** and **external sites**, each with its own managers, working hours and attendance location.

All of this was handled with spreadsheets and paper forms. Balances were calculated by hand, approvals were tracked in messages, and every report took manual effort.

**This system replaces all of that with one platform.** Employees request leave and track it live, approvers act in a defined flow, balances follow each company's policy automatically, attendance is recorded by location, and management exports the reports it needs at any time.

---

## Key Features

<table>
<tr>
<td width="50%" valign="top">

### Leave management
- Leave requests with a multi-stage approval flow
- 12 leave types, each with its own rules (annual, sick, emergency, maternity, marriage, bereavement and more)
- Digital signatures at each approval stage
- Printable leave forms with the operating company's logo
- On mobile, forms open inside the app and can be shared or saved as an A4 PDF

</td>
<td width="50%" valign="top">

### Policy-driven balances
- Monthly accrual based on each employee's own contract date
- Higher accrual rates by policy (e.g. engineers, 5+ years of service)
- Leave year by contract year, calendar year or split calendar year
- Carry-over: none, capped or unlimited, with an optional expiry window
- Advance reminders before a reset or before carried days expire

</td>
</tr>
<tr>
<td valign="top">

### Data quality and control
- **Policy review:** each new company stays flagged until its HR officer confirms its policy
- **Balance confirmation:** each new employee's opening balance is confirmed once by HR, then locked
- No accrual or reset before confirmation, then automatic catch-up in order
- Escalation to management after 5 working days without confirmation
- **Balance wallet** for authorized adjustments, always with a reason

</td>
<td valign="top">

### Attendance
- GPS geofenced check-in and check-out per branch or site
- Custom working hours, grace periods and lateness tracking
- Attendance permissions and field missions with approval
- Daily view, monthly grid and monthly timesheets
- Timesheet approval by the site coordinator

</td>
</tr>
<tr>
<td valign="top">

### Organization management
- Companies, branches, departments and external sites
- **Company assignment:** each company is assigned to HR officers, who see only their companies
- Transfers between departments, sites and companies, with history
- Bulk import of site employees from Excel, with a preview before saving

</td>
<td valign="top">

### Reporting and audit
- **Periodic reports:** executive summary and monthly activity by month or year
- Excel exports: timesheets, attendance and employee lists by branch or site
- **Leave-balance audit:** monthly movement of every employee's balance
- **Activity log:** who did what and when, with before and after values
- In-app and Web Push notifications, with badges for pending actions

</td>
</tr>
</table>

---

## How a Request Flows

```mermaid
flowchart LR
    E([Employee<br/>submits request]) --> M{Direct manager<br/>or site coordinator}
    M -- approves --> H{HR officer<br/>of the company}
    M -- rejects --> R([Rejected<br/>with reason])
    H -- approves --> X{Executive<br/>approval}
    H -- rejects --> R
    X -- approves --> A([Approved<br/>balance deducted · form issued])
    X -- rejects --> R
```

The exact path depends on the employee. Internal staff skip stages that don't apply to them, and site staff go through their site coordinator first. Every decision is recorded with its signature, notes and time, and the employee follows each stage live.

---

## Architecture

```mermaid
flowchart LR
    subgraph Clients
        W[Web browser<br/>desktop]
        P[Installed PWA<br/>mobile]
    end

    subgraph Server[Application server]
        API[REST API<br/>Express + TypeScript]
        AUTH[Authentication &<br/>role-based permissions]
        JOBS[Scheduled jobs<br/>accrual · leave-year rules ·<br/>reminders · escalations]
    end

    DB[(PostgreSQL<br/>Drizzle ORM)]
    PUSH[Web Push]

    W --> API
    P --> API
    API --> AUTH
    API --> DB
    JOBS --> DB
    JOBS --> PUSH
    API --> PUSH
    PUSH --> P
```

---

## Tech Stack

| Layer | Technologies |
|---|---|
| **Frontend** | React, TypeScript, Vite, Tailwind CSS, shadcn/ui (Radix), TanStack Query, Wouter |
| **Backend** | Node.js, Express, TypeScript |
| **Database** | PostgreSQL (serverless), Drizzle ORM |
| **Documents** | ExcelJS for Excel reports, html2canvas + jsPDF for mobile PDFs, print-ready HTML forms |
| **Platform** | Progressive Web App, Web Push notifications, Geolocation API |
| **Tooling** | pnpm monorepo, shared typed API client, Git-based release pipeline |

---

## Roles & Permissions

| Role | Responsibilities in the system |
|---|---|
| **Employee** | Request leave and permissions, check in and out, follow requests, view balance and forms |
| **Direct manager** | First decision on the team's requests, team attendance and permissions |
| **Site coordinator** | Approves requests and monthly timesheets for staff at an external site |
| **Site manager** | Oversees the site team and its coordinator |
| **HR officer** | Manages employees of assigned companies, reviews policies, confirms new balances, processes requests |
| **HR lead** | Assigns companies to HR officers, manages companies, adjusts balances, handles escalations |
| **Executive** | Final approval, executive overview, reports and employee exports |
| **IT / system admin** | Accounts, sites and branches, data imports, activity log and system settings |

Permissions are enforced **on the server** for every action, not just hidden in the interface.

---

## Engineering Practices

- **Release pipeline:** every change goes through development and staging before production. Production releases need explicit approval, and each one gets a version number and a changelog entry.
- **Data integrity:** production data changes start with a full backup and a preview of their effect, and are applied only after approval.
- **Auditability:** balance changes always require a reason and appear in the employee's monthly movement. Sensitive actions are recorded in the activity log with before and after values.
- **Safe policy changes:** changing a company's leave-year rule takes effect from the next year and never wipes current balances.
- **Testing:** critical flows (balance confirmation, approvals, exports, mobile forms) are verified with scripted API and browser checks on a local copy before release.
- **Communication:** weekly change reports for management and formal change notices for the quality department.

---

## Screenshots

> All screenshots come from a **demo environment with fictional data**. No real employee, company or personal information is shown, and the organization's name and logo are blurred.

### Employee

<table>
<tr>
<td width="50%"><b>Leave request</b><br/><img src="screenshots/03-leave-request.png" alt="Leave request"></td>
<td width="50%"><b>My leave balance</b><br/><img src="screenshots/04-leave-balance.png" alt="Leave balance"></td>
</tr>
</table>

### Managers and sites

<table>
<tr>
<td width="50%"><b>Direct manager: requests awaiting decision</b><br/><img src="screenshots/17-manager-dashboard.png" alt="Manager dashboard"></td>
<td width="50%"><b>Site coordinator: monthly timesheet approval</b><br/><img src="screenshots/20-site-timesheet.png" alt="Timesheet approval"></td>
</tr>
<tr>
<td><b>Attendance</b><br/><img src="screenshots/08-attendance.png" alt="Attendance"></td>
<td></td>
</tr>
</table>

### HR

<table>
<tr>
<td width="50%"><b>HR dashboard</b><br/><img src="screenshots/05-hr-dashboard.png" alt="HR dashboard"></td>
<td width="50%"><b>New-employee balance confirmation</b><br/><img src="screenshots/06-balance-confirmation.png" alt="Balance confirmation"></td>
</tr>
<tr>
<td><b>Companies and leave policies</b><br/><img src="screenshots/07-companies-policies.png" alt="Companies and policies"></td>
<td><b>Company assignment to HR officers</b><br/><img src="screenshots/13-company-assignment.png" alt="Company assignment"></td>
</tr>
<tr>
<td><b>Leave-balance audit</b><br/><img src="screenshots/21-leave-audit.png" alt="Leave-balance audit"></td>
<td><b>Periodic reports</b><br/><img src="screenshots/15-periodic-reports.png" alt="Periodic reports"></td>
</tr>
</table>

### Executive

<table>
<tr>
<td width="50%"><b>Final decision with signature</b><br/><img src="screenshots/16-executive-decision.png" alt="Executive decision"></td>
<td width="50%"><b>Employee list export (by branch or site)</b><br/><img src="screenshots/10-employees-export.png" alt="Employee export"></td>
</tr>
</table>

### IT and audit

<table>
<tr>
<td width="50%"><b>IT dashboard: accounts and access</b><br/><img src="screenshots/19-it-dashboard.png" alt="IT dashboard"></td>
<td width="50%"><b>Activity log</b><br/><img src="screenshots/22-activity-log.png" alt="Activity log"></td>
</tr>
<tr>
<td><b>Balance wallet (add or deduct days with a reason)</b><br/><img src="screenshots/14-balance-wallet.png" alt="Balance wallet"></td>
<td></td>
</tr>
</table>

### Mobile

<table>
<tr>
<td width="50%" align="center"><b>Employee dashboard</b><br/><img src="screenshots/11-mobile-dashboard.png" alt="Mobile dashboard" width="300"></td>
<td width="50%" align="center"><b>Leave form inside the app</b><br/><img src="screenshots/12-mobile-form.png" alt="Mobile form" width="300"></td>
</tr>
</table>

---

## My Role

**Software Engineer · NES Global (contract) · placed with a non-profit client · 2026 – Present**

Employed by NES Global and assigned on-site to the client organization, I own the platform end to end, from understanding the business to running it in production.

- **Requirements and analysis:** I work directly with HR, management and site managers, and turn each operating company's leave policy into accrual, carry-over and leave-year rules.
- **Design and development:** I build features across the frontend, backend and database, using AI-assisted development (Claude Code) to deliver quickly while keeping the system consistent.
- **Quality and releases:** I test every change in development and staging and manage versioned releases to production.
- **Data migration and integrity:** I import employee and balance data from spreadsheets, validate it against company policies, and protect every production change with backups and previews.
- **Training and support:** I train the HR team on new features and support users day to day.
- **Reporting:** I write weekly change reports for management and formal change notices for the quality department.

---

## Source Code

The source code is **private**, as this is an internal system of the organization. It is available for review on request.
