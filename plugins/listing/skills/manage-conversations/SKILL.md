---
name: manage-conversations
description: View and manage your listing.ai conversations and messages.
user-invocable: true
allowed-tools:
  - mcp__listing__list_conversations
  - mcp__listing__get_conversation
  - mcp__listing__create_conversation
  - mcp__listing__update_conversation
  - mcp__listing__delete_conversation
  - mcp__listing__list_messages
  - mcp__listing__send_message
---

# Manage Conversations Workflow

Help the user view and manage their conversations and messages on listing.ai.

## Input

The user may provide an action (e.g. `/listing:manage-conversations check my messages`) or a conversation context. If no input is provided, list their active conversations.

## Workflow

### Viewing Conversations

Call `mcp__listing__list_conversations` to show active conversations. Display each with:
- Conversation ID
- Members (other participant names)
- Last message preview
- Context type and context (if applicable)
- Unread status

Offer actions: read messages, send a reply, archive, or start a new conversation.

### Reading Messages

Call `mcp__listing__list_messages` with the conversation ID to show the message history. Display messages with:
- Sender name
- Message content
- Timestamp
- Message type (text, system, ai_response)

Use pagination parameters (`limit`, `offset`, `before`, `after`) for long conversations. Default is newest messages first.

After showing messages, mark the conversation as read using `mcp__listing__update_conversation` with `action: "mark_read"`.

### Sending a Message

Use `mcp__listing__send_message` with the conversation ID and message content. The default `message_type` is `"text"` which is correct for user messages.

Confirm the message was sent successfully.

### Starting a New Conversation

Use `mcp__listing__create_conversation` with:
- `recipient_id` (required) — the other user's UUID
- `message` (optional) — send an initial message with the conversation
- `context_type` (optional) — `"direct"`, `"profile"`, `"listing"`, or `"post"`
- `context_id` (optional) — ID of the related listing or post

If a conversation already exists with the same recipient and context, the existing conversation is returned instead of creating a duplicate.

### Archiving a Conversation

Confirm with the user before archiving. Use either:
- `mcp__listing__update_conversation` with `action: "archive"`
- `mcp__listing__delete_conversation` (same effect — soft delete via archive)

## Notes

- All conversation tools require authentication.
- Conversations are between two users — there are no group conversations.
- Archiving is a soft delete; the conversation can be found again by filtering with `status: "archived"`.
- If a tool call fails, show the error and suggest next steps.