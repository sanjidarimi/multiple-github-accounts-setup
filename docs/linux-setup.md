# 3. Linux Configuration Guide

This guide covers native Linux distributions (Ubuntu, Debian, Fedora, Arch, CentOS, etc.).

## Step 1: Generate Isolated SSH Keys

Run the following commands in your terminal to create distinct keys for each profile.

### Create Personal Key
```bash
ssh-keygen -t ed25519 -C "your-personal-email@example.com" -f ~/.ssh/id_ed25519_personal
Create Work Key
Bash
ssh-keygen -t ed25519 -C "your-work-email@company.com" -f ~/.ssh/id_ed25519_work
🔍 Behind the Command: What do these parameters mean?
-t ed25519: Specifies the encryption algorithm type. Ed25519 is the most modern, highly secure, and computationally efficient cryptographic algorithm used for public-key operations today, vastly superior to older RSA parameters.

-C "email": An inline comment to track which identity belongs to which hardware key on GitHub.

-f ~/.ssh/filename: Forces the system to save the key under a custom descriptive file name, preventing it from overwriting default credentials.

🔒 Security Warning: When prompted to Enter passphrase, always enter a password. Leaving this blank means anyone who gains physical or digital access to your computer's filesystem can copy your secret keys and compromise your repositories instantly.

Step 2: Configure the Background SSH Agent
The SSH Agent manages your private decrypted key certificates in memory. Start the agent using the system execution environment:

Bash
eval "$(ssh-agent -s)"
Add your newly minted secret keys into the manager runtime:

Bash
ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_work
Step 3: Create and Populate your SSH Config File
Open or create the system SSH configuration file:

Bash
nano ~/.ssh/config
Paste the following blocks directly into the file:

Plaintext
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
🧠 Understanding the Schema Line-by-Line:
Host github.com-personal: This sets your custom routing domain trigger name (your alias). You will use this exact host when checking out or linking code repositories.

HostName github.com: Instructs the computer to resolve the traffic back to the actual real target endpoint server destination.

User git: Explicitly logs into GitHub's shared code container server infrastructure.

IdentityFile: Points directly to the exact target isolated authentication file.

IdentitiesOnly yes: Critical flag. Forces the local system agent to strictly present only the key listed in that explicit declaration file block, preventing cascading authentication fallbacks.

Save and exit the editor (Ctrl+O, Enter, then Ctrl+X).

Fix safety permission parameters so other users cannot tamper with your configuration adjustments:

Bash
chmod 600 ~/.ssh/config
Step 4: Link Public Certificates to GitHub
Print your personal public key certificate output:

Bash
cat ~/.ssh/id_ed25519_personal.pub
Copy the entire string (starts with ssh-ed25519 and ends with your email string comment).

Navigate to GitHub.com -> log in to your Personal Account -> Settings -> SSH and GPG keys -> Click New SSH Key.

Give it a title describing your device (e.g., Linux-Laptop) and paste the public content.

Repeat the exact sequence for your work profile account using:

Bash
cat ~/.ssh/id_ed25519_work.pub
Step 5: Test Connection Configurations
Verify that your system aliases route successfully:

Bash
ssh -T git@github.com-personal
Expected Output: Hi personal_username! You've successfully authenticated, but GitHub does not provide shell access.

Bash
ssh -T git@github.com-work
Expected Output: Hi work_company_username! You've successfully authenticated, but GitHub does not provide shell access.