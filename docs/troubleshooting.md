# 7. Troubleshooting & Resolution Guide

This guide helps diagnose and resolve common issues when working with multiple GitHub accounts using SSH.

If something isn't working as expected, find the matching error below and follow the recommended solution.

---

## Error 1: Permission Denied (Public Key)

### Symptoms

When running `git clone`, `git pull`, or `git push`, you see:

```text
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.
```

### Common Causes

* The SSH Agent is running, but no keys have been loaded.
* The wrong SSH key is being used.
* The repository remote URL uses the default GitHub host instead of your custom SSH alias.
* The public key was not added to GitHub.

---

### Step 1: Verify Loaded SSH Keys

Check which keys are currently loaded:

```bash id="drfjlwm"
ssh-add -l
```

If you see:

```text id="f3gmlf"
The agent has no identities.
```

Load your keys manually:

```bash id="a1o6ko"
ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_work
```

Verify again:

```bash id="8f8nuh"
ssh-add -l
```

---

### Step 2: Verify the Repository Remote URL

Check your repository configuration:

```bash id="p3quq7"
git remote -v
```

Incorrect:

```text id="1ol2wj"
git@github.com:username/project.git
```

Correct:

```text id="4xib36"
git@github.com-personal:username/project.git
```

or

```text id="0v4ebf"
git@github.com-work:company/project.git
```

Update the remote if necessary:

```bash id="gk5wcm"
git remote set-url origin git@github.com-work:company/project.git
```

---

### Step 3: Verify GitHub Authentication

Test your SSH alias:

```bash id="v8f6pz"
ssh -T git@github.com-personal
```

or

```bash id="a9wlk7"
ssh -T git@github.com-work
```

Expected output:

```text id="m2r7cn"
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## Error 2: Wrong GitHub Account Detected

### Symptoms

You attempt to push to a work repository, but GitHub identifies you as your personal account.

Example:

```text
Permission to company/project.git denied to personal-user.
```

### Cause

SSH is attempting multiple keys and authenticating with the wrong account.

---

### Solution

Open your SSH configuration:

```bash id="7uxslf"
nano ~/.ssh/config
```

Verify that every account configuration contains:

```ssh id="s4u7an"
IdentitiesOnly yes
```

Example:

```ssh id="zwv6nw"
Host github.com-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work
  IdentitiesOnly yes
```

This forces SSH to use only the specified key.

---

### Verify Which Key Is Being Used

Run:

```bash id="vgs1yr"
ssh -vT git@github.com-work
```

Look for:

```text id="cn7lbi"
Offering public key:
```

The displayed key should match the account you expect.

---

## Error 3: SSH Agent Not Running

### Symptoms

Linux or macOS:

```text
Could not open a connection to your authentication agent.
```

Windows:

```text
unable to start ssh-agent
```

### Cause

The SSH Agent service is not running.

---

### Linux/macOS Solution

Start the SSH Agent:

```bash id="ojy2ps"
eval "$(ssh-agent -s)"
```

Add your keys:

```bash id="vpokq9"
ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_work
```

---

### Windows Solution

Run PowerShell as Administrator:

```powershell id="p6pk4f"
Set-Service -Name ssh-agent -StartupType Automatic

Start-Service ssh-agent
```

Verify:

```powershell id="znr9mv"
Get-Service ssh-agent
```

Expected:

```text id="rwq9ii"
Status   Name
------   ----
Running  ssh-agent
```

---

## Error 4: SSH File Permissions Are Too Open

### Symptoms

Linux or macOS displays:

```text
Permissions 0777 for '/home/user/.ssh/config' are too open.
```

or

```text
It is required that your private key files are NOT accessible by others.
```

### Cause

SSH requires private keys and configuration files to be accessible only by the current user.

---

### Linux/macOS Solution

Restrict permissions:

```bash id="htkhv0"
chmod 700 ~/.ssh
chmod 600 ~/.ssh/config
chmod 600 ~/.ssh/id_ed25519_personal
chmod 600 ~/.ssh/id_ed25519_work
```

Verify:

```bash id="gupzzu"
ls -la ~/.ssh
```

---

### Windows Solution

If using Git Bash:

```bash id="6h4f3g"
chmod 600 ~/.ssh/config
chmod 600 ~/.ssh/id_ed25519_personal
chmod 600 ~/.ssh/id_ed25519_work
```

---

## Error 5: Public Key Not Added to GitHub

### Symptoms

SSH authentication fails even though your local configuration appears correct.

### Cause

The public key was never added to GitHub.

---

### Verify Your Public Key

Personal account:

```bash id="7q4mxp"
cat ~/.ssh/id_ed25519_personal.pub
```

Work account:

```bash id="2vt6rz"
cat ~/.ssh/id_ed25519_work.pub
```

Ensure the matching public key exists in:

```text id="g5mf8q"
GitHub → Settings → SSH and GPG Keys
```

---

## Error 6: Config File Not Being Read

### Symptoms

SSH ignores aliases such as:

```text
github.com-personal
github.com-work
```

### Cause

The configuration file is missing, incorrectly named, or contains syntax errors.

---

### Verify Configuration File

Display the file:

```bash id="v5igln"
cat ~/.ssh/config
```

Example:

```ssh id="eg7r89"
Host github.com-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal
```

---

### Debug Configuration Loading

Run:

```bash id="b79fl3"
ssh -vT git@github.com-personal
```

SSH will display which configuration file is being loaded.

---

## Error 7: Wrong Commit Email Address

### Symptoms

Authentication succeeds, but GitHub shows commits under the wrong email address.

### Cause

Git identity configuration does not match the account being used.

Remember:

* SSH controls authentication.
* Git config controls commit authorship.

---

### Verify Current Identity

```bash id="nmb2yc"
git config user.name
git config user.email
```

Check all active settings:

```bash id="tz4g3v"
git config --list
```

---

### Fix Repository Identity

```bash id="qv7pzn"
git config --local user.name "John Doe"
git config --local user.email "john.doe@company.com"
```

Verify:

```bash id="ckcq9n"
git config user.email
```

---

## Useful Debugging Commands

### List Loaded SSH Keys

```bash id="v8nnrl"
ssh-add -l
```

### Show Repository Remote

```bash id="u5knk8"
git remote -v
```

### Test Authentication

```bash id="u5ljzy"
ssh -T git@github.com-work
```

### Debug SSH Connection

```bash id="8utd4m"
ssh -vT git@github.com-work
```

### Check Current Git Identity

```bash id="mj74fd"
git config user.name
git config user.email
```

### View Full Git Configuration

```bash id="9juxry"
git config --list
```

---

## Still Having Issues?

If the problem persists, gather the following information before troubleshooting further:

1. Output of `ssh -T git@github.com-work`
2. Output of `ssh-add -l`
3. Output of `git remote -v`
4. Output of `git config --list`
5. Contents of your `~/.ssh/config` file (remove sensitive information)

These details usually identify the root cause within a few minutes.
