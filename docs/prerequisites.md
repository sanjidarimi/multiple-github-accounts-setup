# 2. Pre-flight Prerequisites

Before following your specific operating system guide, verify that your local computer has the mandatory dependencies ready.

## 1. Verify Git Installation
Open your terminal application and execute:
```bash
git --version

*Expected Output:* `git version 2.x.x` or higher. If Git is missing, download it from [git-scm.com](https://git-scm.com/).

# 2. Check for Existing Keys

Inspect your current SSH configuration directory to verify you are not accidentally overwriting critical system files:

```bash
ls -al ~/.ssh

```

If you see keys named `id_rsa` or `id_ed25519`, be careful not to delete them. The naming convention in this guide explicitly prevents collisions.

## 3. Clear Cache/Active Sessions (Optional)

If you've been fighting configuration errors before finding this guide, clear out any active mismatched system variables:

```bash
ssh-add -D

This command safely flushes active keys out of temporary cache memory (it does not delete your physical key files).