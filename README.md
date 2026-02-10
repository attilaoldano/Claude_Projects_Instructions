# 🔧 Claude Projects - Dynamic Instructions System

A pattern for managing dynamic and updatable instructions in Claude projects, without needing to manually modify the project each time.

---

## 🎯 Problem Solved

Claude projects have static instructions that require manual modification in the interface. This system allows you to:

- ✅ **Update instructions** without touching the Claude project
- ✅ **Version** instructions with Git
- ✅ **Share** the same instruction set across multiple projects
- ✅ **Separate** local configuration from business logic

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      CLAUDE PROJECT                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────────┐    ┌─────────────────────┐            │
│  │  Instructions.txt   │    │ Personalization.txt │            │
│  │  ─────────────────  │    │  ─────────────────  │            │
│  │  URLInstructions =  │    │  DirectoryFile = .. │            │
│  │  https://github...  │    │  ApiKey = ...       │            │
│  └──────────┬──────────┘    │  CustomParam = ...  │            │
│             │               └─────────────────────┘            │
│             │                                                   │
│  ┌──────────▼──────────────────────────────────────────────┐   │
│  │              PROJECT INSTRUCTIONS                       │   │
│  │  ─────────────────────────────────────────────────────  │   │
│  │  "At the start of every conversation:                   │   │
│  │   1. Read Instructions.txt → extract URL                │   │
│  │   2. Fetch the MD file from GitHub                      │   │
│  │   3. Apply those instructions"                          │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ web_fetch
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                         GITHUB                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  repository/                                                    │
│  └── project/                                                   │
│      └── INSTRUCTIONS.md  ◄─── Complete instructions,          │
│                                 freely updatable                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📁 File Structure

### 1. `Instructions.txt` (in Claude project)

Contains **only** the pointer to remote instructions:

```ini
# ============================================
# INSTRUCTIONS URL CONFIGURATION
# ============================================

URLInstructions = https://raw.githubusercontent.com/USER/REPO/main/project/INSTRUCTIONS.md
```

### 2. `Personalization.txt` (in Claude project)

Contains user-specific **local parameters**:

```ini
# ============================================
# LOCAL CONFIGURATION
# ============================================

DirectoryFile = D:\Projects\MyProject\data
ApiEndpoint = https://api.example.com
MaxResults = 10
Language = EN
```

### 3. `INSTRUCTIONS.md` (on GitHub)

Contains the **complete logic** of the project:

```markdown
# Project Name

## Objective
[Description of what Claude should do]

## Files to Read
[Which files to process and where to find them]

## Processing Logic
[Algorithms, rules, calculations]

## Required Output
[Exact output format]

## Available Commands
[List of commands the user can give]
```

---

## ⚙️ Instructions to Insert in Claude Project

Copy this block into the **Claude project instructions**:

```markdown
# Dynamic Instructions from GitHub

**At the start of EVERY conversation**, before doing anything else:

1. **Read the `Instructions.txt` file** attached to the project
   and extract the `URLInstructions` value

2. **Retrieve the updated instructions** from the found URL

3. **Read and apply ALL instructions** contained in the downloaded file

4. **Proceed with the user's request** following those instructions

---

## Operating notes

- The instructions on GitHub are the source of truth and have priority
- If the fetch fails, inform the user and ask if they want to retry
- The `Instructions.txt` file must contain: `URLInstructions = [URL]`
```

---

## 🚀 Step-by-Step Setup

### 1. Create the GitHub repository

```bash
# Create the structure
mkdir -p claude-instructions/my-project
cd claude-instructions/my-project

# Create the instructions file
touch INSTRUCTIONS.md
```

### 2. Write instructions in `INSTRUCTIONS.md`

```markdown
# My Project

## Objective
Analyze CSV files in the configured directory...

## Processing
1. Read the most recent file
2. Apply logic X
3. Generate output Y

## Output
[Output format]
```

### 3. Publish on GitHub

```bash
git add .
git commit -m "Project instructions"
git push origin main
```

### 4. Get the raw URL

Format: `https://raw.githubusercontent.com/USER/REPO/BRANCH/PATH/FILE.md`

Example: `https://raw.githubusercontent.com/mario/claude-instructions/main/trading/INSTRUCTIONS.md`

### 5. Create the Claude project

1. Go to **claude.ai** → **Projects** → **New Project**
2. Add `Instructions.txt` file:
   ```
   URLInstructions = https://raw.githubusercontent.com/...
   ```
3. Add `Personalization.txt` file with local parameters
4. In the **project instructions**, paste the block from the ⚙️ section

---

## 📝 Usage Examples

### Example 1: Data Analysis Project

**Personalization.txt:**
```ini
DirectoryFile = C:\Users\Mario\Data\Sales
OutputFormat = Excel
Language = EN
```

**INSTRUCTIONS.md (GitHub):**
```markdown
# Sales Analysis

## Read from
Directory specified in `DirectoryFile`

## Processing
1. Load all CSVs
2. Calculate totals by category
3. Generate report
```

### Example 2: Multi-Environment Project

**Personalization.txt (DEV):**
```ini
Environment = development
ApiUrl = https://dev.api.example.com
Debug = true
```

**Personalization.txt (PROD):**
```ini
Environment = production
ApiUrl = https://api.example.com
Debug = false
```

Same `INSTRUCTIONS.md` for both, different behavior based on parameters.

---

## ✅ Benefits

| Aspect | Without system | With system |
|---------|---------------|-------------|
| **Updates** | Manual modification in project | Push to GitHub |
| **Versioning** | None | Complete Git history |
| **Sharing** | Copy-paste | Same URL |
| **Rollback** | Impossible | `git revert` |
| **Collaboration** | Difficult | PRs and reviews |
| **Local parameters** | Hardcoded | Separate file |

---

## ⚠️ Limitations

- **Requires connection**: Claude must be able to fetch the URL
- **Public repository**: The raw URL must be accessible (or use a token)
- **Rate limits**: GitHub has limits on raw requests (but generous)
- **Latency**: Small overhead for the initial fetch

---

## 🔒 For Private Repositories

If the repository is private, use a Personal Access Token:

```ini
URLInstructions = https://raw.githubusercontent.com/USER/REPO/main/INSTRUCTIONS.md
GitHubToken = ghp_xxxxxxxxxxxx
```

And in the project instructions, specify to use the token in the Authorization header.

---

## 📚 Best Practices

1. **One INSTRUCTIONS.md file per project** - Maintain clear separation
2. **Sensitive parameters in Personalization.txt** - Never on GitHub
3. **Use branches for versions** - `main` = stable, `dev` = test
4. **Document parameters** - Comment what each parameter does
5. **Test modifications** - Verify on a test project first

---

## 🤝 Contributing

This pattern is open and adaptable. Feel free to:
- Fork and customize
- Propose improvements
- Share variants for specific use cases

---

## 📄 License

MIT - Use freely for your projects.
