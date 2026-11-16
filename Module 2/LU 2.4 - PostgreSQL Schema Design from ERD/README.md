# Helpdesk Schema Repair

This project fixes the PostgreSQL database schema for a Helpdesk customer support platform.

The schema follows the given ER diagram and uses UUID primary keys, foreign keys, constraints, and appropriate ON DELETE actions to protect data.

## ERD-to-Schema Decisions

### Decision 1: Column Type Decision

**ERD said:** Priority and status are string values with a limited set of options.

**Schema decided:** Used TEXT with CHECK constraints instead of PostgreSQL ENUM.

**Reason:** The allowed values can change as the application grows. Updating a CHECK constraint is easier than managing changes to an ENUM type.

### Decision 2: ON DELETE Decision

**ERD said:** A ticket can have an optional assigned agent.

**Schema decided:** Used ON DELETE SET NULL for the assignee_id foreign key.

**Reason:** If an agent is deleted, the ticket should not be deleted. It should remain in the system without an assigned agent.

### Decision 3: Default Value Decision

**ERD said:** A new ticket has a status.

**Schema decided:** Used DEFAULT 'open' for the status column.

**Reason:** Every new ticket should automatically start with the open status, even if the application does not provide a status value.

## Rejected Table Shape

### Shape: Tags Array Inside Tickets

**What it was:** Storing all ticket tags inside the tickets table using a TEXT[] array.

**Why rejected:** Storing tags as an array makes it difficult to manage, update, and reuse tags across multiple tickets.

**What would break:** Renaming a tag would require updating multiple ticket records. It would also be difficult to maintain consistent tag information.

**Chosen solution:** Created separate tags and ticket_tags tables to represent the many-to-many relationship between tickets and tags.

## Testing

Run the following commands:

```bash
npm install
npm run db:reset
npm test
```

All 36 pgTAP tests should pass.

## Conclusion

The schema now follows the given ER diagram and uses appropriate data types, constraints, foreign keys, and deletion rules to maintain data consistency.