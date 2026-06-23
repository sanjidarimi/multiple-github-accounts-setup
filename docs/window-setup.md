# 4. Windows Configuration Guide

This guide covers Windows 10 and Windows 11 environments using either **Git Bash** or **PowerShell**.

> **Recommendation:** Use Git Bash whenever possible. It provides a Linux-like terminal experience and simplifies SSH-related commands.

---

## Step 1: Verify SSH Support

Before generating SSH keys, ensure that Git or OpenSSH is installed.

### Check SSH Installation

Open Git Bash or PowerShell and run:

```bash
ssh -V
```

Expected output:

```text
OpenSSH_for_Windows_x.x
```

If SSH is not installed, install:

* Git for Windows (includes Git Bash and OpenSSH)
* OpenSSH Client via Windows Optional Features

---

## Step 2: Generate Separate SSH Keys

Create dedicated SSH keys for each GitHub account.

### Personal Account

```bash
ssh-keygen -t ed25519 -C "personal-email@domain.com" -f ~/.ssh/id_ed25519_personal
```

### Work Account

```bash
ssh-keygen -t ed25519 -C "company-email@work.com" -f ~/.ssh/id_ed25519_work
```

### Understanding the Parameters

#### `-t ed25519`

Specifies the cryptographic algorithm.

Ed25519 provides:

* Strong security
* Fast authentication
* Smaller key sizes
* Modern cryptographic standards

#### `-C`

Adds a descriptive comment to the public key.

Example:

```bash
-C "personal-email@domain.com"
```

This makes it easier to identify keys inside GitHub.

#### `-f`

Defines the output filename.

Example:

```bash
-f ~/.ssh/id_ed25519_personal
```

This prevents existing SSH keys from being overwritten.

### Security Recommendation

When prompted:

```text
Enter passphrase (empty for no passphrase):
```

Use a strong passphrase to protect your private keys.

---

## Step 3: Start the SSH Agent

The SSH Agent stores decrypted keys in memory so you don't have to enter your passphrase repeatedly.

### Option A: PowerShell (Recommended)

Run PowerShell as Administrator:

```powershell
Set-Service -Name ssh-agent -StartupType Automatic

Start-Service ssh-agent
```

Verify the service:

```powershell
Get-Service ssh-agent
```

Expected status:

```text
Running
```

---

### Option B: Git Bash

Start an SSH Agent session:

```bash
eval "$(ssh-agent -s)"
```

Add your SSH keys:

```bash
ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_work
```

Verify loaded keys:

```bash
ssh-add -l
```

---

## Step 4: Configure SSH Routing

Create or edit your SSH configuration file:

```bash
notepad ~/.ssh/config
```

Add the following configuration:

```ssh
# Personal GitHub Account
Host github.com-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal
  IdentitiesOnly yes

# Work GitHub Account
Host github.com-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work
  IdentitiesOnly yes
```

### Configuration Breakdown

#### `Host`

Creates a custom SSH alias.

Example:

```ssh
Host github.com-personal
```

This alias will later be used when cloning repositories.

#### `HostName`

Defines the real destination server.

```ssh
HostName github.com
```

#### `User`

GitHub SSH authentication always uses:

```ssh
User git
```

#### `IdentityFile`

Specifies which private key should be used.

Example:

```ssh
IdentityFile ~/.ssh/id_ed25519_personal
```

#### `IdentitiesOnly yes`

Forces SSH to use only the specified key and prevents accidental authentication with another loaded key.

---

### Important Windows Note

Ensure the file is saved as:

```text
config
```

Not:

```text
config.txt
```

Windows may automatically append file extensions when using Notepad.

---

## Step 5: Add Public Keys to GitHub

Display the personal public key:

```bash
cat ~/.ssh/id_ed25519_personal.pub
```

Display the work public key:

```bash
cat ~/.ssh/id_ed25519_work.pub
```

Copy each key and add it to the corresponding GitHub account.

### Add SSH Keys in GitHub

1. Sign in to GitHub
2. Open **Settings**
3. Select **SSH and GPG Keys**
4. Click **New SSH Key**
5. Enter a descriptive title
6. Paste the public key
7. Save

Repeat the process for both accounts.

---

## Step 6: Test Authentication

Verify the personal account:

```bash
ssh -T git@github.com-personal
```

Expected output:

```text
Hi personal_username! You've successfully authenticated, but GitHub does not provide shell access.
```

Verify the work account:

```bash
ssh -T git@github.com-work
```

Expected output:

```text
Hi work_username! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## Step 7: Clone Repositories Using Account Aliases

### Personal Repository

```bash
git clone git@github.com-personal:your-username/project-name.git
```

### Work Repository

```bash
git clone git@github.com-work:company-name/project-name.git
```

Using account-specific aliases ensures the correct SSH key is automatically selected for each repository.

---

## Troubleshooting

### Permission Denied (Public Key)

Check loaded keys:

```bash
ssh-add -l
```

Re-add the key if necessary:

```bash
ssh-add ~/.ssh/id_ed25519_personal
```

---

### Wrong GitHub Account Appears

Run:

```bash
ssh -T git@github.com-personal
```

Verify that the returned username matches the expected account.

---

### SSH Config Not Working

Validate the configuration:

```bash
ssh -vT git@github.com-personal
```

The verbose output will show which configuration block and key are being used.

---

### Config File Saved as `config.txt`

Open:

```bash
ls ~/.ssh
```

If you see:

```text
config.txt
```

Rename it to:

```text
config
```

SSH will ignore files named `config.txt`.
