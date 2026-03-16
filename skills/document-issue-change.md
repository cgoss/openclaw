# Skill: Document Feature Changes for GitHub Issues

## Purpose

This skill provides a workflow for documenting feature changes in GitHub issues with detailed implementation plans, including line numbers and before/after code examples.

## When to Use

- When planning a new feature or bug fix
- When you need to create a detailed implementation plan for a code change
- When you want to provide clear guidance for code reviewers or other developers

## Workflow

### Step 1: Analyze the Code

1. Read the relevant source files to understand the current implementation
2. Identify:
   - Exact file paths
   - Line numbers for changes
   - Type signatures
   - Function signatures
   - Existing imports

### Step 2: Determine Required Changes

1. Identify what imports need to be added
2. Determine what code needs to be modified
3. Note any new test cases needed
4. Estimate scope (lines of code)

### Step 3: Create the Issue Body

Create a GitHub issue with these sections:

```markdown
## Summary

Brief description of the change.

## Problem

What currently happens vs what should happen.

## Desired Behavior

Code example showing the expected flow.

## Configuration

Any new config needed (env vars, config files).

## Files to Modify

- `src/path/file.ts` - Description

## Technical Approach

High-level explanation of the implementation.

## Backward Compatibility

✅ Compatible or ⚠️ Breaking changes
```

### Step 4: Add Implementation Comment

Add a detailed comment with:

1. **Imports**: Exact line numbers and code to add
2. **Type Changes**: Before/after code with line numbers
3. **Code Changes**: Before/after with exact line references
4. **Tests**: Test cases to add
5. **Summary Table**: File, Lines, Change

Example format:

````markdown
### 1. Add import

**File:** `src/agents/model-fallback.ts`  
**Add after line 29:**

```typescript
import { collectProviderApiKeys } from "./live-auth-keys.js";
```
````

### 2. Modify function

**File:** `src/agents/model-fallback.ts`  
**Lines 40-44**

**Current:**

```typescript
type ModelFallbackRunFn<T> = (provider: string, model: string) => Promise<T>;
```

**Change to:**

```typescript
type ModelFallbackRunFn<T> = (
  provider: string,
  model: string,
  options?: { apiKey?: string },
) => Promise<T>;
```

````

## GitHub CLI Commands

```bash
# Check auth
gh auth status

# Create issue
gh issue create --title "[Feature] Title" --body "description" --repo owner/repo

# Add comment
gh issue comment ISSUE_NUM --body-file /path/to/body.txt --repo owner/repo

# View issue
gh issue view ISSUE_NUM --repo owner/repo --comments
````

## Tips

- Always verify the issue was created correctly with `gh issue view`
- Use line numbers from the actual source files
- Include before AND after code examples
- Keep backward compatibility notes clear
- Add summary table for quick reference
