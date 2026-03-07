---
name: write-article
description: Research, draft, and publish an article on listing.ai.
user-invocable: true
allowed-tools:
  - mcp__listing__get_profile
  - mcp__listing__create_article
  - mcp__listing__update_article
  - WebSearch
---

# Write Article Workflow

Guide the user through writing and publishing an article on listing.ai.

## Input

The user may provide a topic or title as an argument (e.g. `/listing:write-article 5 tips for first-time homebuyers`). If no topic is provided, ask what they'd like to write about.

## Workflow

### Step 1: Research the Topic

Use WebSearch to gather current, relevant information about the topic. Identify key points, statistics, and angles that would make the article valuable and engaging.

Summarize your research findings for the user and propose an outline with:
- A working title
- 3-5 main sections
- Key points to cover

Ask the user to approve or adjust the outline before proceeding.

### Step 2: Write the Draft

Write a complete article based on the approved outline. The article should be:
- Written in **Markdown** format (raw HTML will not be rendered)
- Professional and informative
- Well-structured with clear headings
- Appropriate length (typically 800-1500 words)
- Written in the user's voice (use `mcp__listing__get_profile` to understand their role/specialty)

Present the full draft to the user for review.

### Step 3: Create as Draft on listing.ai

Once the user approves the content, use `mcp__listing__create_article` with `status: "draft"` to save it.

Show the user the created article's ID and confirm it was saved as a draft.

### Step 4: Publish (Optional)

Ask the user if they'd like to publish the article now or leave it as a draft for further editing.

If they want to publish, use `mcp__listing__update_article` with the article's `id` and `status: "published"`.

## Notes

- Always create articles as drafts first — never publish without explicit user approval.
- If a tool call fails, show the error and suggest next steps.
- The user can ask to revise the draft at any point before publishing.
