---
name: korean-io-english-processing
description: >
  Korean I/O with English internal processing for token efficiency. When the
  user writes in Korean (or a mix including Korean), process all internal
  reasoning, tool calls, and MCP interactions in English, then deliver the
  final response in Korean. Always trigger this skill when the user's message
  contains Korean. The only exception is when the user explicitly requests an
  English response.
---

# Korean I/O · English Internal Processing

Apply the **Input (Korean) → Internal processing (English) → Output (Korean)** pipeline to save tokens.

---

## Why

Korean text consumes ~1.5–2× more tokens than equivalent English text.
By processing internally in English — reasoning, tool calls, intermediate results —
you save context window budget while still delivering natural Korean responses.

---

## Pipeline

```
User (Korean input)
        │
        ▼
┌──────────────────────────────────┐
│  1. Parse intent (in English)    │
│  2. Plan steps (in English)      │
│  3. Tool / MCP calls (English)   │
│  4. Intermediate reasoning (Eng) │
│  5. Synthesize results (English) │
└──────────────────────────────────┘
        │
        ▼
Final response (Korean output)
```

---

## Rules

### 1. Internal processing — use English

Perform ALL of the following in **English**:

- **Thinking / chain-of-thought**: All internal reasoning in English.
- **Tool calls**: Every parameter (`query`, `command`, `description`, etc.) for `web_search`, `web_fetch`, `bash_tool`, `view`, `create_file`, `conversation_search`, etc. must be written in English.
- **MCP calls**: Input parameters for Slack, Gmail, Google Calendar, Notion, etc. in English. Exception: message body intended for the end recipient should match the recipient's language.
- **Intermediate analysis**: Interpreting tool results, debugging, code comments — all in English.
- **File internals**: Code comments, commit messages, variable names — keep English as per convention.

### 2. Final output — use Korean

The **user-facing response text** must be in **Korean**.

- Translate technical terms to Korean, with English in parentheses where helpful.
  - e.g., "의존성 주입(Dependency Injection)"
- Leave code blocks, CLI commands, file paths, and URLs in English as-is.
- Write natural Korean. Avoid translationese (직역투). Prefer conversational tone.

### 3. Exceptions

- If the user **explicitly requests English output**, respond in English.
- When quoting English content from the user's message (e.g., code review), keep the English as-is.
- **Draft messages** (email, Slack) intended for others should match the recipient's language.

---

## Examples of English tool usage

### web_search

```
User: "최근 리눅스 커널 6.x 변경사항 알려줘"

→ web_search query: "Linux kernel 6.x recent changes changelog"
  (NOT: "리눅스 커널 6.x 최근 변경사항")
```

### bash_tool

```
User: "현재 디렉토리 구조 보여줘"

→ bash_tool description: "List directory structure"
→ bash_tool command: "find . -maxdepth 2 -type f | head -30"
```

### MCP (Slack)

```
User: "Slack에서 #general 채널 최근 메시지 확인해줘"

→ slack_search_channels query: "general"
→ slack_read_channel (English parameters)
→ Summarize results in Korean for the user
```

### conversation_search

```
User: "지난번에 btrfs 설정한 거 찾아줘"

→ conversation_search query: "btrfs setup configuration"
  (NOT: "btrfs 설정")
```

---

## Quality checklist

Before sending the final response, verify:

- [ ] All internal processing (thinking, tool description/query) is in English.
- [ ] Final response is natural Korean.
- [ ] Technical terms have English in parentheses where appropriate.
- [ ] Code, commands, paths, and URLs remain in English.
- [ ] No translationese — reads like native Korean.
