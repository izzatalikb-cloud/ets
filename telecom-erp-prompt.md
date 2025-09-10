# Telecom ERP System - Complete Replit Prompt

Copy and paste this entire prompt into Replit's AI assistant:

---

Create a comprehensive ERP system for a telecom company that provides Starlink technology services. The system should be a modern, responsive web application with the following specifications:

## Technology Stack
- Frontend: React with TypeScript
- Backend: Node.js with Express
- Database: PostgreSQL with Prisma ORM
- Authentication: JWT tokens
- UI Framework: Material-UI (MUI) with custom theming
- State Management: Redux Toolkit
- Real-time: Socket.io for notifications

## User Roles & Permissions
1. CEO - Full access with executive dashboard
2. Admin - System administration
3. Project Manager - Project and task management
4. Technician - Field operations and task execution
5. Accountant - Financial management
6. HR - Human resources management
7. Procurement - Purchase and supplier management
8. Account Manager - Client billing and invoicing

## Core Features

### 1. Authentication & Layout
- JWT-based login system
- Role-based access control
- Sidebar navigation (collapsible)
- Top header with notifications bell, user profile, and search bar
- Dark/light mode toggle
- Blue and white color scheme
- Mobile responsive design
- Multi-language support (English primary, Arabic RTL support)
- Currency support: Sudanese Pound (SDG) and UAE Dirham (AED)
- Date format: DD/MM/YYYY, GMT+2

### 2. Dashboard
- CEO Dashboard: Executive KPIs, revenue charts, project status overview, team performance
- Role-specific dashboards with relevant widgets
- Drag-and-drop customizable widgets
- Real-time data updates

### 3. Projects Section
Features:
- Project creation with templates
- Categories: Installation, Maintenance, Network Setup, Troubleshooting
- Project fields: Client, deadline, budget, location, priority, status
- Task management with branching workflows
- Task features: Due dates, priority, checklists, multiple assignees
- File attachments with version control
- Kanban board, Gantt chart, and list views (switchable)
- Project statuses: Planning, In Progress, On Hold, Completed, Cancelled
- Equipment tracking per project
- Travel time/distance logging
- Profitability tracking (revenue vs costs)
- Milestone tracking (25%, 50%, 75%, 100%)
- Task handover with optional notes, photos, completion forms
- Automatic notifications on task completion
- Task reassignment capabilities
- WhatsApp integration placeholder

### 4. Operations Section
Features:
- Service request management (calls, emails, equipment alerts)
- Technician scheduling calendar
- No double-booking prevention
- Site visit tracking with check-in/out
- Offline capability for field work
- Free-form service reports
- Automatic maintenance reminders
- Ticketing system for support
- SLA tracking and alerts
- Service-to-project conversion
- Customer feedback/ratings
- Integration with Starlink monitoring dashboard (placeholder)

### 5. Billing & Invoicing Section
Features:
- Invoice creation (manual and auto-generation from projects)
- Invoice types: Standard, Proforma, Recurring, Partial/Milestone
- Product/service catalog
- Client-specific pricing
- Multi-currency with manual exchange rate updates
- Payment tracking (full, partial, overpayment)
- Payment methods: Cash, bank transfer, check
- Automatic overdue reminders
- Bill management for expenses
- Expense categories by type and client
- Approval workflow for large amounts
- Invoice statuses: Draft, Sent, Viewed, Paid, Overdue, Cancelled
- Aging reports (30, 60, 90 days)
- Client portal for invoice viewing
- Auto-numbering system
- Outstanding amount dashboard

### 6. Accounting Section (Basic)
Features:
- Income and expense tracking
- Simple chart of accounts
- Bank account management
- Financial reports (P&L, Balance Sheet, Cash Flow)
- Budget management with alerts
- Project and client profitability
- Audit trail
- Period closing
- Export to Excel/PDF

### 7. Inventory Section
Features:
- Asset and resource management
- Stock levels with minimum alerts
- Asset distribution to projects
- Equipment check-in/out
- Barcode/QR code support
- Depreciation tracking
- Maintenance schedules

### 8. Procurement Section
Features:
- Purchase order creation
- Supplier management
- Bid creation and management
- Approval workflows
- Purchase-to-bill linking
- Vendor performance tracking
- RFQ management

### 9. Client Management Section
Features:
- 360-degree client view
- Project history
- Service history
- Invoice history
- Communication log
- Contract management
- Client portal access
- Document repository

### 10. HR Section
Features:
- Employee profiles
- Leave management
- Attendance tracking
- Document management
- Basic payroll tracking
- Performance reviews
- Training records
- Expense claims

### 11. Communication Features
- In-app messaging
- Email notifications
- SMS notifications (placeholder)
- AI assistant for navigation help
- Global search functionality
- Activity feed

### 12. Reports & Analytics
- Customizable dashboards
- Export to PDF/Excel
- Print-friendly views
- Real-time charts using Chart.js
- KPI tracking
- Scheduled reports

## Database Schema Requirements
- Multi-tenant architecture
- Audit logging for all changes
- Soft delete for data recovery
- Optimized for performance

## UI/UX Requirements
- Clean, spacious design
- Consistent spacing and typography
- Loading states and error handling
- Form validation
- Confirmation dialogs for critical actions
- Breadcrumb navigation
- Keyboard shortcuts
- Accessibility compliance (WCAG 2.1)

## Security Requirements
- Encrypted passwords (bcrypt)
- Session management
- Role-based API endpoints
- Input sanitization
- HTTPS enforcement
- Rate limiting

## File Structure
```
/src
  /components
    /common
    /dashboard
    /projects
    /operations
    /billing
    /accounting
    /inventory
    /procurement
    /clients
    /hr
  /pages
  /services
  /utils
  /store
  /hooks
  /types
/server
  /routes
  /controllers
  /models
  /middleware
  /utils
/prisma
  schema.prisma
  /migrations
```

## Sample Data
Include sample data for:
- 5 users (one of each role)
- 10 projects with tasks
- 20 clients
- 15 invoices
- 30 inventory items
- Sample service tickets

## Deployment Configuration
- Environment variables for configuration
- Docker support
- PM2 for process management
- Nginx configuration

Create this as a production-ready application with beautiful UI, smooth animations, and excellent user experience. Include proper error handling, loading states, and success notifications. The design should feel modern and professional, suitable for a technology company.

Make sure all features are functional and connected to the database. Use real-time updates where appropriate, especially for notifications and task assignments.

---

END OF PROMPT