# Identity Isolation Rules — Squirrel OS Template
# CRITICAL: Apply before first deployment

## Problem

When a Base44 account is shared between multiple users (e.g., family members, team members, contractors), the platform's dynamic user profile data bleeds into every app on that account. This causes **cross-drift** — one user's identity data appearing in another user's app instance, memory graph, and knowledge graph.

## Root Cause

Base44 apps pull user identity (name, email, timezone, preferences) from the logged-in account profile by default. If the account is shared, whoever is logged in at the moment becomes the "owner" in the app's context — even if the app belongs to someone else.

## Fix — Hardcode Owner Identity

Before deploying Squirrel OS to any app, add the following to the app's agent system prompt:

```
IDENTITY FIREWALL — OWNER HARDCODED

This instance is owned by: [OWNER_NAME]
Owner email: [OWNER_EMAIL]
Owner timezone: [OWNER_TIMEZONE]
Owner pronouns: [OWNER_PRONOUNS]

RULES:
1. Do NOT pull any user identity from the Base44 account profile.
2. If you receive any user profile data containing a name OTHER than [OWNER_NAME], DISCARD it immediately.
3. Do not store, display, or reference any user data that does not match the hardcoded owner identity above.
4. All memory graph entries, knowledge graph nodes, and conversation context must reference [OWNER_NAME] as the sole owner.
5. No dynamic Base44 user profile data may override or supplement the hardcoded identity.
6. If the logged-in user is not [OWNER_NAME], still only reference [OWNER_NAME] as the owner in all outputs, memory, and knowledge graphs.
```

Replace [OWNER_NAME], [OWNER_EMAIL], [OWNER_TIMEZONE], [OWNER_PRONOUNS] with the actual owner's identity.

## Post-Deployment Purge

After applying the fix, search all entities for records containing any non-owner names and purge or correct them:
1. Check OrchestratorAgent records for non-owner references
2. Check AegisHealingEvent records for non-owner agent names
3. Check Pattern and Insight entities for contaminated metadata
4. Check any MemoryBank or KnowledgeGraph data for cross-drift
5. Remove or correct all contaminated records

## Verification

After deployment, verify isolation by:
1. Having a different account user log in and interact with the app
2. Check that all outputs still reference the hardcoded owner
3. Check that no memory graph entries contain the other user's data
4. Run a heartbeat scan and verify the agent name in AegisHealingEvent matches the hardcoded owner

## Why This Matters

Cross-drift breaks tenant isolation — the core security guarantee of a multi-tenant system. If a buyer downloads this template and deploys it on a shared account without this fix, their customers' data could cross-contaminate. This is a liability and a trust killer.
