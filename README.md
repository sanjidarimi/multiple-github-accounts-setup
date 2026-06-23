

### 1. `README.md` (Linux)

```markdown
# Managing Multiple GitHub Accounts on a Single Machine (Linux)

This guide helps you set up and seamlessly switch between multiple GitHub accounts (e.g., Personal and Work) on a single Linux or macOS machine using SSH configurations.

## 🚀 Step-by-Step Setup

### 1. Generate Separate SSH Keys
Create a unique SSH key for each GitHub account. Open your terminal and run:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"

```

When prompted to save the file, give it a specific name to avoid overwriting your default keys:

```text
/home/username/.ssh/id_ed25519_filename

```

*(e.g., `id_ed25519_personal` and `id_ed25519_work`)*

### 2. Add Keys to the SSH Agent

Start the background SSH agent and register your newly created keys:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_filename

```

### 3. Add SSH Keys to GitHub

Copy your public key to your clipboard:

```bash
cat ~/.ssh/id_ed25519_filename.pub

```

Go to **GitHub → Settings → SSH and GPG Keys → New SSH Key**, paste the content, and save.

### 4. Configure the SSH Config File

Open or create your SSH configuration file:

```bash
nano ~/.ssh/config

```

Add distinct host configurations for your accounts:

```text
# Personal Account
Host github.com-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal

# Work Account
Host github.com-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work

```

### 5. Test the Connections

Verify that your SSH authentication works perfectly:

```bash
ssh -T git@github.com-personal
ssh -T git@github.com-work

```

*Expected Output:* `Hi username! You've successfully authenticated, but GitHub does not provide shell access.`

---

👉 Looking for Windows instructions? Check out [WINDOWS_SETUP.md](https://www.google.com/search?q=./WINDOWS_SETUP.md).

⚠️ Running into issues? Check out [TROUBLESHOOTING.md](https://www.google.com/search?q=./TROUBLESHOOTING.md).

```

---

### 2. `WINDOWS_SETUP.md`

```markdown
# Windows Setup: Multiple GitHub Accounts

Follow these steps to configure multiple GitHub accounts on Windows using PowerShell or Git Bash.

## 🛠️ Step-by-Step Setup

### 1. Generate Separate SSH Keys
Open PowerShell or Git Bash and run:
```bash
ssh-keygen -t ed25519 -C "your-email@example.com"

```

Save the files with recognizable names inside your `.ssh` directory:

```text
C:\Users\YourUsername\.ssh\id_ed25519_personal
C:\Users\YourUsername\.ssh\id_ed25519_work

```

### 2. Start and Configure the SSH Agent

Open PowerShell as **Administrator** and run the following to ensure the SSH service runs automatically:

```powershell
Get-Service ssh-agent | Set-Service -StartupType Automatic
Start-Service ssh-agent

```

Now, add your specific keys to the agent:

```powershell
ssh-add $env:USERPROFILE\.ssh\id_ed25519_personal
ssh-add $env:USERPROFILE\.ssh\id_ed25519_work

```

To verify your loaded keys:

```powershell
ssh-add -l

```

### 3. Add SSH Keys to GitHub

Retrieve your public key:

```powershell
Get-Content $env:USERPROFILE\.ssh\id_ed25519_personal.pub

```

Go to **GitHub → Settings → SSH and GPG Keys → New SSH Key**, paste the output, and save. Repeat for your work account.

### 4. Configure the SSH Config File

Create or open the config file using Notepad:

```powershell
notepad $env:USERPROFILE\.ssh\config

```

Paste the following custom routing structure:

```text
# Personal account
Host personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal

# Work account
Host work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work

```

### 5. Test the Connection

```powershell
ssh -T git@personal
ssh -T git@work

```

```

---

### 3. `TROUBLESHOOTING.md`

```markdown
# Troubleshooting & Cloning Strategies

### 1. How to Clone Repositories Correctly
When using custom routing in your SSH config, you **cannot** use the default copy-paste URL from GitHub. You must modify the host profile name.

* **Default URL:** `git@github.com:username/repo.git`
* **Your Custom Command:**
  ```bash
  # For Personal Account
  git clone git@personal:username/repo.git

  # For Work Account
  git clone git@work:company/repo.git

```

### 2. Setting Local Git Directory Permissions

Ensure git applies the correct author profile details inside your cloned directory so your commits don't mix up your personal and work emails:

```bash
git config local user.name "Your Name"
git config local user.email "your-work-email@company.com"

```

### 3. Error: "Permission Denied (publickey)"

* Double-check that your keys are loaded using `ssh-add -l`.
* Verify that the spelling of `IdentityFile` paths inside your `.ssh/config` perfectly matches your local system paths.

```
