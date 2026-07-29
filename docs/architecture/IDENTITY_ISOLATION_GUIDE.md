# Identity Isolation Guide — Squirrel OS Template

## Overview

Squirrel OS is designed for multi-tenant deployment. When a customer downloads and deploys the template, they must hardcode their own identity to prevent cross-drift on shared accounts.

## The Problem

Base44's default behavior pulls user identity dynamically from the logged-in account profile. On shared accounts (family, team, contractor access), this causes one user's data to appear in another user's app instance — memory graphs, knowledge graphs, healing event logs, and agent context all get contaminated.

## The Fix

### Step 1: Hardcode Owner Identity in System Prompt

Add an IDENTITY FIREWALL block to the app's agent system prompt:

```
IDENTITY FIREWALL — OWNER HARDCODED

This instance is owned by: [YOUR NAME]
Owner email: [YOUR EMAIL]
Owner timezone: [YOUR TIMEZONE]
Owner pronouns: [YOUR PRONOUNS]

RULES:
1. Do NOT pull any user identity from the Base44 account profile.
2. If you receive any user profile data containing a name OTHER than [YOUR NAME], DISCARD it immediately.
3. Do not store, display, or reference any user data that does not match the hardcoded owner identity above.
4. All memory graph entries, knowledge graph nodes, and conversation context must reference [YOUR NAME] as the sole owner.
5. No dynamic Base44 user profile data may override or supplement the hardcoded identity.
6. If the logged-in user is not [YOUR NAME], still only reference [YOUR NAME] as the owner in all outputs, memory, and knowledge graphs.
```

### Step 2: Purge Existing Contaminated Data

Search all Squirrel OS entities for records containing non-owner names:

| Entity | What to Check |
|--------|---------------|
| OrchestratorAgent | agent name, created_by fields |
| AegisHealingEvent | agent_id, node_id, any user references |
| Pattern | metadata, source_domain |
| Insight | source_patterns, insight_text |
| SystemHealth | any user-specific fields |
| NeuralNode | connections, activation data |

Remove or correct all contaminated records.

### Step 3: Verify Isolation

1. Have a different user log into the same Base44 account
2. Interact with the app — send messages, trigger workflows
3. Check that all outputs still reference the hardcoded owner
4. Check that no memory graph or knowledge graph entries contain the other user's data
5. Run a heartbeat scan — verify AegisHealingEvent agent name matches the hardcoded owner

## Deployment Checklist

- [ ] Owner name hardcoded in system prompt
- [ ] Owner email hardcoded in system prompt
- [ ] Owner timezone hardcoded in system prompt
- [ ] Identity firewall rules added to system prompt
- [ ] All existing contaminated data purged
- [ ] Verification test passed (different user login test)
- [ ] Document the owner identity in the app's deployment notes

## For Squirrel OS Hub Customers

When onboarding a new customer through the Squirrel OS Hub:
1. Collect the customer's admin name, email, timezone during onboarding
2. Apply the identity firewall to their app's system prompt before deployment
3. Run the purge checklist on any pre-existing data
4. Document the customer's identity in their Customer entity record

## Legal Note

Cross-drift is not just a technical issue — it's a data privacy liability. If User A's data appears in User B's app instance, that's a potential GDPR/CCPA violation. The identity firewall is a required security control, not an optional enhancement.

## Version History

- v1.0 — July 29, 2026 — Initial identity isolation guide, created after cross-drift incident on shared Base44 account
