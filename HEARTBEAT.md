# Heartbeat Checklist

## Memory Extraction (every heartbeat)
1. Check conversations since last heartbeat for durable facts
2. Extract: relationships, status changes, milestones, decisions, preferences
3. Write to relevant ~/life/ entity (append items.json, update summary.md if needed)
4. Update today's memory/YYYY-MM-DD.md with timeline entries
5. Skip: small talk, transient requests, already-known info
6. Save durable facts immediately - don't defer

## Entity Rules
- Create ~/life/ entity when: mentioned 3+ times, OR direct relationship, OR significant
- Never delete items.json facts - supersede (status: "superseded", supersededBy: "id")
- summary.md is disposable, items.json is truth
- Fact schema: {"id","fact","category","timestamp","source","status","relatedEntities","lastAccessed","accessCount"}

## Memory Maintenance (rotate, every few days)
1. Read recent memory/YYYY-MM-DD.md files
2. Distill significant events/lessons into MEMORY.md
3. Remove stale info from MEMORY.md
4. Rewrite ~/life/ summary.md for entities with new facts (hot/warm/cold curation)

## Periodic Checks (rotate 2-4x/day)
- Mentions / notifications
- Project status
- If nothing needs attention, reply HEARTBEAT_OK
