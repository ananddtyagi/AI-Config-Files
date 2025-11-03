# Claude Code Issue to PR Workflow Collection

A comprehensive set of GitHub Actions workflows that automate your development process from idea to pull request using Claude Code AI agents.

## 🎯 Overview

This collection provides **4 powerful workflows** that can work independently or together:

1. **Issue Creator** - Generate detailed GitHub issues from brief descriptions
2. **Issue to PR** - Automatically implement any GitHub issue and create a PR
3. **Full Flow** - Chain issue creation and implementation in one go
4. **PR Review** - Automated code review for pull requests

## 📋 Workflows

### 1️⃣ Issue Creator (`claude-code-issue-creator.yml`)

**Trigger**: Manual (`workflow_dispatch`)

**What it does**: Takes a brief description and generates a comprehensive, well-researched GitHub issue.

**Inputs**:
- `issue_description` - Brief description (e.g., "Add dark mode support")
- `priority` - Priority level (low/medium/high/critical)
- `labels` - Comma-separated labels

**Example Use**:
```
Description: "Add user authentication"
Priority: high
Labels: "feature,security"
```

**Output**: Creates a detailed issue with:
- Problem statement and rationale
- Proposed solution with technical details
- Affected files (researched from your codebase)
- Acceptance criteria
- Testing strategy
- Complexity estimate

---

### 2️⃣ Issue to PR (`claude-code-issue-to-pr.yml`)

**Trigger**: Manual (`workflow_dispatch`)

**What it does**: Implements any existing GitHub issue autonomously and creates a pull request.

**Inputs**:
- `issue_number` - The issue number to implement (e.g., 42)
- `branch_prefix` - Branch name prefix (default: "feature")
- `draft_pr` - Create as draft PR (default: false)

**Example Use**:
```
Issue Number: 42
Branch Prefix: feature
Draft PR: false
```

**What Claude Does**:
1. **Research**: Explores codebase to understand architecture
2. **Implementation**: Writes code following your patterns
3. **Testing**: Adds/updates tests
4. **Documentation**: Updates docs and adds comments
5. **Git Operations**: Creates branch, commits, pushes
6. **PR Creation**: Creates PR with detailed description
7. **Linking**: Comments on original issue with PR link

---

### 3️⃣ Full Flow (`claude-code-full-flow.yml`)

**Trigger**: Manual (`workflow_dispatch`)

**What it does**: Complete end-to-end automation - creates issue AND implements it.

**Inputs**:
- `issue_description` - What to build
- `priority` - Priority level
- `labels` - Labels to add
- `branch_prefix` - Branch name prefix
- `draft_pr` - Create as draft PR
- `auto_implement` - Auto-implement after creating issue (default: true)

**Example Use**:
```
Description: "Add export to CSV feature"
Priority: medium
Labels: "enhancement,export"
Auto Implement: true
```

**The Flow**:
```
Your Input
    ↓
[Job 1: Create Issue]
    → Claude researches codebase
    → Generates detailed issue
    → Creates GitHub issue
    ↓
[Job 2: Implement Issue] (if auto_implement = true)
    → Claude researches implementation
    → Writes code + tests
    → Creates branch & commits
    → Pushes & creates PR
    → Links PR to issue
    ↓
Ready for Review!
```

---

### 4️⃣ PR Review (`claude-code-review.yml`)

**Trigger**: Automatic on PR open/update

**What it does**: Provides comprehensive code review with feedback on:
- Code quality and best practices
- Potential bugs or issues
- Performance considerations
- Security concerns
- Test coverage

**Customization Options** (see workflow comments):
- Filter by file types
- Filter by PR author
- Customize review focus areas
- Skip review for WIP/draft PRs
- Run tests/linting as part of review

---

## 🚀 Getting Started

### Prerequisites

1. **Claude Code OAuth Token**: Set up `CLAUDE_CODE_OAUTH_TOKEN` as a repository secret
   - Get your token from [Claude Code settings](https://claude.com/settings)

2. **Permissions**: Ensure the workflows have necessary permissions (already configured in files)

### Installation

1. Copy the `workflows` folder to your repository's `.github/workflows/` directory:
   ```bash
   cp -r workflows/* YOUR_REPO/.github/workflows/
   ```

2. Commit and push:
   ```bash
   git add .github/workflows/
   git commit -m "Add Claude Code automation workflows"
   git push
   ```

3. The workflows are now available in your repository's Actions tab!

---

## 💡 Usage Examples

### Scenario 1: Manual Step-by-Step Control

**Step 1**: Create an issue
- Go to Actions → "Claude Code Issue Creator"
- Enter description: "Add pagination to user list"
- Run workflow → Issue #123 created

**Step 2**: Review the generated issue
- Read issue #123, make any manual adjustments

**Step 3**: Implement when ready
- Go to Actions → "Claude Code Issue to PR"
- Enter issue number: 123
- Run workflow → PR #45 created

### Scenario 2: Fully Automated Development

- Go to Actions → "Claude Code Full Flow"
- Enter description: "Add email notifications for new messages"
- Set auto_implement: true
- Run workflow
- ✅ Issue created + PR created in one flow!

### Scenario 3: Existing Issues

Have existing issues created manually? No problem!

- Go to Actions → "Claude Code Issue to PR"
- Enter the issue number
- Claude will implement it and create a PR

---

## ⚙️ Configuration & Customization

### Model Selection

All workflows default to **Claude Sonnet 4**. To use **Claude Opus 4** (more capable, slower):

```yaml
# Uncomment this line in any workflow:
model: "claude-opus-4-20250514"
```

### Tool Access

Claude has access to these tools (configurable via `allowed_tools`):

**Issue Creator**:
- `Read, Glob, Grep` - Code exploration
- `Bash(git log), Bash(git grep)` - Git history

**Issue to PR / Full Flow**:
- `Read, Write, Edit` - File operations
- `Glob, Grep` - Code search
- `Bash(git *)` - Git operations (branch, commit, push, PR)
- `Bash(npm/yarn/pnpm *)` - Package management
- `Bash(python/pytest)` - Python testing

### Branch Naming

Customize branch prefixes:
- `feature` → `feature/issue-42`
- `fix` → `fix/issue-42`
- `enhancement` → `enhancement/issue-42`

---

## 🎓 How It Works

### Claude Code Agent Mode

These workflows use `mode: agent`, which gives Claude:
- **Autonomy**: Can execute multi-step plans independently
- **Tool Access**: Uses Read, Write, Git, Bash tools
- **Research Capability**: Explores codebase before coding
- **Decision Making**: Chooses best implementation approach

### Workflow Chaining

The Full Flow workflow uses GitHub Actions job dependencies:

```yaml
jobs:
  create-issue:
    # Creates issue, outputs issue_number
    outputs:
      issue_number: ${{ steps.create-github-issue.outputs.issue_number }}

  implement-issue:
    needs: create-issue  # Waits for create-issue job
    if: inputs.auto_implement == true  # Conditional execution
    # Uses issue_number from previous job
```

---

## 📊 Best Practices

### When to Use Which Workflow

| Workflow | Use When |
|----------|----------|
| **Issue Creator** | You want a well-researched issue but plan to code manually |
| **Issue to PR** | You have an existing issue and want automated implementation |
| **Full Flow** | You have an idea and want complete automation |
| **PR Review** | Automatic - runs on every PR |

### Tips for Better Results

1. **Be Specific**: More detailed descriptions → better issues and implementations
   - ❌ "Add search"
   - ✅ "Add full-text search to user list with filters by role and status"

2. **Use Labels**: Help categorize and filter
   - `enhancement`, `bug`, `security`, `ui`, `api`, etc.

3. **Review Before Merging**: Claude is good but not perfect
   - Always review generated PRs
   - Check tests pass
   - Verify edge cases

4. **Iterative Refinement**: You can run workflows multiple times
   - If implementation isn't perfect, provide feedback in PR comments
   - Create new issues for refinements

---

## 🔧 Troubleshooting

### Issue: PR URL not extracted

**Cause**: Claude's response format changed or PR creation failed

**Solution**: Check the execution file in workflow logs for Claude's full response

### Issue: Tests failing in PR

**Cause**: Claude may not have all context about test requirements

**Solution**: Add testing guidelines to the issue description or prompt

### Issue: Wrong files modified

**Cause**: Claude misunderstood the architecture

**Solution**:
- Add more context in issue description
- Reference specific files/paths in issue
- Use more specific file patterns in workflow

---

## 🤝 Contributing

This workflow collection is maintained by Anand Tyagi. Contributions welcome!

### Ideas for Enhancement

- Add automatic deployment after PR merge
- Integrate with project management tools
- Add automatic changelog generation
- Support for multi-repository changes

---

## 📝 License

MIT License - feel free to use and modify for your projects!

## 👤 Author

**Anand Tyagi** - [@ananddtyagi](https://github.com/ananddtyagi)

---

## 🔗 Resources

- [Claude Code Documentation](https://docs.claude.com/en/docs/claude-code)
- [Claude Code GitHub Action](https://github.com/anthropics/claude-code-action)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)