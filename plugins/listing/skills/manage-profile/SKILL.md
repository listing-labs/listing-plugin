---
name: manage-profile
description: View and update your listing.ai profile.
user-invocable: true
allowed-tools:
  - mcp__listing__get_profile
  - mcp__listing__update_profile
---

# Manage Profile Workflow

Help the user view and update their listing.ai profile.

## Workflow

### Step 1: Fetch Current Profile

Use `mcp__listing__get_profile` to fetch the current profile.

Display the profile in a readable format:

- **Name**: first_name last_name
- **Title**: title
- **Office**: office
- **Specialty**: specialty (comma-separated)
- **Bio**: bio
- **Phone**: phone_number
- **Links**: links (formatted as "name: url")
- **Published**: is_published (yes/no)

### Step 2: Ask What to Change

Ask the user what they'd like to update. They might want to:
- Edit their bio
- Update their title or office
- Add/change specialties
- Update contact info or links
- Publish or unpublish their profile

If the user wants to update their bio, help them draft a new one based on their current profile and any input they provide.

### Step 3: Apply Updates

Use `mcp__listing__update_profile` with only the changed fields.

### Step 4: Confirm

Fetch the updated profile with `mcp__listing__get_profile` and show the changes to confirm everything was applied correctly.

## Notes

- Always show the current profile before making changes so the user can see what they're working with.
- For bio updates, respect the 5000-character limit.
- The `links` field is a key-value object. To update links, send the entire links object (it replaces, not merges).
- If a tool call fails, show the error and suggest next steps.
