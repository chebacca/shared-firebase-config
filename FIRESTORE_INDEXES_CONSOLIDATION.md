# Firestore Indexes Consolidation - Complete Guide

## Overview

All Firestore indexes across all BACKBONE projects have been consolidated into a single source of truth: `shared-firebase-config/firestore.indexes.json`. This ensures:

- ✅ No duplicate indexes
- ✅ Consistent index definitions across all projects
- ✅ Safe deployment without conflicts
- ✅ Easy maintenance and updates

## Current Status

- **Total Indexes**: 331 (after cleanup of 83 unused indexes)
- **Source of Truth**: `shared-firebase-config/firestore.indexes.json`
- **All Projects**: Point to shared config via `firebase.json`

## Project Configuration

All `firebase.json` files now point to the shared index file:

```json
{
  "firestore": {
    "indexes": "../shared-firebase-config/firestore.indexes.json"
  }
}
```

### Updated Projects

- ✅ `_backbone_production_workflow_system/firebase.json`
- ✅ `_backbone_standalone_call_sheet/firebase.json`
- ✅ `_backbone_cns/firebase.json`
- ✅ `_backbone_timecard_management_system/firebase.json`
- ✅ `_backbone_licensing_website/firebase.json`
- ✅ `_backbone_clip_show_pro/packages/web-browser/firebase.json`
- ✅ `_backbone_cuesheet_budget_tools/packages/web-browser/firebase.json`
- ✅ `_backbone_iwm/firebase.json`
- ✅ Root `firebase.json` (already correct)

## Maintenance Scripts

### 1. Consolidate Indexes

Merges indexes from Firebase production and all project files:

```bash
node scripts/maintenance/consolidate-firestore-indexes.mjs
```

**What it does:**
- Exports current Firebase production indexes
- Collects indexes from all project `firestore.indexes.json` files
- Merges and deduplicates
- Updates `shared-firebase-config/firestore.indexes.json`

### 2. Audit Index Usage

Scans codebase to identify which indexes are actually used:

```bash
node scripts/maintenance/audit-index-usage.mjs
```

**Output:** `scripts/maintenance/index-audit-report.json`

**Report includes:**
- Used indexes (required)
- Potentially unused indexes (candidates for removal)
- Duplicate indexes
- Collections with/without indexes

### 3. Cleanup Unused Indexes

Removes indexes identified as unused by the audit:

```bash
node scripts/maintenance/cleanup-unused-indexes.mjs
```

**Safety:**
- Creates automatic backup before removal
- Only removes indexes confirmed as unused
- Preserves all indexes that might be needed

### 4. Reconcile Indexes

Safely merges Firebase production indexes with local file:

```bash
node scripts/reconcile-firestore-indexes-simple.mjs
```

**Strategy:**
- Preserves ALL Firebase indexes (highest priority)
- Adds missing local indexes
- Never deletes anything

## Deployment

### Safe Deployment Process

1. **Validate index file:**
   ```bash
   node -e "JSON.parse(require('fs').readFileSync('shared-firebase-config/firestore.indexes.json'))"
   ```

2. **Deploy indexes:**
   ```bash
   firebase deploy --only firestore:indexes --project backbone-logic
   ```

3. **If prompted about deleting indexes:**
   - **Answer: `N` (No)**
   - This preserves all existing Firebase indexes
   - Only adds missing indexes from local file

### Automated Deployment

Use the updated deployment script:

```bash
./scripts/deployment/firebase-deploy-all.sh
```

**Features:**
- Validates index file before deployment
- Handles index conflicts automatically
- Preserves existing Firebase indexes
- Only adds new indexes

## Index Structure

Each index follows this format:

```json
{
  "collectionGroup": "collectionName",
  "queryScope": "COLLECTION",
  "fields": [
    {
      "fieldPath": "fieldName",
      "order": "ASCENDING" | "DESCENDING"
    }
  ],
  "density": "SPARSE_ALL"  // Optional
}
```

## Adding New Indexes

### Method 1: Add to Shared Config (Recommended)

1. Edit `shared-firebase-config/firestore.indexes.json`
2. Add new index to the `indexes` array
3. Run consolidation script to validate
4. Deploy

### Method 2: Use Firebase Console

1. Go to [Firebase Console](https://console.firebase.google.com/project/backbone-logic/firestore/indexes)
2. Create index manually
3. Export using: `firebase firestore:indexes --project backbone-logic`
4. Merge into shared config using consolidation script

## Best Practices

1. **Always use shared config** - Don't create project-specific index files
2. **Run audit before cleanup** - Understand what indexes are actually used
3. **Preserve Firebase indexes** - Never delete indexes without confirmation
4. **Test deployment** - Use `--dry-run` flag before actual deployment
5. **Create backups** - Scripts create automatic backups, but manual backups are also recommended

## Troubleshooting

### Deployment Conflicts

If Firebase asks about deleting indexes:

1. **Answer `N` (No)** - This is safe and preserves existing indexes
2. Review which indexes Firebase wants to delete
3. If an index should be removed, do it manually in Firebase Console
4. Re-run deployment

### Missing Indexes

If queries fail with "index required" errors:

1. Check the error message for required index details
2. Add the index to `shared-firebase-config/firestore.indexes.json`
3. Deploy indexes
4. Wait for index to build (5-15 minutes)

### Index Build Time

- Small collections: 1-5 minutes
- Medium collections: 5-15 minutes
- Large collections: 15-60 minutes

Check build status in [Firebase Console](https://console.firebase.google.com/project/backbone-logic/firestore/indexes)

## Audit Report

The audit report (`scripts/maintenance/index-audit-report.json`) provides:

- **Summary**: Total, used, unused, duplicates
- **Used Indexes**: Indexes confirmed to be in use
- **Potentially Unused**: Indexes that may be safe to remove
- **Collections**: Lists of collections with/without indexes

Review the report before removing any indexes.

## Rollback

If you need to rollback:

1. Restore from backup: `firestore.indexes.json.backup.[timestamp]`
2. Or use reconciliation script to restore from Firebase
3. Redeploy indexes

## Related Files

- **Index File**: `shared-firebase-config/firestore.indexes.json`
- **Consolidation Script**: `scripts/maintenance/consolidate-firestore-indexes.mjs`
- **Audit Script**: `scripts/maintenance/audit-index-usage.mjs`
- **Cleanup Script**: `scripts/maintenance/cleanup-unused-indexes.mjs`
- **Reconciliation Script**: `scripts/reconcile-firestore-indexes-simple.mjs`
- **Deployment Script**: `scripts/deployment/firebase-deploy-all.sh`
- **Audit Report**: `scripts/maintenance/index-audit-report.json`

## Summary

✅ **Consolidated**: All indexes merged into single file  
✅ **Cleaned**: 83 unused indexes removed  
✅ **Aligned**: All projects point to shared config  
✅ **Tested**: Dry-run deployment successful  
✅ **Documented**: Complete process documented  

The Firestore index system is now unified, clean, and ready for safe deployment.

