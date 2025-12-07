# S3 Lifecycle Management
## Topics Covered
- What is Lifecycle
- Why Lifecycle is used
- Types of Lifecycle actions
- Scenarios
- Example (Console)
- Best Practices

---

## What is Lifecycle
Lifecycle Management automates moving objects to cheaper storage classes and deleting objects after a defined period. It helps control cost, manage retention, and clean objects without manual action.

---

## Why Lifecycle is Important
- S3 can become expensive as data grows
- Logs accumulate continuously
- Old versions consume hidden storage
- Delete markers create clutter
- Compliance requires retention rules

Lifecycle automatically:
- moves data to cheaper tiers
- deletes old data
- removes delete markers
- controls version sprawl

---

## Lifecycle Actions

### 1. Transition Current Versions
Move active objects to cheaper storage (Standard-IA, Glacier) after X days.

### 2. Transition Non-Current Versions
Move **older versions** to cheaper storage.

### 3. Expire Current Versions
Delete the active object after a certain number of days.

### 4. Permanently Delete Non-Current Versions
Remove older versions and keep only recent ones.

### 5. Delete expired delete markers & incomplete uploads
Remove delete markers and failed multipart uploads automatically.

---

## Scenarios (Company Examples)

### Logs
- Transition to IA after 30 days
- Delete after 180 days

### Temp uploads
- Delete after 1 day

### Profile images
- Keep latest versions
- Delete older non-current versions

### Product images
- Move to Glacier after 90 days
- Keep forever

---

## Console Example (Simple Workflow)
1. Go to S3 Bucket → Management
2. Create Lifecycle Rule
3. Choose “Apply to all objects” or filter by prefix
4. Select actions:
   - transition
   - expiration
   - non-current version expiration
   - delete marker cleanup
5. Add number of days
6. Save

---

## Apply to All vs Limit Scope

### Apply to All
Lifecycle applies to the entire bucket.
Used when bucket stores single data type (ex: logs-only bucket).

### Limit Scope (Prefix)
Different retention for different folders:
- logs/
- images/
- temp/

Used by real companies to control storage cost precisely.

---

## Best Practices
- Use prefixes (folders)
- Avoid transitions during free tier
- Apply different policies per data type
- Clean delete markers regularly
- Keep only required versions
- Test rules on non-production buckets

---

## Summary
Lifecycle automates cost optimization and data retention. It moves data to cheaper storage, deletes old data, manages version history, and cleans delete markers. Companies rely on lifecycle to prevent uncontrolled storage growth.

---

