# Archived Firestore Index Files

**Date Archived:** 2026-01-24  
**Reason:** Consolidation - keeping only root `firestore.indexes.json` as single source of truth

## Archived Individual App Index Files

All individual app index files have been archived. The comprehensive index file at `shared-firebase-config/firestore.indexes.json` (200KB, ~5000 lines) contains all necessary indexes for the entire project.

### Archived Files

1. **clip_show_pro__backbone_clip_show_pro_packages_web-browser_firestore.indexes.json** (217 lines)
2. **cns_firestore.indexes.json** (4994 lines) - Large CNS-specific indexes
3. **cuesheet_budget_tools__backbone_cuesheet_budget_tools_packages_web-browser_firestore.indexes.json** (99 lines)
4. **iwm_firestore.indexes.json** (1234 lines)
5. **licensing_website_firestore.indexes.json** (885 lines)
6. **production_workflow_system__backbone_production_workflow_system_shared-firebase-config_firestore.indexes.json** (231 lines)
7. **production_workflow_system_firestore.indexes.json** (125 lines)
8. **standalone_call_sheet_firestore.indexes.json** (529 lines)
9. **timecard_management_system_firestore.indexes.json** (880 lines)

## Active Index File

**Location:** `shared-firebase-config/firestore.indexes.json`  
**Size:** ~200KB (~5000+ lines)  
**Referenced by:** Root `firebase.json`  
**Status:** Production active - contains all indexes for entire project

## Notes

- The active index file is comprehensive and includes indexes for all collections across all apps
- Individual app indexes have been preserved in this archive for reference
- All indexes are deployed as a single unit via root `firebase.json`
- To review historical app-specific indexes, refer to the archived files above

## Index Deployment

Indexes are deployed via:

```bash
firebase deploy --only firestore:indexes
```

From project root only.
