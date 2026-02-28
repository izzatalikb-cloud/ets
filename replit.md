# TechCorp ERP System

## Overview

TechCorp ERP is a full-stack enterprise resource planning application designed for comprehensive business management. It includes modules for project management, operations tracking, financial management (invoices, bills, accounting), human resources, procurement, and internal team communication. The system features a modern, responsive UI and supports real-time data operations via REST APIs. The project aims to provide a robust, production-ready solution for managing enterprise resources efficiently, with a focus on comprehensive archive functionality, manager-controlled task workflows, and an enhanced real-time notification system.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture
- **Framework**: React 18 with TypeScript and Vite
- **UI Library**: shadcn/ui built on Radix UI
- **Styling**: Tailwind CSS with CSS variables
- **State Management**: TanStack Query for server state and API caching
- **Routing**: Wouter
- **Forms**: React Hook Form with Zod validation
- **Charts**: Recharts

### Backend Architecture
- **Runtime**: Node.js with Express.js
- **Language**: TypeScript
- **API Design**: RESTful API
- **Development Server**: Vite middleware integration

### Data Layer
- **Database**: PostgreSQL with Drizzle ORM
- **Schema**: Centralized definitions using Drizzle and Zod
- **Migrations**: Drizzle Kit
- **Validation**: Zod schemas for runtime type validation

### Authentication & Authorization
- **Session Management**: Express sessions with PostgreSQL store
- **User Roles**: Role-based access control (employee/manager/CEO)
- **Branch-Based Access Control**: Multi-tenant data isolation with CEO override
- **Security**: OWASP Top 10 2021 compliant implementation, including rate limiting, CSRF protection, security headers, password validation, and security event logging.

### Project Structure
- **Monorepo Layout**: Client, server, and shared code
- **Shared Types**: Common TypeScript interfaces and Zod schemas
- **Component Organization**: Feature-based with reusable UI components
- **API Routes**: RESTful endpoints organized by business domain

### Key Features and Design Decisions
- **Comprehensive Archive Functionality**: Soft-delete with restore across key modules (Projects, Tasks, Operations, Procurement, Quotations, Invoices) with role-based access and cascade archiving for related entities.
- **Project Manager-Controlled Task Workflow**: Complete task lifecycle (unassigned → assigned → in_progress → submitted → completed). Only the designated project manager (stored in project.managerId) or CEO can create, assign, and reassign tasks within their projects. Mandatory comments required for task submission. All task operations require a valid project association.
- **Real-time Notification System**: Enhanced notifications for task assignments, project changes, and approvals, with real-time updates across the system.
- **Quotation-to-Invoice Conversion**: Seamless conversion of CEO-endorsed quotations to invoices, including branch-aware numbering and role-based authorization.
- **Team Chat System**: Real-time internal communication with pre-configured conversations and notification integration (all participants receive notifications when a new message is sent).
- **Redesigned Quotation & Invoice Pipeline**: Visual workflow redesign with multi-stage approval processes (Draft → Technical Manager → Account Manager → CEO Endorsement → Sent to Client → Accepted/Rejected).
- **Finance & Accounting Reorganization**: Separation into "Finance Centre" (strategic analytics, dashboards, quotation pipeline) and "Accounting" (core operations like Chart of Accounts, Journal Entries, Bills).
- **4-Stage Approval Workflow**: Implemented for invoices, bills, and journal entries (Employee → Technical Manager → Account Manager → CEO) with role-based authorization and notification system.
- **Invoice Ticketing Workflow System** (New December 2025): Transformed the invoice approval process into a support-ticket-style system with:
  - **Explicit Assignment**: Each invoice is assigned to a specific user at each approval stage, ensuring clear ownership and accountability.
  - **Stage Tracking**: New fields `currentStage` and `currentAssigneeId` on invoices track the workflow state (draft → technical_review → finance_review → ceo_approval → ready_to_send → sent).
  - **Stage History**: Complete audit trail in `invoice_stage_history` table tracking all transitions with timestamps, performers, assignees, and comments.
  - **Ownership Enforcement**: Only the currently assigned approver (or CEO override) can approve, reject, or request rework on an invoice.
  - **Automatic Hand-offs**: When an approver takes action, the system automatically assigns the invoice to the next appropriate person and sends them a notification.
  - **Rework Flow**: New `/api/invoices/:id/rework` endpoint allows returning an invoice to a previous stage with comments.
  - **UI Updates**: ApprovalWorkflowTracker component shows current assignee and collapsible stage history with all transitions.
- **UI/UX Decisions**: Modern, responsive design; consistent currency formatting (AED); enhanced layouts for Vendors and HR pages; card-based designs for workflows.

## External Dependencies

### Database & ORM
- **PostgreSQL**: Primary database
- **Drizzle ORM**: Type-safe database queries and migrations
- **@neondatabase/serverless**: PostgreSQL driver

### UI & Styling
- **Radix UI**: Accessible component primitives
- **Tailwind CSS**: Utility-first CSS framework
- **Lucide React**: Icon library
- **Recharts**: Chart library

### Form & Validation
- **React Hook Form**: Performant form library
- **Zod**: Schema validation library
- **@hookform/resolvers**: Integration with Zod

### State & Data Fetching
- **TanStack Query**: Server state management
- **Date-fns**: Date utility library

### Development Tools
- **Vite**: Fast build tool and development server
- **ESBuild**: Fast JavaScript bundler
- **TypeScript**: Static type checking

### Security Packages
- **Helmet**: Security headers middleware
- **Express Rate Limit**: Brute-force protection
- **bcrypt**: Password hashing
- **csurf**: CSRF token generation and validation

## Test Data (Seeded December 2025)

The system has been seeded with comprehensive test data to demonstrate all workflows:

### Users (8 total)
- 1 CEO (Ammar Ali) - Full access to both branches
- 4 Management (2 telecom, 2 petroleum) - Branch-specific management access
- 3 Employees (2 telecom, 1 petroleum) - Task execution and data entry

### CRM & Customers (7 customers)
- 3 Telecom branch: Etisalat UAE, Du Telecom, Ooredoo Qatar
- 3 Petroleum branch: ADNOC, Emirates Petroleum, Gulf Oil Industries
- 1 existing: gvgv

### Projects & Tasks
- 7 projects with 35 tasks total (5 tasks per project)
- Task status distribution: 7 unassigned, 7 assigned, 7 in_progress, 7 submitted, 7 completed
- Demonstrates full task workflow: assignment, progress tracking, submission with comments, completion

### Finance Pipeline
- 6 Quotations: draft, pending_technical, pending_accounts, pending_ceo, sent_to_client, accepted
- 7 Invoices: draft, pending_technical, pending_accounts, pending_ceo, sent, paid, overdue
- Demonstrates full 4-stage approval workflow

### Accounting
- 8 Chart of Accounts entries
- 5 Journal Entries: draft through posted stages
- 6 Bills: draft through paid stages

### HR
- 4 Employees with leave balances
- 4 Leave Requests: pending and approved

### Procurement
- 11 Vendors across both branches

## Login Credentials

| Username | Password | Role | Branch | Notes |
|----------|----------|------|--------|-------|
| ammar.ali | TechCorp2025!CEO | CEO | All | Full access |
| azzam.ali | TechCorp2025!TA1 | Management | Telecom | **Project Manager** for telecom projects |
| emad.ali | TechCorp2025!PA1 | Management | Petroleum | Project Manager for petroleum projects |
| ibrahim | TechCorp2025!PA2 | Management | Petroleum | |
| nosaiba.mohamed | TechCorp2025!TE1 | Employee | Telecom | |
| gezouli | TechCorp2025!PE1 | Employee | Petroleum | |