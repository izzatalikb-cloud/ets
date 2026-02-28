# Production Database Migration Guide

## Overview
This guide explains how to apply the latest schema changes to your production database to fix all current errors.

## What's Broken in Production

### Current Issues:
1. ❌ **Employee creation fails** - Missing `company` column
2. ❌ **Activities/Tasks fail** - `request_type` is required but shouldn't be
3. ❌ **Bids fail** - Same request_type issue
4. ❌ **Inventory fails** - Schema mismatch
5. ❌ **Customer deletion fails** - Schema mismatch

### Root Cause:
Production database schema is outdated. Development has the latest schema, but production doesn't.

## Migration File
- **File**: `migrations/0001_burly_joystick.sql`
- **Contains**: All SQL needed to sync production with development

## RECOMMENDED SOLUTION: Use Drizzle Migrate

### Prerequisites:
1. You need command-line access OR
2. Access to Replit Database UI with ability to run SQL

### Option A: Automatic Migration (Easiest)

**If you have access to run commands:**

```bash
# 1. Temporarily set DATABASE_URL to production
# (You'll need to get your production DATABASE_URL from Replit secrets)

# 2. Run the migration
npx drizzle-kit migrate

# 3. That's it! Production is now synced
```

### Option B: Manual SQL in Database UI

**Open Replit Database UI → Production → SQL Runner**

Then run this complete SQL (copy everything):

```sql
-- Create new tables that don't exist in production
CREATE TABLE IF NOT EXISTS "company_loans" (
        "id" varchar PRIMARY KEY DEFAULT gen_random_uuid() NOT NULL,
        "loan_number" text NOT NULL,
        "lender_name" text NOT NULL,
        "loan_type" text NOT NULL,
        "principal_amount" numeric(12, 2) NOT NULL,
        "outstanding_balance" numeric(12, 2) NOT NULL,
        "interest_rate" numeric(5, 2) NOT NULL,
        "currency" text DEFAULT 'AED' NOT NULL,
        "monthly_payment" numeric(12, 2) NOT NULL,
        "term_months" integer NOT NULL,
        "remaining_months" integer NOT NULL,
        "start_date" timestamp NOT NULL,
        "maturity_date" timestamp NOT NULL,
        "next_payment_date" timestamp NOT NULL,
        "last_payment_date" timestamp,
        "status" text DEFAULT 'active' NOT NULL,
        "purpose" text,
        "collateral" text,
        "notes" text,
        "created_by" varchar NOT NULL,
        "created_at" timestamp DEFAULT now(),
        "updated_at" timestamp DEFAULT now(),
        CONSTRAINT "company_loans_loan_number_unique" UNIQUE("loan_number")
);

CREATE TABLE IF NOT EXISTS "starlink_devices" (
        "id" varchar PRIMARY KEY DEFAULT gen_random_uuid() NOT NULL,
        "device_name" text NOT NULL,
        "serial_number" text,
        "kit_number" text,
        "location" text,
        "description" text,
        "status" text DEFAULT 'active' NOT NULL,
        "subscription_plan" text,
        "subscription_status" text DEFAULT 'active',
        "monthly_fee" numeric(10, 2),
        "next_billing_date" timestamp,
        "payment_gateway_reference" text,
        "installation_date" timestamp,
        "installed_by" varchar,
        "customer_id" varchar,
        "created_by" varchar,
        "created_at" timestamp DEFAULT now(),
        "updated_at" timestamp DEFAULT now()
);

-- Fix activities table (make request_type optional)
ALTER TABLE "activities" ALTER COLUMN "request_type" DROP NOT NULL;

-- Add company field to employees
ALTER TABLE "employees" ADD COLUMN IF NOT EXISTS "company" text DEFAULT 'All' NOT NULL;
ALTER TABLE "employees" ADD COLUMN IF NOT EXISTS "profile_picture_url" text;
ALTER TABLE "employees" ADD COLUMN IF NOT EXISTS "assigned_resources" text[] DEFAULT '{}';

-- Add supplier fields to inventory
ALTER TABLE "inventory" ADD COLUMN IF NOT EXISTS "supplier_contact_person" text;
ALTER TABLE "inventory" ADD COLUMN IF NOT EXISTS "supplier_email" text;
ALTER TABLE "inventory" ADD COLUMN IF NOT EXISTS "supplier_phone" text;
ALTER TABLE "inventory" ADD COLUMN IF NOT EXISTS "supplier_address" text;
ALTER TABLE "inventory" ADD COLUMN IF NOT EXISTS "assigned_to" varchar;

-- Add foreign key constraints
ALTER TABLE "company_loans" ADD CONSTRAINT "company_loans_created_by_users_id_fk" 
    FOREIGN KEY ("created_by") REFERENCES "public"."users"("id") ON DELETE no action ON UPDATE no action;

ALTER TABLE "starlink_devices" ADD CONSTRAINT "starlink_devices_installed_by_users_id_fk" 
    FOREIGN KEY ("installed_by") REFERENCES "public"."users"("id") ON DELETE no action ON UPDATE no action;

ALTER TABLE "starlink_devices" ADD CONSTRAINT "starlink_devices_customer_id_customers_id_fk" 
    FOREIGN KEY ("customer_id") REFERENCES "public"."customers"("id") ON DELETE no action ON UPDATE no action;

ALTER TABLE "starlink_devices" ADD CONSTRAINT "starlink_devices_created_by_users_id_fk" 
    FOREIGN KEY ("created_by") REFERENCES "public"."users"("id") ON DELETE no action ON UPDATE no action;

ALTER TABLE "inventory" ADD CONSTRAINT "inventory_assigned_to_employees_id_fk" 
    FOREIGN KEY ("assigned_to") REFERENCES "public"."employees"("id") ON DELETE set null ON UPDATE no action;
```

### Option C: Run SQL Commands One by One

If the full SQL above fails, run these commands individually:

#### 1. Create company_loans table
```sql
CREATE TABLE IF NOT EXISTS "company_loans" (
        "id" varchar PRIMARY KEY DEFAULT gen_random_uuid() NOT NULL,
        "loan_number" text NOT NULL,
        "lender_name" text NOT NULL,
        "loan_type" text NOT NULL,
        "principal_amount" numeric(12, 2) NOT NULL,
        "outstanding_balance" numeric(12, 2) NOT NULL,
        "interest_rate" numeric(5, 2) NOT NULL,
        "currency" text DEFAULT 'AED' NOT NULL,
        "monthly_payment" numeric(12, 2) NOT NULL,
        "term_months" integer NOT NULL,
        "remaining_months" integer NOT NULL,
        "start_date" timestamp NOT NULL,
        "maturity_date" timestamp NOT NULL,
        "next_payment_date" timestamp NOT NULL,
        "last_payment_date" timestamp,
        "status" text DEFAULT 'active' NOT NULL,
        "purpose" text,
        "collateral" text,
        "notes" text,
        "created_by" varchar NOT NULL,
        "created_at" timestamp DEFAULT now(),
        "updated_at" timestamp DEFAULT now(),
        CONSTRAINT "company_loans_loan_number_unique" UNIQUE("loan_number")
);
```

#### 2. Create starlink_devices table
```sql
CREATE TABLE IF NOT EXISTS "starlink_devices" (
        "id" varchar PRIMARY KEY DEFAULT gen_random_uuid() NOT NULL,
        "device_name" text NOT NULL,
        "serial_number" text,
        "kit_number" text,
        "location" text,
        "description" text,
        "status" text DEFAULT 'active' NOT NULL,
        "subscription_plan" text,
        "subscription_status" text DEFAULT 'active',
        "monthly_fee" numeric(10, 2),
        "next_billing_date" timestamp,
        "payment_gateway_reference" text,
        "installation_date" timestamp,
        "installed_by" varchar,
        "customer_id" varchar,
        "created_by" varchar,
        "created_at" timestamp DEFAULT now(),
        "updated_at" timestamp DEFAULT now()
);
```

#### 3. Fix activities table
```sql
ALTER TABLE "activities" ALTER COLUMN "request_type" DROP NOT NULL;
```

#### 4. Update employees table
```sql
ALTER TABLE "employees" ADD COLUMN IF NOT EXISTS "company" text DEFAULT 'All' NOT NULL;
ALTER TABLE "employees" ADD COLUMN IF NOT EXISTS "profile_picture_url" text;
ALTER TABLE "employees" ADD COLUMN IF NOT EXISTS "assigned_resources" text[] DEFAULT '{}';
```

#### 5. Update inventory table
```sql
ALTER TABLE "inventory" ADD COLUMN IF NOT EXISTS "supplier_contact_person" text;
ALTER TABLE "inventory" ADD COLUMN IF NOT EXISTS "supplier_email" text;
ALTER TABLE "inventory" ADD COLUMN IF NOT EXISTS "supplier_phone" text;
ALTER TABLE "inventory" ADD COLUMN IF NOT EXISTS "supplier_address" text;
ALTER TABLE "inventory" ADD COLUMN IF NOT EXISTS "assigned_to" varchar;
```

#### 6. Add foreign key constraints
```sql
ALTER TABLE "company_loans" ADD CONSTRAINT "company_loans_created_by_users_id_fk" 
    FOREIGN KEY ("created_by") REFERENCES "public"."users"("id") ON DELETE no action ON UPDATE no action;

ALTER TABLE "starlink_devices" ADD CONSTRAINT "starlink_devices_installed_by_users_id_fk" 
    FOREIGN KEY ("installed_by") REFERENCES "public"."users"("id") ON DELETE no action ON UPDATE no action;

ALTER TABLE "starlink_devices" ADD CONSTRAINT "starlink_devices_customer_id_customers_id_fk" 
    FOREIGN KEY ("customer_id") REFERENCES "public"."customers"("id") ON DELETE no action ON UPDATE no action;

ALTER TABLE "starlink_devices" ADD CONSTRAINT "starlink_devices_created_by_users_id_fk" 
    FOREIGN KEY ("created_by") REFERENCES "public"."users"("id") ON DELETE no action ON UPDATE no action;

ALTER TABLE "inventory" ADD CONSTRAINT "inventory_assigned_to_employees_id_fk" 
    FOREIGN KEY ("assigned_to") REFERENCES "public"."employees"("id") ON DELETE set null ON UPDATE no action;
```

## Verification Checklist

After running the migration, test these in your deployed app:

- [ ] ✅ Create new employee (should show company dropdown)
- [ ] ✅ Create service request/activity (should work without request_type)
- [ ] ✅ Create project task (should work)
- [ ] ✅ Create bid (should work)
- [ ] ✅ Create inventory item (should work)
- [ ] ✅ Delete customer (should work)

## Troubleshooting

### Error: "column already exists"
- Safe to ignore, column was already added
- The `IF NOT EXISTS` clauses prevent errors

### Error: "relation already exists"  
- Table already created, safe to skip
- Move to next command

### Error: "constraint already exists"
- FK constraint already added, safe to skip

### Error: "type does not exist"
- Some enum types might be missing
- Not critical, app will still work

## Rollback (If Needed)

Only use if something goes catastrophically wrong:

```sql
-- Remove new tables
DROP TABLE IF EXISTS company_loans CASCADE;
DROP TABLE IF EXISTS starlink_devices CASCADE;

-- Revert column changes
ALTER TABLE activities ALTER COLUMN request_type SET NOT NULL;
ALTER TABLE employees DROP COLUMN IF EXISTS company;
ALTER TABLE employees DROP COLUMN IF EXISTS profile_picture_url;
ALTER TABLE employees DROP COLUMN IF EXISTS assigned_resources;
ALTER TABLE inventory DROP COLUMN IF EXISTS supplier_contact_person;
ALTER TABLE inventory DROP COLUMN IF EXISTS supplier_email;
ALTER TABLE inventory DROP COLUMN IF EXISTS supplier_phone;
ALTER TABLE inventory DROP COLUMN IF EXISTS supplier_address;
ALTER TABLE inventory DROP COLUMN IF EXISTS assigned_to;
```

## Summary

**RECOMMENDED**: Use Option B - Copy the complete SQL block and run it once in Database UI.

This will:
1. ✅ Create missing tables (company_loans, starlink_devices)
2. ✅ Fix activities table (make request_type optional)
3. ✅ Add company field to employees
4. ✅ Add all missing columns
5. ✅ Set up all foreign keys

After this, production will match development and all errors will be fixed!

## Need Help?

If you encounter errors:
1. Copy the exact error message
2. Note which SQL command failed
3. Check if you're in the production database
4. Verify you have admin/write permissions
