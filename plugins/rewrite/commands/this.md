---
description: Rewrite a rough prompt into a clear, specific request
argument-hint: <your rough prompt>
---

# Rewrite Prompt

Take the user's rough prompt and rewrite it into a clear, specific, well-structured request that will get better results from Claude Code.

## Input

The user provides their rough prompt as `$ARGUMENTS`. If no arguments provided, ask them to paste their prompt.

## Analysis Process

Before rewriting, analyze the original prompt for:

1. **Vagueness**: Words like "fix", "improve", "make better", "clean up" without specifics
2. **Missing context**: No mention of files, languages, frameworks, or constraints
3. **Unclear scope**: Could mean many different things
4. **Missing success criteria**: No way to know when it's "done"
5. **Implicit assumptions**: Things the user knows but didn't state

## Rewrite Principles

Apply these improvements:

### 1. Clarity & Specificity
- Replace vague verbs with concrete actions
- "fix the bug" → "fix the null pointer exception in UserService.getUser() that occurs when userId is undefined"
- "make it faster" → "reduce the API response time for /api/users endpoint, currently 2s, target <200ms"

### 2. Context & Constraints
- Specify relevant files, functions, or components
- Mention the tech stack if it matters
- Include constraints: "without breaking existing tests", "maintaining backward compatibility"
- Reference patterns already in the codebase: "following the same pattern as AuthController"

### 3. Structure & Format
- Break complex requests into numbered steps
- Specify expected output format if relevant
- Indicate priority if multiple things are requested

### 4. Success Criteria
- Add measurable or verifiable outcomes
- "tests should pass", "should handle edge case X", "should log errors to Y"

## Output Format

Present the rewritten prompt to the user in this format:

```
## Original prompt:
[their original text]

## Rewritten prompt:
[the improved version]

## What I improved:
- [bullet points explaining key changes]
```

Then ask: **"Would you like me to execute this prompt, adjust it further, or try a different approach?"**

## Examples

### Example 1: Vague bug fix

**Original**: "fix the login bug"

**Rewritten**: "Fix the login issue where users get a 401 error after entering correct credentials. Check the authentication flow in `src/auth/login.ts`, specifically the token validation logic. Ensure the fix handles both email and username login methods. Add a test case for this scenario."

**Improvements**:
- Added specific error symptom (401)
- Pointed to likely file location
- Specified scope (both login methods)
- Added testing requirement

### Example 2: Vague feature request

**Original**: "add dark mode"

**Rewritten**: "Add a dark mode toggle to the settings page that:
1. Persists user preference in localStorage
2. Applies a dark theme using CSS variables (check if `src/styles/variables.css` exists, otherwise create it)
3. Respects system preference as default (`prefers-color-scheme`)
4. Includes a smooth transition when switching themes
Follow the existing component patterns in `src/components/`."

**Improvements**:
- Broke into specific requirements
- Added persistence requirement
- Specified implementation approach
- Referenced existing patterns

### Example 3: Performance request

**Original**: "the dashboard is slow, make it faster"

**Rewritten**: "Investigate and fix performance issues on the dashboard page (`src/pages/Dashboard.tsx`):
1. Profile the component to identify slow renders
2. Check for unnecessary re-renders (missing memo, unstable references)
3. Look for N+1 query patterns in data fetching
4. Consider lazy loading for charts/widgets below the fold
Report findings before making changes."

**Improvements**:
- Specified the file to investigate
- Listed common performance issues to check
- Asked for analysis before changes
- Added specific techniques to consider

## Important Notes

- Preserve the user's intent - don't add requirements they didn't ask for
- Keep the rewrite concise - don't over-engineer simple requests
- If the original prompt is already good, say so and make only minor tweaks
- If the prompt is ambiguous in ways you can't resolve, include "[CLARIFY: ...]" markers
