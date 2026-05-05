# memory-vault

Cross-conversation memory and recall skill. Retrieves anything from any past Claude
conversation — code, designs, decisions, plans, research, or any previous output.

## What It Does

Drop this skill into a Claude Project and Claude gains the ability to systematically
search across your entire conversation history and reconstruct past context. Instead of
"I don't have access to previous conversations", Claude actively searches, finds, and
brings the relevant content into the current chat.

## Triggers On

- "Find the code you wrote for..."
- "What did we decide about..."
- "Remember when we built..."
- "What was that thing we worked on..."
- "Recall our conversation about..."
- "What do you know about me from past chats"
- Any vague memory request: "wasn't there something about X"

## Recall Types Handled

| Type | Example |
|---|---|
| Code | "Find the React component you built for my dashboard" |
| Design | "What did the UI we designed for X look like" |
| Decisions | "What architecture did we settle on for Y" |
| Plans | "What was our roadmap for Z" |
| Research | "What did we find out about X" |
| Timeline | "What did we work on last week" |
| Fuzzy | "There was something about authentication we discussed..." |

## Output Format

```
📌 FOUND: [what was located]
📅 FROM: [approximate timeframe]
─────────────────────────────
[The actual content — code, decision, plan, etc.]
─────────────────────────────
CONTEXT: [what project it was part of]
STATUS: [if there were follow-ups or it's still in progress]
```

## Notes

- Searches across all conversations in your Claude account
- Honest about confidence: "I found this" vs "I think this is related"
- Gracefully handles partial matches and not-found cases
- Does not fabricate — if unsure, searches again
