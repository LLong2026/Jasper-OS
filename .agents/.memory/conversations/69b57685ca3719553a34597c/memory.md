<!-- build-plan:begin -->
## Active build plan — squirrel_os_hub
Work through every step, and confirm each is satisfied before telling the user the agent is ready.

- [ ] Create the 12 platform entities (Customer, ConnectedApp, DeploymentJob, HealthManifest, HealingEventLog, CreditUsage, PlatformAlert, TierConfiguration, PlaybookTemplate, SkillTemplate, NeuralNodeTemplate, NeuralMeshSnapshot) with full schemas and relations
- [ ] Seed TierConfiguration with the three tiers (free / licensed / saas), mapping each tier to its allowed feature set, entity set, playbook set, and credit allotment
- [ ] Seed PlaybookTemplate with the 11 Aegis playbooks (PB-001 prompt_drift through PB-011 integration_degraded), SkillTemplate with the 4 skills (heartbeat-check, full-system-sweep, anomaly-response, pattern-learning), and NeuralNodeTemplate with the 31 nodes across 5 layers
- [ ] Implement backend functions: scanConnectedApps (base44.read_entities cross-app), deploySquirrelOS (base44.manage_entity_schemas + base44.deploy_backend_function), aggregateHealthMetrics, generateHealthManifest, checkCreditUsage, escalateAlert (webhook/function call into Gabriel superagent chat)
- [ ] Authorize the base44 connector with scopes for read_entities, manage_entity_schemas, and deploy_backend_function across customer app_ids
- [ ] Authorize the github connector for the Squirrel-OS template repository (already provisioned for Leon)
- [ ] Configure the in-app dashboard channel as the primary mission-control surface for Leon
- [ ] Configure the Gabriel superagent escalation path (function/webhook) so critical PlatformAlerts reach Leon's existing chat with Gabriel
- [ ] Write operating rules to .agents/rules/deployment_policy.md, .agents/rules/tenant_isolation.md, .agents/rules/alert_escalation.md, .agents/rules/tier_enforcement.md, .agents/rules/credit_integrity.md
- [ ] Register the 8 skills under .agents/skills/ so the agent can execute onboarding, deployment, scanning, sweep, anomaly response, credit, and tier workflows deterministically
- [ ] Create the two schedule automations (15-min heartbeat scan, daily 03:00 sweep) and the three entity-change / connector automations (health_score drop, deployment completion, GitHub PR merge)
- [ ] Build the dashboard UI: live health grid across connected apps, neural mesh topology viewer (NeuralNodeTemplate/Snapshot), healing event history feed, credit usage panels, tier management console, and deployment job tracker
- [ ] Run a reference deployment against Gabriel's own app to validate the end-to-end deploy-squirrel-os skill and confirm parity with the proven production architecture
- [ ] Open customer onboarding: publish the free open-core, licensed template, and SaaS subscription entry points and wire them to the onboard-customer skill
<!-- build-plan:end -->