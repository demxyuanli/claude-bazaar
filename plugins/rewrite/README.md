# Rewrite

Turn rough prompts into clear, specific requests that get better results from Claude Code.

## The Problem

We often type quick, vague prompts:
- "fix the bug"
- "make it faster"
- "add dark mode"

These work sometimes, but often lead to:
- Claude asking clarifying questions (extra round-trips)
- Solutions that miss the mark
- Changes you didn't want

## The Solution

Before sending a prompt, run it through `/rewrite:this` to get a clearer, more specific version that Claude can act on immediately.

## Installation

```bash
/plugin install rewrite@claude-bazaar
```

## Usage

```bash
/rewrite:this fix the login bug
```

Output:
```
## Original prompt:
fix the login bug

## Rewritten prompt:
Fix the login issue where users get a 401 error after entering correct
credentials. Check the authentication flow in `src/auth/login.ts`,
specifically the token validation logic. Ensure the fix handles both
email and username login methods. Add a test case for this scenario.

## What I improved:
- Added specific error symptom (401)
- Pointed to likely file location
- Specified scope (both login methods)
- Added testing requirement

Would you like me to execute this prompt, adjust it further, or try
a different approach?
```

## What It Improves

| Aspect | Before | After |
|--------|--------|-------|
| **Clarity** | "fix the bug" | "fix the null pointer in UserService.getUser()" |
| **Context** | (none) | "in src/services/user.ts, following existing patterns" |
| **Structure** | one vague sentence | numbered steps with priorities |
| **Success criteria** | (none) | "tests should pass, handles edge case X" |

## Tips

- Use it when you're not sure how to phrase a complex request
- Great for learning what makes a good prompt
- Skip it for simple, obvious tasks
