# CLAUDE.md - Rust Project Development Rules

**STRICT RULES - MUST BE FOLLOWED!**

---

## Project-Specific Configuration

> Edit this section for each project. Everything below is the default template.

| Key | Value |
|-----|-------|
| **Source of Truth** | `docs/flow.md` |
| **Quick Reference** | `docs/CHEATSHEET.md` |
| **Build Command** | `LICENSE_PUBLIC_KEY="DEVELOPMENT_MODE" cargo build` |
| **Release Build** | `LICENSE_PUBLIC_KEY="$(cat keys/public.pem)" cargo build --release` |

### Project Notes
- All implementations MUST follow `docs/flow.md` - update it first if changes needed
- Don't make your own interpretations!

---

## Dependency Management

**NEVER edit `Cargo.toml` manually!**

```bash
cargo add <package>           # Add dependency
cargo add --dev <package>     # Dev dependency
cargo add --build <package>   # Build dependency
cargo remove <package>        # Remove dependency
```

---

## Git Workflow

```bash
# 1. Code & test
cargo test                    # MUST PASS
cargo test --test integration # Check integration tests

# 2. Format & lint
cargo fmt
cargo clippy                  # MUST HAVE NO WARNINGS

# 3. Stage selectively (DON'T stage everything!)
git add <specific files>      # EXCLUDE: screenshots, data/, keys/

# 4. Ask permission before commit
# 5. Commit only after approval
```

**Commit types:** `feat`, `fix`, `test`, `docs`, `refactor`, `chore`, `perf`

### Commit Request Format

```
Test passed | Formatted | No clippy warnings

May I commit?
Message: '<type>: <description>'

Summary:
- <what changed> (file.rs)
- <what added/removed> (other.rs)

Files: file.rs, other.rs
```

---

## TDD Workflow

**Red -> Green -> Refactor**

### Planning Phase (Red)
Show **expected behavior** to user, not code:

```
Expected behavior:
- User clicks "Save" -> data saved, toast "Success" appears
- Empty input -> button disabled, message "Required"
- Network error -> retry 3x, then show error message
```

### Implementation Phase (Green -> Refactor)
1. Write failing test first
2. Implement until test passes
3. Refactor if needed

### When Expectation Cannot Be Met
**STOP CODING!** Don't compromise without user permission.

1. Stop implementation
2. Explain why briefly
3. Provide multiple choice options:

```
Expectation "retry 3x then error" cannot be met because API doesn't support retry.

Options:
A) Client-side retry with 1s delay
B) Show error immediately without retry
C) Add loading state without retry
```

### Test Requirements
- Unit test for every function/method
- Integration test for every phase completion
- Mock external dependencies (serial port, file system)
- UI changes -> Playwright responsiveness test

---

## UI Testing (Playwright)

**Required for every UI/template change:**

Test viewports:
1. Desktop (1920x1080)
2. Tablet (768x1024)
3. Mobile (375x667)

Use Playwright MCP tools:
- `browser_navigate` - navigate to changed page
- `browser_resize` - test different screen sizes
- `browser_snapshot` - verify layout
- `browser_take_screenshot` - documentation (DON'T commit screenshots!)

---

## Release Workflow

```bash
git checkout main && git pull origin main
git tag vX.X.X
git push origin vX.X.X
```

GitHub Actions: cross-compile (aarch64 & x86_64) -> bundle scripts -> create release

---

## Critical Rules Summary

1. **Source of truth is law** - don't improvise
2. **Test first** - Red -> Green -> Refactor
3. **Ask before commit** - always
4. **cargo add only** - never touch Cargo.toml manually
5. **Format & lint** - before every git add
6. **UI changes** - test with Playwright

**Remember: Test -> Format -> Ask -> Commit**
