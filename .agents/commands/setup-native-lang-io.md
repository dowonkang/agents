---
description: Add native-language I/O with English internal processing rules
  to the project's agent instruction file (AGENTS.md, CLAUDE.md, etc.)
---

Follow these steps:

1. Scan the project root for known agent instruction files:
   `CLAUDE.md`, `AGENTS.md`, `.cursorrules`, `.windsurfrules`,
   `.clinerules`, `.github/copilot-instructions.md`
2. If exactly one found — use it.
   If multiple found — ask the user which one to update.
   If none found — ask the user which agent they use, then create the appropriate file.
3. Check if a `## Native Language I/O` section already exists in the target file.
   If present — inform the user it is already configured. Stop.
4. Ask the user to confirm adding the rules to `<detected file>`.
5. Append the following block to the target file with a blank line separator:

---

## Native Language I/O · English Internal Processing

When the user writes in a non-English language:

- **Internal processing in English**: reasoning, tool call parameters
  (search queries, shell commands, descriptions), intermediate analysis,
  code comments, and commit messages — all in English.
- **Final response in user's native language**: Translate technical terms
  with English in parentheses (e.g., "의존성 주입(Dependency Injection)").
  Leave code blocks, commands, paths, and URLs in English.
- **Language detection**: Identify from the first message; for mixed input,
  follow the dominant natural language; if the user switches, follow.
- **Exceptions**: Respond in English if explicitly requested. Draft messages
  (email, Slack) match the recipient's language.

---

6. Confirm what was added and to which file.
