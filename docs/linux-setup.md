# 3. Linux Configuration Guide

This guide covers native Linux distributions including Ubuntu, Debian, Fedora, Arch Linux, CentOS, and other Linux-based operating systems.

---

## Step 1: Generate Isolated SSH Keys

Create separate SSH keys for each GitHub account.

### Create Personal Key

```bash
ssh-keygen -t ed25519 -C "your-personal-email@example.com" -f ~/.ssh/id_ed25519_personal
```

### Create Work Key

```bash
ssh-keygen -t ed25519 -C "your-work-email@company.com" -f ~/.ssh/id_ed25519_work
```

### 🔍 Behind the Command: What Do These Parameters Mean?

#### `-t ed25519`

Specifies the encryption algorithm type.

**Ed25519** is a modern public-key cryptography algorithm that offers:

* Strong security
* Faster operations
* Smaller key sizes
* Better performance than traditional RSA keys

#### `-C "email"`

Adds a comment to the generated key.

This helps identify which account a key belongs to when viewing SSH keys inside GitHub.

#### `-f ~/.ssh/filename`

Defines a custom filename for the generated SSH key.

This prevents new keys from overwriting existing credentials.

### 🔒 Security Warning

When prompted:

```bash
Enter passphrase (empty for no passphrase):
```

Always use a strong passphrase.

Without a passphrase, anyone who gains access to your computer can potentially use your private key and access your repositories.

---

## Step 2: Configure the SSH Agent

The SSH Agent securely stores decrypted private keys in memory so you don't need to enter your passphrase repeatedly.

### Start the SSH Agent

```bash
eval "$(ssh-agent -s)"
```

### Add Your Keys

```bash
ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_work
```

### Verify Loaded Keys

```bash
ssh-add -l
```

---

## Step 3: Create and Configure the SSH Config File

Open (or create) your SSH configuration file:

```bash
nano ~/.ssh/config
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

### 🧠 Understanding Each Configuration Option

#### `Host github.com-personal`

Defines a custom SSH alias.

This alias is what you'll use when cloning repositories.

#### `HostName github.com`

Specifies the actual destination server.

Even though you connect using an alias, SSH routes the request to GitHub.

#### `User git`

GitHub SSH authentication always uses the `git` user.

#### `IdentityFile`

Points to the private key associated with that account.

Example:

```ssh
IdentityFile ~/.ssh/id_ed25519_personal
```

#### `IdentitiesOnly yes`

Forces SSH to use only the specified key.

This prevents SSH from attempting multiple keys and accidentally authenticating with the wrong account.

### Save and Exit

In Nano:

```text
Ctrl + O
Enter
Ctrl + X
```

### Secure the Configuration File

Restrict access permissions:

```bash
chmod 600 ~/.ssh/config
```

---

## Step 4: Add Public Keys to GitHub

Display your personal public key:

```bash
cat ~/.ssh/id_ed25519_personal.pub
```

Copy the entire output.

Example:

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... your-personal-email@example.com
```

### Add the Key to GitHub

1. Sign in to GitHub
2. Navigate to **Settings**
3. Open **SSH and GPG Keys**
4. Click **New SSH Key**
5. Enter a descriptive title
6. Paste the public key
7. Save

### Repeat for Work Account

Display the work account public key:

```bash
cat ~/.ssh/id_ed25519_work.pub
```

Add it to the corresponding GitHub account using the same process.

---

## Step 5: Test Your Configuration

Verify that the personal account is working correctly:

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
Hi work_company_username! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## Next Step: Clone Repositories Using Account Aliases

### Personal Account

```bash
git clone git@github.com-personal:your-username/repository-name.git
```

### Work Account

```bash
git clone git@github.com-work:company-name/repository-name.git
```

Using aliases ensures each repository automatically authenticates with the correct GitHub account.
