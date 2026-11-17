# Change Records

Every configuration change to a device that's in service goes through a change record, the way it would in production (ITIL v4 change enablement).

| Type | When | Approval |
|------|------|----------|
| **Standard** | Pre-approved, low-risk, repeatable (e.g. bounce a user port) | None beyond the runbook |
| **Normal** | Planned change with risk to assess | Change authority / CAB |
| **Emergency** | Restoring service in a major incident, or an urgent security fix | Emergency CAB (ECAB); documented after |

## Template

```markdown
# C-YYYYMMDDNNN — <short title>

| Field | Value |
|-------|-------|
| Type | Standard / Normal / Emergency |
| Requester | |
| Related ticket | |
| CIs affected | |
| Window | |
| Status | Draft / Approved / Implemented / Closed / Backed out |

## Reason
What problem this solves, and why this change fixes it.

## Risk and impact
What could break, who notices, for how long.

## Implementation plan
Numbered steps with exact commands.

## Test plan
How to prove it worked.

## Backout plan
Exact steps to return to the previous state.

## Result
What happened, verification output, closure.
```

## Log

| Change | Title | Type | Status |
|--------|-------|------|--------|
| [V1-C-20261116002](./archive/V1-C-20261116002.md) | Replace DMZ-SRV1 IOL stand-in with Ubuntu web server | Normal | Superseded (built into v2 baseline) |
