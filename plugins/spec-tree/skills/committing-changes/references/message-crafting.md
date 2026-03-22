<overview>
Detailed guidance for crafting commit message descriptions. Read when writing the actual commit message — not needed during staging or classification.
</overview>

<description_guidelines>

**Write for the reader, not the writer.**

Someone scanning `git log --oneline` needs to understand what changed without opening the commit.

**Principle 1: No State Words**

Describe the action, not the prior problem:

```text
# ❌ Describes prior state
fix: handle missing config file
spec(auth): add missing validation rules

# ✅ Describes the action
fix: return defaults when config absent
spec(auth): specify validation rules
```

Avoid: "missing", "broken", "wrong", "bad", "incorrect"

**Principle 2: Content Over Container**

Describe WHAT changed, not WHICH files:

```text
# ❌ Describes the container
spec(session): add stories for timeout feature
docs: update README file

# ✅ Describes the content
spec(session): specify timeout and cleanup behaviors
docs: add installation prerequisites
```

**Principle 3: Don't Repeat the Prefix**

The type already tells you what kind of change:

```text
# ❌ Redundant - prefix already says it's a spec
spec(session): add session management spec
spec(auth): define auth feature stories

# ✅ Just describe the content
spec(session): specify timeout and cleanup behaviors
spec(auth): specify OAuth2 token lifecycle
```

</description_guidelines>

<examples>

**Good Examples**

```text
feat(parser): add support for nested expressions

Enables users to write complex queries with unlimited nesting depth.
Previously limited to 3 levels.

Refs: #234
```

```text
fix: prevent crash on empty config file

Return sensible defaults when config is missing or empty
instead of throwing unhandled exception.
```

```text
refactor: extract validation logic into separate module

Prepares codebase for unit testing by isolating validation
from business logic.
```

**Bad Examples**

```text
# Too vague
fix: bug fixes

# Multiple unrelated changes
feat: add parser and fix tests and update docs

# Contains attribution (NEVER do this)
feat: add export feature (by John)

# Not atomic
refactor: various improvements

# Describes prior state instead of action
fix: handle missing config
spec(auth): add missing validation

# Describes container instead of content
spec(session): add stories for advanced operations
docs: update README file

# Repeats the prefix
spec(session): add session spec
test(auth): add auth tests
```

</examples>
