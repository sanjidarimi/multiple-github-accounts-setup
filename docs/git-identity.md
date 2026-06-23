# 6. Managing Git Commit Identity Profiles

After configuring SSH authentication for multiple GitHub accounts, there's another important piece to understand:

**Git authentication and Git commit identity are two separate things.**

SSH determines **which GitHub account can access a repository**, while Git identity determines **who appears as the author of a commit**.

---

## Why This Matters

> ⚠️ **Common Mistake**
>
> You successfully push code using your work GitHub account through SSH, but your commits are still attributed to your personal email address.
>
> Authentication succeeds, but commit authorship is incorrect.

For example:

* SSH Account: `github.com-work`
* Commit Email: `personal@gmail.com`

The code will push successfully, but GitHub will show the commit author as your personal identity.

This can create:

* Incorrect contribution history
* Corporate compliance issues
* Audit and tracking problems
* Confusing commit ownership

---

## Authentication vs Identity

| Layer          | Mechanism         | Configuration Location | Purpose                           |
| -------------- | ----------------- | ---------------------- | --------------------------------- |
| Authentication | SSH Key Pair      | `~/.ssh/config`        | Grants repository access          |
| Identity       | Git Configuration | `.gitconfig`           | Defines commit author information |

### Authentication Example

```bash id="u0x8e2"
ssh -T git@github.com-work
```

Determines which GitHub account is used.

### Identity Example

```bash id="vtmn4n"
git config user.email
```

Determines which email address appears in commits.

---

## Strategy A: Automatic Identity Switching (Recommended)

Instead of configuring every repository manually, Git can automatically select the correct identity based on the repository's location.

### Step 1: Organize Projects by Directory

Create dedicated folders for different account types.

```text id="l0r5h7"
~/Developer/
├── personal/
│   ├── portfolio
│   ├── open-source-project
│   └── side-project
│
└── work/
    ├── company-dashboard
    ├── internal-tools
    └── client-project
```

All personal repositories go into the `personal` folder.

All company repositories go into the `work` folder.

---

## Step 2: Configure Global Git Settings

Open your global Git configuration file:

```bash id="fahg9l"
nano ~/.gitconfig
```

Add the following configuration:

```ini id="2y1z8w"
[user]
    name = Your Full Name
    email = fallback-email@example.com

[includeIf "gitdir:~/Developer/personal/"]
    path = ~/.gitconfig-personal

[includeIf "gitdir:~/Developer/work/"]
    path = ~/.gitconfig-work
```

### Understanding the Configuration

#### Default Identity

```ini id="ik3r1n"
[user]
    name = Your Full Name
    email = fallback-email@example.com
```

Used when a repository does not match any conditional rule.

#### Conditional Includes

```ini id="3lndhz"
[includeIf "gitdir:~/Developer/personal/"]
```

Tells Git:

> If the repository is located inside `~/Developer/personal/`, load another configuration file.

The same applies to the work directory.

---

## Step 3: Create Personal Profile Configuration

Create:

```bash id="5slvud"
nano ~/.gitconfig-personal
```

Add:

```ini id="52i3ov"
[user]
    email = personal-email@gmail.com
```

Optionally:

```ini id="4e7uw9"
[user]
    name = John Doe
    email = personal-email@gmail.com
```

---

## Step 4: Create Work Profile Configuration

Create:

```bash id="w9kqlm"
nano ~/.gitconfig-work
```

Add:

```ini id="3mvfxu"
[user]
    email = employee-id@company.com
```

Or:

```ini id="k6xg8z"
[user]
    name = John Doe
    email = employee-id@company.com
```

---

## Step 5: Verify Automatic Switching

Navigate into a personal repository:

```bash id="gsav5x"
cd ~/Developer/personal/portfolio
git config user.email
```

Expected:

```text id="iwq5l7"
personal-email@gmail.com
```

Navigate into a work repository:

```bash id="jxxjmc"
cd ~/Developer/work/company-dashboard
git config user.email
```

Expected:

```text id="7c5pnf"
employee-id@company.com
```

Git automatically switches identities based on the repository location.

---

## Strategy B: Repository-Level Configuration

If you prefer not to organize repositories into dedicated folders, configure each repository individually.

Navigate to the repository:

```bash id="hx8kfr"
cd my-project
```

Set a repository-specific identity:

```bash id="vbkq8k"
git config --local user.name "John Doe"
git config --local user.email "john.doe@company.com"
```

These settings affect only the current repository.

---

## Verify Repository Identity

Check the active configuration:

```bash id="h7f5zc"
git config user.name
git config user.email
```

Or view all active settings:

```bash id="e7kxg4"
git config --list
```

---

## Understanding Configuration Levels

Git supports three configuration scopes.

| Scope  | Command    | Applies To              |
| ------ | ---------- | ----------------------- |
| System | `--system` | Entire operating system |
| Global | `--global` | Current user            |
| Local  | `--local`  | Current repository      |

Example:

```bash id="jglhkw"
git config --global user.email "personal@gmail.com"
```

Repository-specific settings always override global settings.

---

## Best Practice

For developers managing multiple GitHub accounts:

1. Use separate SSH keys for authentication.
2. Use SSH aliases for account routing.
3. Organize repositories into dedicated folders.
4. Use `includeIf` rules for automatic identity switching.
5. Verify your commit identity before making your first commit.

This approach removes manual configuration, prevents mistakes, and scales well whether you're managing two repositories or hundreds.
