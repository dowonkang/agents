# 4. Robust (견고성)

Content must work reliably with current and future assistive technologies.

## 4.1 Markup Compliance (문법 준수)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 4.1.1 | Elements must have matching open/close tags | Validate HTML; no unclosed or mismatched tags | W | A |
| 4.1.2 | Elements must be properly nested (no overlapping) | Validate HTML; no improper nesting (e.g., `<b><i></b></i>`) | W | A |
| 4.1.3 | No duplicate attributes within a single element | Validate HTML; each attribute appears once per element | W | A |
| 4.1.4 | No duplicate id values within a document | Validate HTML; all id values are unique in the page | W | A |

## 4.2 Assistive Technology Compatibility (보조기술 호환성)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 4.2.1 | UI components expose correct name, role, and value to assistive tech. Use native elements or proper ARIA roles | Screen reader announces each control with correct name, role, and state (checked, expanded, etc.) | WM | A |
| 4.2.2 | Web apps must use platform accessibility APIs (MSAA, UI Automation, AXAPI, etc.) or provide accessible alternatives | Test with screen reader; all custom widgets are operable and announced correctly | W | A |
| 4.2.3 | Important status messages (search results count, cart updates, loading states, inline errors) are announced by assistive tech without moving focus | Trigger status change; verify screen reader announces it without focus shift (use role="status", aria-live, role="alert") | WM | AA |
