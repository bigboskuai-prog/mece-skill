# MECE Examples — Before & After

Real-world examples showing how MECE validation catches overlaps and gaps, then fixes them.

---

## Example 1: The Protein Problem

**Context:** You're ordering food with a one-protein-per-meal restriction.

**Before (NOT MECE):**
```
Order:
├── Fish
├── Rice
├── Salad
└── Eggs
```

**Validation:**
```
OVERLAP: Fish ↔ Eggs
Reason: Both are proteins. You've ordered the same nutritional category twice.
Fix: Pick one protein. Move the other out or replace it.

Verdict: NOT MECE — overlap in protein category
```

**After (MECE):**
```
Order (by food group):
├── Protein: Fish
├── Carb: Rice
├── Vegetable: Salad
└── Fat/Dairy: Butter or cheese
```

---

## Example 2: The Packing List

**Context:** Packing for a trip.

**Before (NOT MECE):**
```
Packing list:
├── Clothes
├── Shoes
└── Toiletries
```

**Validation:**
```
GAP: Electronics (chargers, adapters, headphones)
GAP: Documents (passport, tickets, insurance)
GAP: Medications / health items
GAP: Accessories (sunglasses, watch, belt)

Verdict: NOT MECE — 4 gaps found
```

**After (MECE):**
```
Packing list (by category):
├── Clothing (tops, bottoms, outerwear, underwear)
├── Footwear (daily shoes, activity-specific, formal)
├── Toiletries (hygiene, skincare, haircare)
├── Electronics (devices, chargers, adapters)
├── Documents (ID, travel docs, insurance, itinerary)
├── Health (medications, first aid, supplements)
└── Accessories (eyewear, jewelry, bags)
```

---

## Example 3: Product Feature Spec

**Context:** A PM writes a feature list for a dashboard app.

**Before (NOT MECE):**
```
Dashboard features:
├── Analytics and reporting
├── Data visualization (charts, graphs)
├── Export to PDF and CSV
├── User management
├── Role-based access control
└── Real-time updates
```

**Validation:**
```
OVERLAP: "Analytics and reporting" ↔ "Data visualization"
Reason: Charts and graphs ARE analytics. These are the same concern split artificially.

OVERLAP: "User management" ↔ "Role-based access control"
Reason: RBAC is a subset of user management. Not separate features.

GAP: No mention of data input / data sources
GAP: No mention of notifications or alerts
GAP: No mention of settings or configuration

Verdict: NOT MECE — 2 overlaps, 3 gaps
```

**After (MECE):**
```
Dashboard features:
├── Data Layer (sources, connections, sync, import)
├── Visualization (charts, tables, maps, real-time displays)
├── Actions (export, share, schedule, print)
├── Access Control (users, roles, permissions, audit log)
├── Notifications (alerts, thresholds, digests)
└── Configuration (settings, preferences, integrations)
```

---

## Example 4: API Endpoint Design

**Context:** Designing REST endpoints for a task management API.

**Before (NOT MECE):**
```
Endpoints:
├── /tasks — CRUD operations
├── /tasks/assign — Assign task to user
├── /tasks/complete — Mark task complete
├── /users — User management
├── /teams — Team management
├── /users/teams — Get user's teams
```

**Validation:**
```
OVERLAP: /tasks/assign and /tasks/complete ↔ /tasks CRUD
Reason: Assignment and completion are state mutations — they belong under task CRUD
(PATCH /tasks/:id), not separate endpoints.

OVERLAP: /users/teams ↔ /teams
Reason: "Get user's teams" could live under /users/:id/teams OR /teams?userId=X.
Having both creates ambiguity.

GAP: No error/health endpoints
GAP: No search or filtering
GAP: No batch operations

Verdict: NOT MECE — 2 overlaps, 3 gaps
```

**After (MECE):**
```
Endpoints (by resource):
├── /tasks — CRUD + state transitions (assign, complete, archive)
├── /users — CRUD + relationships (GET /users/:id/tasks, /users/:id/teams)
├── /teams — CRUD + membership (GET /teams/:id/members)
├── /search — Cross-resource search (tasks, users, teams)
└── /system — Health check, version, config
```

---

## Example 5: State Management (React Reducer)

**Context:** Defining actions for a queue management reducer.

**Before (NOT MECE):**
```
Actions:
├── ADD_TO_QUEUE
├── REMOVE_FROM_QUEUE
├── UPDATE_TICKET
├── MARK_AS_SERVING
├── CALL_NEXT
├── HOLD_TICKET
├── COMPLETE_TICKET
├── TRANSFER_TICKET
├── CANCEL_TICKET
└── SET_QUEUE
```

**Validation:**
```
OVERLAP: MARK_AS_SERVING ↔ CALL_NEXT
Reason: "Call next" marks the next ticket as serving. These are the same
state transition triggered differently. Pick one semantic.

OVERLAP: REMOVE_FROM_QUEUE ↔ CANCEL_TICKET / COMPLETE_TICKET
Reason: Completing and cancelling both remove from queue. Is REMOVE_FROM_QUEUE
a separate action or the shared effect? Ambiguous.

GAP: No action for reordering / priority changes
GAP: No action for bulk operations (clear all, reset)
GAP: No action for ticket metadata updates vs. status updates (UPDATE_TICKET is too broad)

Verdict: NOT MECE — 2 overlaps, 3 gaps
```

**After (MECE):**
```
Actions (by transition type):
├── Lifecycle (ticket enters/exits queue)
│   ├── ENQUEUE — new ticket added
│   └── DEQUEUE — ticket leaves (completed, cancelled, or no-show)
├── Status transitions (ticket changes state)
│   ├── CALL_NEXT — waiting → serving
│   ├── HOLD — serving → held
│   ├── RESUME — held → serving
│   ├── TRANSFER — serving → transferred
│   └── COMPLETE — serving → completed
├── Mutations (ticket data changes without status change)
│   ├── UPDATE_PRIORITY — reorder within queue
│   └── UPDATE_NOTES — add/edit ticket notes
└── Bulk (whole-queue operations)
    ├── SET_QUEUE — replace entire queue (sync/init)
    └── CLEAR_QUEUE — remove all tickets
```

---

## Example 6: Roadmap Phases

**Context:** A startup planning their product roadmap.

**Before (NOT MECE):**
```
Roadmap:
├── MVP features
├── Core platform
├── Growth features
├── Enterprise features
├── Technical debt
└── Nice-to-haves
```

**Validation:**
```
OVERLAP: "MVP features" ↔ "Core platform"
Reason: The MVP IS the core platform. These aren't distinct phases.

OVERLAP: "Growth features" ↔ "Enterprise features"
Reason: Enterprise IS a growth segment. A feature could be both.

OVERLAP: "Technical debt" crosses all phases — it's a concern, not a phase.

GAP: No infrastructure / DevOps / reliability work
GAP: No compliance / security phase
GAP: "Nice-to-haves" is an undefined catch-all

Verdict: NOT MECE — 3 overlaps, 3 gaps
```

**After (MECE by phase + scope):**
```
Roadmap:
├── Phase 1: Foundation (auth, core data model, base UI, infra)
├── Phase 2: Core Workflows (primary user journeys, integrations)
├── Phase 3: Scale (performance, multi-tenancy, monitoring)
├── Phase 4: Compliance (audit trails, encryption, certifications)
├── Phase 5: Advanced (analytics, automation, marketplace)
└── Deferred (explicitly scoped out with rationale for each)
```

---

## Pattern: The MECE Validation Prompt

Use this after any structured output to validate it:

```
Review the structure above for MECE compliance:

1. Mutual Exclusivity — Can any item fit into more than one category?
   Are there concepts described with different names in different sections?

2. Collective Exhaustiveness — What real-world scenarios or items are NOT
   covered by any category? What would a domain expert immediately flag
   as missing?

For each violation, state what's wrong, why it matters, and how to fix it.
Then provide the corrected MECE structure.
```
