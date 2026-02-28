# Production Migration: Bid Purchase Items & Invoice Endorsement

## Overview
This migration adds two new features to the production database:
1. **Bid Purchase Items Workflow** - Purchasing items within bids requiring Level 2 approval and CEO endorsement
2. **Invoice Endorsement Workflow** - CEO endorsement for invoices after Level 2 approval

## What's Being Added

### New Features:
1. ✅ **bid_purchase_items table** - Manage purchase items within bids with approval workflow
2. ✅ **Invoice endorsement fields** - Add endorsedBy and endorsedAt to invoices table

## Migration SQL

### Option A: Complete Migration (Run All At Once)

**Open Replit Database UI → Production → SQL Runner**

Then run this complete SQL (copy everything):

```sql
-- ============================================================================
-- 1. CREATE BID PURCHASE ITEMS TABLE
-- ============================================================================
CREATE TABLE IF NOT EXISTS "bid_purchase_items" (
    "id" varchar PRIMARY KEY DEFAULT gen_random_uuid() NOT NULL,
    "bid_id" varchar NOT NULL,
    
    -- Item details
    "item_name" text NOT NULL,
    "description" text,
    "category" text,
    
    -- Quantity and pricing
    "quantity" integer NOT NULL,
    "unit_price" numeric(12, 2) NOT NULL,
    "total_price" numeric(12, 2) NOT NULL,
    "currency" text DEFAULT 'AED' NOT NULL,
    
    -- Vendor information
    "vendor" text,
    "vendor_contact" text,
    "vendor_email" text,
    
    -- Approval workflow (Level 2 → CEO)
    "status" text DEFAULT 'pending' NOT NULL,
    "requested_by" varchar NOT NULL,
    "approved_by" varchar,
    "approved_at" timestamp,
    "endorsed_by" varchar,
    "endorsed_at" timestamp,
    "rejection_reason" text,
    
    -- Inventory integration
    "inventory_item_id" varchar,
    "added_to_inventory" boolean DEFAULT false,
    "added_to_inventory_at" timestamp,
    
    -- Delivery tracking
    "expected_delivery_date" timestamp,
    "actual_delivery_date" timestamp,
    "delivery_status" text DEFAULT 'pending',
    
    -- Notes
    "notes" text,
    "internal_notes" text,
    
    -- Audit trail
    "created_at" timestamp DEFAULT now(),
    "updated_at" timestamp DEFAULT now()
);

-- ============================================================================
-- 2. ADD FOREIGN KEY CONSTRAINTS FOR BID PURCHASE ITEMS
-- ============================================================================
DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM pg_constraint 
        WHERE conname = 'bid_purchase_items_bid_id_bids_id_fk'
    ) THEN
        ALTER TABLE "bid_purchase_items" 
            ADD CONSTRAINT "bid_purchase_items_bid_id_bids_id_fk" 
            FOREIGN KEY ("bid_id") REFERENCES "public"."bids"("id") 
            ON DELETE cascade ON UPDATE no action;
    END IF;
END $$;

DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM pg_constraint 
        WHERE conname = 'bid_purchase_items_requested_by_users_id_fk'
    ) THEN
        ALTER TABLE "bid_purchase_items" 
            ADD CONSTRAINT "bid_purchase_items_requested_by_users_id_fk" 
            FOREIGN KEY ("requested_by") REFERENCES "public"."users"("id") 
            ON DELETE no action ON UPDATE no action;
    END IF;
END $$;

DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM pg_constraint 
        WHERE conname = 'bid_purchase_items_approved_by_users_id_fk'
    ) THEN
        ALTER TABLE "bid_purchase_items" 
            ADD CONSTRAINT "bid_purchase_items_approved_by_users_id_fk" 
            FOREIGN KEY ("approved_by") REFERENCES "public"."users"("id") 
            ON DELETE no action ON UPDATE no action;
    END IF;
END $$;

DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM pg_constraint 
        WHERE conname = 'bid_purchase_items_endorsed_by_users_id_fk'
    ) THEN
        ALTER TABLE "bid_purchase_items" 
            ADD CONSTRAINT "bid_purchase_items_endorsed_by_users_id_fk" 
            FOREIGN KEY ("endorsed_by") REFERENCES "public"."users"("id") 
            ON DELETE no action ON UPDATE no action;
    END IF;
END $$;

DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM pg_constraint 
        WHERE conname = 'bid_purchase_items_inventory_item_id_inventory_id_fk'
    ) THEN
        ALTER TABLE "bid_purchase_items" 
            ADD CONSTRAINT "bid_purchase_items_inventory_item_id_inventory_id_fk" 
            FOREIGN KEY ("inventory_item_id") REFERENCES "public"."inventory"("id") 
            ON DELETE no action ON UPDATE no action;
    END IF;
END $$;

-- ============================================================================
-- 3. ADD INVOICE ENDORSEMENT FIELDS
-- ============================================================================
ALTER TABLE "invoices" ADD COLUMN IF NOT EXISTS "endorsed_by" varchar;
ALTER TABLE "invoices" ADD COLUMN IF NOT EXISTS "endorsed_at" timestamp;

-- ============================================================================
-- 4. ADD FOREIGN KEY CONSTRAINT FOR INVOICE ENDORSEMENT
-- ============================================================================
DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM pg_constraint 
        WHERE conname = 'invoices_endorsed_by_users_id_fk'
    ) THEN
        ALTER TABLE "invoices" 
            ADD CONSTRAINT "invoices_endorsed_by_users_id_fk" 
            FOREIGN KEY ("endorsed_by") REFERENCES "public"."users"("id") 
            ON DELETE no action ON UPDATE no action;
    END IF;
END $$;
```

### Option B: Run Commands Step by Step

If the complete SQL above fails, run these commands individually:

#### Step 1: Create bid_purchase_items table
```sql
CREATE TABLE IF NOT EXISTS "bid_purchase_items" (
    "id" varchar PRIMARY KEY DEFAULT gen_random_uuid() NOT NULL,
    "bid_id" varchar NOT NULL,
    
    -- Item details
    "item_name" text NOT NULL,
    "description" text,
    "category" text,
    
    -- Quantity and pricing
    "quantity" integer NOT NULL,
    "unit_price" numeric(12, 2) NOT NULL,
    "total_price" numeric(12, 2) NOT NULL,
    "currency" text DEFAULT 'AED' NOT NULL,
    
    -- Vendor information
    "vendor" text,
    "vendor_contact" text,
    "vendor_email" text,
    
    -- Approval workflow (Level 2 → CEO)
    "status" text DEFAULT 'pending' NOT NULL,
    "requested_by" varchar NOT NULL,
    "approved_by" varchar,
    "approved_at" timestamp,
    "endorsed_by" varchar,
    "endorsed_at" timestamp,
    "rejection_reason" text,
    
    -- Inventory integration
    "inventory_item_id" varchar,
    "added_to_inventory" boolean DEFAULT false,
    "added_to_inventory_at" timestamp,
    
    -- Delivery tracking
    "expected_delivery_date" timestamp,
    "actual_delivery_date" timestamp,
    "delivery_status" text DEFAULT 'pending',
    
    -- Notes
    "notes" text,
    "internal_notes" text,
    
    -- Audit trail
    "created_at" timestamp DEFAULT now(),
    "updated_at" timestamp DEFAULT now()
);
```

#### Step 2: Add bid_purchase_items foreign keys
```sql
DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM pg_constraint 
        WHERE conname = 'bid_purchase_items_bid_id_bids_id_fk'
    ) THEN
        ALTER TABLE "bid_purchase_items" 
            ADD CONSTRAINT "bid_purchase_items_bid_id_bids_id_fk" 
            FOREIGN KEY ("bid_id") REFERENCES "public"."bids"("id") 
            ON DELETE cascade ON UPDATE no action;
    END IF;
END $$;

DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM pg_constraint 
        WHERE conname = 'bid_purchase_items_requested_by_users_id_fk'
    ) THEN
        ALTER TABLE "bid_purchase_items" 
            ADD CONSTRAINT "bid_purchase_items_requested_by_users_id_fk" 
            FOREIGN KEY ("requested_by") REFERENCES "public"."users"("id") 
            ON DELETE no action ON UPDATE no action;
    END IF;
END $$;

DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM pg_constraint 
        WHERE conname = 'bid_purchase_items_approved_by_users_id_fk'
    ) THEN
        ALTER TABLE "bid_purchase_items" 
            ADD CONSTRAINT "bid_purchase_items_approved_by_users_id_fk" 
            FOREIGN KEY ("approved_by") REFERENCES "public"."users"("id") 
            ON DELETE no action ON UPDATE no action;
    END IF;
END $$;

DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM pg_constraint 
        WHERE conname = 'bid_purchase_items_endorsed_by_users_id_fk'
    ) THEN
        ALTER TABLE "bid_purchase_items" 
            ADD CONSTRAINT "bid_purchase_items_endorsed_by_users_id_fk" 
            FOREIGN KEY ("endorsed_by") REFERENCES "public"."users"("id") 
            ON DELETE no action ON UPDATE no action;
    END IF;
END $$;

DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM pg_constraint 
        WHERE conname = 'bid_purchase_items_inventory_item_id_inventory_id_fk'
    ) THEN
        ALTER TABLE "bid_purchase_items" 
            ADD CONSTRAINT "bid_purchase_items_inventory_item_id_inventory_id_fk" 
            FOREIGN KEY ("inventory_item_id") REFERENCES "public"."inventory"("id") 
            ON DELETE no action ON UPDATE no action;
    END IF;
END $$;
```

#### Step 3: Add invoice endorsement columns
```sql
ALTER TABLE "invoices" ADD COLUMN IF NOT EXISTS "endorsed_by" varchar;
ALTER TABLE "invoices" ADD COLUMN IF NOT EXISTS "endorsed_at" timestamp;
```

#### Step 4: Add invoice endorsement foreign key
```sql
DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM pg_constraint 
        WHERE conname = 'invoices_endorsed_by_users_id_fk'
    ) THEN
        ALTER TABLE "invoices" 
            ADD CONSTRAINT "invoices_endorsed_by_users_id_fk" 
            FOREIGN KEY ("endorsed_by") REFERENCES "public"."users"("id") 
            ON DELETE no action ON UPDATE no action;
    END IF;
END $$;
```

## Verification Checklist

After running the migration, test these features in your deployed app:

### Bid Purchase Items:
- [ ] ✅ Navigate to Bid Hub
- [ ] ✅ Open any bid (expand tasks section to see purchase items section below it)
- [ ] ✅ Add a new purchase item (item name, description, quantity, unit price)
- [ ] ✅ Verify purchase item appears with "pending" status
- [ ] ✅ Click "Approve" button (Level 2 employees)
- [ ] ✅ Verify status changes to "approved_level2"
- [ ] ✅ Click "Endorse" button (CEO only)
- [ ] ✅ Verify status changes to "endorsed"
- [ ] ✅ Test "Reject" button with a reason

### Invoice Endorsement:
- [ ] ✅ Navigate to Accounting → Invoices tab
- [ ] ✅ Create a new invoice (or find one in "approved" status)
- [ ] ✅ If invoice is in "draft", click "Submit" button
- [ ] ✅ If invoice is in "pending_approval", click "Approve" button (Level 2)
- [ ] ✅ Once approved, verify "Endorse" button appears (amber color with Award icon)
- [ ] ✅ Click "Endorse" button (CEO only)
- [ ] ✅ Verify "Send" button appears after endorsement

## Workflow Details

### Bid Purchase Items Workflow
```
pending → approved_level2 → endorsed
    ↓           ↓
rejected    rejected
```

**Status Values:**
- `pending` - Initial state, awaiting Level 2 approval
- `approved_level2` - Approved by Level 2 employee, awaiting CEO endorsement
- `endorsed` - Endorsed by CEO, fully approved
- `rejected` - Rejected at any stage with reason

**Columns:**
- `requested_by` - User who created the purchase item
- `approved_by` - Level 2 employee who approved
- `endorsed_by` - CEO who endorsed
- `rejection_reason` - Reason if rejected

### Invoice Endorsement Workflow
```
draft → pending_approval → approved (+ endorsed) → sent
```

**New Fields:**
- `endorsed_by` - CEO who endorsed the invoice
- `endorsed_at` - Timestamp when CEO endorsed

**Workflow:**
1. Employee creates invoice (status: draft)
2. Employee submits for approval (status: pending_approval)
3. Level 2 approves invoice (status: approved, approvedBy set)
4. CEO endorses invoice (endorsedBy set)
5. Invoice can be sent to client (status: sent)

## Troubleshooting

### Error: "table already exists"
- Safe to ignore, table was already created
- The `IF NOT EXISTS` clause prevents errors

### Error: "column already exists"
- Safe to ignore, column was already added
- The `IF NOT EXISTS` clause handles this

### Error: "constraint already exists"
- Safe to skip, FK constraint already added
- The DO block checks for existence

### Error: "relation 'bids' does not exist"
- This means the bids table doesn't exist in production yet
- You may need to run the previous migration first (migrations/0001_burly_joystick.sql)

### Error: "relation 'invoices' does not exist"
- This means the invoices table doesn't exist in production yet
- Check that the basic schema is deployed to production

## Rollback (If Needed)

Only use if something goes catastrophically wrong:

```sql
-- Remove bid_purchase_items table
DROP TABLE IF EXISTS bid_purchase_items CASCADE;

-- Remove invoice endorsement columns
ALTER TABLE invoices DROP COLUMN IF EXISTS endorsed_by;
ALTER TABLE invoices DROP COLUMN IF EXISTS endorsed_at;
```

## Summary

**RECOMMENDED**: Use Option A - Copy the complete SQL block and run it once in Database UI.

This migration adds:
1. ✅ Complete bid purchase items workflow with approval/endorsement
2. ✅ Invoice endorsement capability for CEO workflow
3. ✅ All necessary foreign key constraints for data integrity

After this migration, production will support:
- Purchasing items within bids with multi-level approval
- CEO endorsement of invoices after Level 2 approval

## API Endpoints Added

### Bid Purchase Items:
- `GET /api/bid-purchase-items` - Get all purchase items
- `GET /api/bid-purchase-items/bid/:bidId` - Get items for a specific bid
- `POST /api/bid-purchase-items` - Create new purchase item
- `PATCH /api/bid-purchase-items/:id` - Update purchase item
- `DELETE /api/bid-purchase-items/:id` - Delete purchase item
- `POST /api/bid-purchase-items/:id/approve` - Approve (Level 2)
- `POST /api/bid-purchase-items/:id/endorse` - Endorse (CEO)
- `POST /api/bid-purchase-items/:id/reject` - Reject with reason

### Invoice Endorsement:
- `PUT /api/invoices/:id/endorse` - Endorse invoice (CEO)

## Need Help?

If you encounter errors:
1. Copy the exact error message
2. Note which SQL command failed
3. Verify you're connected to the production database
4. Check that you have admin/write permissions
5. Ensure the bids and invoices tables exist in production
