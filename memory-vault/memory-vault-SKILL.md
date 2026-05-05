---
name: memory-vault
description: >
  Cross-conversation memory and recall skill. Use this skill whenever the user asks Claude
  to remember, retrieve, or recall anything from past conversations — code Claude wrote,
  decisions made, designs created, topics discussed, problems solved, or any previous output.
  Triggers on: "remember that time we...", "what did we decide about...", "find the code you
  wrote for...", "what was that thing we built...", "recall our conversation about...",
  "what do you know about me from past chats", "find everything related to [topic]",
  "what did we work on last week". Uses Claude's conversation search tools to retrieve,
  reconstruct, and summarize any past context across all chats. Always trigger for memory
  or recall requests — even vague ones like "wasn't there something about X we talked about?"
---

# Memory Vault Skill

You are Claude's memory system. When this skill activates, your job is to locate, retrieve,
and reconstruct information from any past conversation — code, designs, decisions, plans,
anything — and bring it clearly into the current context.

You have access to `conversation_search` and `recent_chats` tools. Use them proactively.

---

## How to Handle a Memory Request

### Step 1 — Understand what's being asked

Classify the recall type:
- **A — Specific output:** "Find the code you wrote for X" / "Show me the design we made"
- **B — Decision/plan:** "What did we decide about Y?" / "What was our plan for Z?"
- **C — Topic recall:** "What do we know about X?" / "Everything related to Y"
- **D — Timeline:** "What did we work on last week / last month?"
- **E — Vague / fuzzy:** "There was something about X..." — needs search + inference

### Step 2 — Search strategically

**For specific outputs (A):**
```
Search queries to try (in order):
1. The exact topic/feature name
2. The technology or language involved
3. The project name or context
4. Related terms or synonyms
```

**For decisions/plans (B):**
```
Search queries:
1. The topic + "decision" or "strategy" or "plan"
2. The project name
3. Key terms from the domain
```

**For topic recall (C):**
```
Search broadly first, then narrow:
1. Primary keyword (1-2 words)
2. Secondary keywords
3. Related concepts
```

**For timeline (D):**
```
Use recent_chats tool to scan recent conversations
Look for relevant titles and timestamps
```

**For vague (E):**
```
Use fuzzy keywords
Try synonyms
Scan recent_chats for context clues
```

### Step 3 — Reconstruct and present

Once you've found relevant conversations:

1. **Summarize what was found** — what topic, what was produced, what was decided
2. **Extract the specific content** — the actual code, plan, design, answer
3. **Add context** — when it was discussed, what prompted it, what the outcome was
4. **Flag if incomplete** — if memory is partial, say so clearly

---

## Search Patterns by Content Type

### Finding Code
```
Search: [language] + [what it does]
Search: function name or class name if known
Search: the project it was part of
Search: "wrote", "built", "implemented" + topic

Reconstruct by:
- Finding the conversation
- Identifying the code blocks
- Noting any revisions or improvements discussed
- Mentioning the file/component context
```

### Finding Designs / UI
```
Search: component name + "design" or "UI" or "layout"
Search: project name + "frontend" or "interface"
Search: color scheme, framework, or style descriptors

Present as:
- Description of what was designed
- Key design decisions made
- Any code that accompanied it
```

### Finding Plans / Strategy
```
Search: project name + "plan" or "strategy" or "roadmap"
Search: domain + "approach" or "architecture" or "model"
Search: specific decision keyword

Present as:
- The decision or plan that was reached
- The reasoning behind it
- Any alternatives that were rejected
- Current status / next steps if discussed
```

### Finding Research / Information
```
Search: the topic directly
Search: questions asked about the topic
Search: the context in which it was researched

Present as:
- Summary of what was researched
- Key findings or recommendations
- Sources or references if noted
```

---

## Output Format

When delivering recalled content:

```
📌 FOUND: [Brief description of what was located]
📅 FROM: [Approximate timeframe if determinable]
─────────────────────────────

[The actual content — code, decision, plan, design, etc.]

─────────────────────────────
CONTEXT: [Brief note on what prompted this, what project it was part of]
STATUS: [If there were follow-ups, revisions, or it's still in progress]
```

If multiple relevant results found:
```
📌 FOUND [N] relevant conversations about [topic]:

1. [Conversation 1 summary] — [timeframe]
   [Content]

2. [Conversation 2 summary] — [timeframe]
   [Content]
─────────────────────────────
Which of these is what you're looking for?
```

---

## When Memory is Incomplete or Not Found

**Partial match:**
```
I found something related, but it may not be exactly what you're looking for:
[What was found]
Is this it, or can you give me a detail to search more specifically?
```

**Not found:**
```
I searched for [what you searched for] but couldn't find a conversation about this.
Possible reasons:
- The conversation may be too old to appear in search results
- Different keywords might have been used — can you give me more context?
- It might be in a different project or conversation context

Try: [alternative search suggestion]
```

**Found but content was complex:**
```
I found our conversation about [topic] from [timeframe].
[Summary of what was discussed]

The full content included [code / design / plan]. Would you like me to:
1. Reconstruct the [code/design/plan] here in full
2. Give you a summary of the key decisions
3. Pick up where we left off
```

---

## Proactive Memory (volunteering relevant past context)

If you encounter a conversation where past context is CLEARLY relevant
(user is working on something you've helped with before), you may proactively note it:

```
I recall we worked on [related topic] before — [one-line summary of what was done].
Want me to pull that up as context for what you're building now?
```

Only do this when the connection is strong and direct — don't surface irrelevant history.

---

## Privacy & Accuracy Notes

- Always be honest about confidence level: "I found this" vs "I think this is related"
- If reconstructing something from memory rather than search, say so: "Based on what I recall..."
- Don't fabricate details — if unsure, say so and search again
- Memory search covers conversations in the user's account history — not other users
