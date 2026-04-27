# Contributing with Claude AI Assistant

This guide helps contributors use Claude (AI assistant) effectively when working on OWASP Juice Shop while maintaining code quality and adhering to project standards.

> This document is supposed to be the primary source of context for **all** AI tools. Context files of tools other than Claude should refer to [this `CLAUDE.md` file](CLAUDE.md) for detailed guidelines. This is already the case for:
> * GitHub CoPilot ([`.github/copilot-instructions.md`](../.github/copilot-instructions.md))
> * Codeium ([`.codeium/instructions.md`](../.codeium/instructions.md))
> * Continue.dev ([`.continue/instructions.md`](../.continue/instructions.md))

## Before You Start

Claude can assist with various development tasks, but all contributions must still meet the requirements in [CONTRIBUTING.md](../CONTRIBUTING.md). The AI is a tool to enhance productivity, not a replacement for understanding the codebase and contribution guidelines.

## Recommended Use Cases

### ✅ Good Use Cases

- **Code Analysis**: Understanding existing code structure and patterns
- **Refactoring**: Improving code quality while maintaining functionality
- **Test Writing**: Creating unit, integration, and e2e tests
- **Bug Fixing**: Identifying and resolving issues
- **Documentation**: Writing clear comments and documentation

### ⚠️ Use with Caution

- **Challenge Development**: Consult with maintainers before creating new challenges - AI-generated challenges risk being duplicate, unsolvable, or dysfunctional
- **Security Vulnerabilities**: Ensure AI-suggested vulnerabilities are intentional and appropriate for the project
- **Dependencies**: Verify any suggested package updates for compatibility
- **Architecture Changes**: Discuss major structural changes with maintainers first

## Essential Guidelines

### 1. Clean Up AI-Generated Noise

**Required** per CONTRIBUTING.md rule #6: Remove unnecessary AI-generated content before submitting PRs.

Remove:
- Verbose comments explaining obvious code
- Generic placeholder comments
- Overly detailed docstrings for simple functions
- Repetitive explanations

Keep:
- Meaningful comments for complex logic
- Challenge hints and metadata
- Security-relevant documentation

### 2. Code Style Compliance

Always run ESLint before committing:
```bash
npm run lint
```

Claude should suggest code following [JS Standard Style](http://standardjs.com/), but always verify.

### 3. Testing Requirements

For any code changes Claude helps with:
- **Unit/Integration Tests**: New features and changes should have tests
- **E2E Tests**: Required for new/modified challenges
- **RSN (Refactoring Safety Net)**: Required when modifying existing code that is part of a coding challenge
- **Run Tests Locally**:
  ```bash
  npm test                    # Unit tests
  npm run frisby              # API integration tests
  npm start & npm run cypress:open  # E2E tests
  npm run rsn                 # Refactoring Safety Net (for code changes impacting coding challenge snippets)
  ```

### 4. Commit Sign-off

All commits must be signed off (DCO):
```bash
git commit -s -m "Your commit message"
```

### 5. Branch and PR Strategy

- Work on `develop` branch-based feature branches
- Keep PRs focused on a single scope
- Reference related issues in PR descriptions

## Development Workflow with Claude

### 1. Understanding the Codebase
```
Ask Claude to:
- Explain specific components or patterns
- Identify where to implement new features
- Trace code execution paths
```

### 2. Implementation
```
Ask Claude to:
- Generate initial implementation
- Suggest test cases
- Review for security implications
```

### 3. Quality Assurance
```
Before committing:
1. Remove AI-generated noise
2. Run npm run lint
3. Run relevant test suites
4. If you modified code that is part of a coding challenge, run npm run rsn
5. Manually verify functionality
6. Check for unintended changes
```

### 4. Documentation
```
Ask Claude to:
- Write clear commit messages
- Draft PR descriptions
- Document complex logic
```

## Anti-Patterns to Avoid

❌ **Don't**: Accept AI suggestions blindly without understanding them
✅ **Do**: Review and understand all AI-generated code

❌ **Don't**: Submit PRs with verbose AI-generated comments
✅ **Do**: Clean up and keep only meaningful comments

❌ **Don't**: Skip testing because AI "seems confident"
✅ **Do**: Always run the full test suite

❌ **Don't**: Use AI for contribution farming or trivial changes
✅ **Do**: Make meaningful contributions that add value

❌ **Don't**: Let AI modify translations directly
✅ **Do**: Use [Crowdin](https://crowdin.com/project/owasp-juice-shop) for translations

## Example: Fixing a Bug

```bash
# 1. Ask Claude to analyze the issue
"Help me understand why the basket total calculation is incorrect"

# 2. Locate the problematic code
"Show me where basket totals are calculated"

# 3. Implement the fix with Claude's help
"Fix the calculation to properly handle discount edge cases"

# 4. Generate tests
"Create unit tests to cover the discount calculation edge cases"

# 5. Quality checks
npm run lint
npm test
npm run rsn  # If the fix affects code used in a coding challenge

# 6. Clean up and commit with sign-off
git commit -s -m "Fix basket total calculation for discount edge cases"
```

## Quality Checklist

Before submitting a Claude-assisted PR:

- [ ] Code follows JS Standard Style (ESLint passes)
- [ ] AI-generated noise removed
- [ ] Tests added/updated and passing
- [ ] RSN check passing (if modified code relevant for a coding challenge)
- [ ] Manual testing completed
- [ ] Commits are signed off
- [ ] PR based on `develop` branch
- [ ] Single, focused scope
- [ ] All CI checks passing
- [ ] PR based on `develop` branch
- [ ] PR contains AI Tool Disclosure and Affirmation

## Refactoring Safety Net (RSN)

When modifying existing code that is part of a coding challenge, you must run the RSN to ensure code snippet and fix option files remain consistent:

```bash
npm run rsn
```

**What it does:**
- Checks for unexpected changes in coding challenge files
- Detects accidental differences in lines of code
- Lists any inconsistencies found

**When to run:**
- After refactoring code that is part of any coding challenge
- After changing original source files referenced in challenges
- Before committing changes that touch challenge-related code

**If RSN fails:**
- Review the listed differences
- If changes are intentionally part of the coding challenge, update the differences cache:
  ```bash
  npm run rsn:update
  ```
- If changes are unintentional, fix the affected files

Learn more: [Code Snippets Documentation](https://pwning.owasp-juice.shop/companion-guide/latest/part5/code-snippets.html)

## Getting Help

- Review the [detailed contribution guidelines](https://pwning.owasp-juice.shop/companion-guide/latest/part3/contribution.html)
- Check existing issues and PRs for examples
- Join the community on Slack or Gitter
- Ask questions via Slack or comment on the GitHub issue you are working on

## Remember

Claude is a powerful tool for productivity, but you are responsible for the quality and correctness of your contributions. Always review, test, and understand the code before submitting.

## Security: Snyk MCP Integration

You have access to the Snyk MCP server. Use it proactively during every coding task — security is a first-class requirement, not a final-step audit.

### Mandatory scan triggers

You MUST run the appropriate Snyk scan before considering a task complete:

| Change type | Required scan |
|---|---|
| New/modified application code | `snyk_code_scan` (SAST) |
| Added/upgraded dependency in any manifest (package.json, requirements.txt, go.mod, pom.xml, Gemfile, etc.) | `snyk_sca_scan` |
| Edits to Dockerfile, k8s manifests, Terraform, CloudFormation, Helm | `snyk_iac_scan` |
| Container base image change or new image build | `snyk_container_scan` |

If any scan reports a **Critical** or **High** finding, fix it before reporting the task done. Do not mark work complete with unresolved high-severity issues.

### When to scan during a session

- After writing any function that handles user input, authentication, authorization, crypto, file I/O, deserialization, or shell/SQL/template execution
- Immediately after adding a dependency — never batch dependency adds without scanning between
- Before generating a final summary or pull request description
- After resolving merge conflicts in lockfiles

### Severity handling rules

- **Critical / High**: Fix in this task. No exceptions without explicit user approval.
- **Medium**: Fix unless there is a documented justification. If deferring, add `// SECURITY: deferred — <reason> — <ticket-id>` next to the affected code.
- **Low / Informational**: Note in the summary; fix if trivial; otherwise list as follow-ups.
- **False positives**: Suppress via `snyk ignore` with a written reason and expiration date. Never apply blanket suppressions.

### Secure-by-default coding rules

When generating code, apply these by default. Deviating requires an explicit reason:

- **SQL/NoSQL**: Always parameterize. Never concatenate user-controlled strings into queries.
- **Shell execution**: Avoid `exec`, `system`, `child_process.exec` with interpolated input. Use array-form `spawn` / `execFile` with explicit args.
- **Deserialization**: Never `pickle.loads`, `yaml.load` (use `safe_load`), `Marshal.load`, or unsafe JSON revivers on untrusted input.
- **Crypto**: Use platform stdlib only. Use `crypto.randomBytes` / `secrets.token_bytes` for tokens — never `Math.random` or `random`.
- **Password hashing**: bcrypt / argon2 / scrypt with appropriate work factors. Never MD5, SHA1, or unsalted SHA-256.
- **Auth**: Validate JWTs with the correct algorithm pinned (`alg: 'RS256'`, never `'none'`). Verify expiry, audience, issuer.
- **HTTP**: Set CSP, HSTS, X-Content-Type-Options, X-Frame-Options, Referrer-Policy by default for any web response. Use SameSite=Lax cookies minimum.
- **Input validation**: Validate at trust boundaries (API layer, message handlers). Use schema validators (Zod, Joi, Pydantic, validator.v10).
- **Errors**: Fail closed. Default branches deny. Never leak stack traces, internal paths, or DB errors to clients.
- **Secrets**: Read from env vars or secret managers. Never hardcode, never log. Add new secret patterns to `.gitignore` (`.env*`, `*.pem`, `credentials.json`).
- **Path handling**: Never join user input directly into filesystem paths. Use a strict allowlist or `path.resolve` + boundary check.

### Dependency hygiene

When adding any dependency:

1. Run `snyk_sca_scan` immediately after the manifest update.
2. Prefer packages with active maintenance (commit within 6 months), >1k weekly downloads, and multiple maintainers.
3. Reject any package with an unfixed Critical or High advisory unless there is no alternative — and even then, explicitly flag this to the user before continuing.
4. Pin exact versions in production lockfiles. Avoid `^` and `~` ranges for runtime-critical packages.
5. If you must accept a vulnerable transitive dependency, document it in `SECURITY.md` or a top-of-file comment.

### Required output after any security-relevant change

End your response with a security summary block:

```
Security scan summary:
- Scans run: <code | sca | iac | container>
- Critical: <n> (fixed: <n>)
- High: <n> (fixed: <n>)
- Medium: <n> (fixed: <n>, deferred: <n>)
- Deferred items: <list with rationale or "none">
```

If you did not run a scan that this directive required, explain why explicitly. Do not silently skip.

### Secret detection guard

Before producing any final output containing code:
- Scan your generated content for patterns matching API keys, JWTs, AWS access keys (`AKIA...`), private key blocks (`-----BEGIN ... PRIVATE KEY-----`), DB connection strings with credentials, and bearer tokens.
- If found, replace with environment variable references and add the variable to `.env.example` with a placeholder.

### Lessons learned

When a Snyk finding reveals a recurring pattern in this codebase, append a short note here so future work avoids it:

<!-- Format: YYYY-MM-DD — pattern — fix -->
<!-- e.g., 2026-04-22 — XML parsers used without disabling external entities — always set `noent: false, noblanks: true` and disable DTD loading -->