# Managing Multiple GitHub Accounts on One Computer

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](docs/CONTRIBUTING.md)

A simple guide for developers who need to use multiple GitHub accounts (Personal, Work, Client, Open Source) on the same computer without authentication or identity conflicts.

---

## Why This Guide?

If you use more than one GitHub account, you've probably faced one of these problems:

* Pushed code using the wrong GitHub account
* Committed with the wrong email address
* Received `Permission denied (publickey)` errors
* Constantly switched between accounts
* Reconfigured Git settings for every project

The good news is that you don't need to log in and out of GitHub.

By using SSH keys and Git configuration correctly, you can keep each account completely separate and let Git automatically use the correct identity.

---

## What You'll Learn

✅ Set up multiple GitHub accounts on one machine

✅ Create separate SSH keys for each account

✅ Configure SSH aliases

✅ Manage Git commit identities

✅ Use different emails for work and personal projects

✅ Fix common SSH and Git issues

✅ Work with repositories without account conflicts

---

## Supported Platforms

| Operating System          | Status      |
| ------------------------- | ----------- |
| Windows 10 / 11           | ✅ Supported |
| Ubuntu                    | ✅ Supported |
| Debian                    | ✅ Supported |
| Fedora                    | ✅ Supported |
| Arch Linux                | ✅ Supported |
| Other Linux Distributions | ✅ Supported |

---

## Documentation

### Getting Started

* Introduction & Architecture
* Prerequisites

### Setup Guides

* Linux Setup
* Windows Setup

### Git Configuration

* Git Identity Management
* Managing Multiple Emails

### Troubleshooting

* Permission Denied (Public Key)
* Wrong GitHub Account
* SSH Agent Issues
* File Permission Errors
* Git Identity Problems

---

## Quick Start

If you're already familiar with Git and SSH, here's the short version.

### 1. Generate SSH Keys

```bash id="pg4n2t"
ssh-keygen -t ed25519 -C "personal@email.com" -f ~/.ssh/id_ed25519_personal

ssh-keygen -t ed25519 -C "work@company.com" -f ~/.ssh/id_ed25519_work
```

### 2. Start SSH Agent

```bash id="eym5vx"
eval "$(ssh-agent -s)"
```

### 3. Add Keys

```bash id="m6v8n0"
ssh-add ~/.ssh/id_ed25519_personal

ssh-add ~/.ssh/id_ed25519_work
```

### 4. Configure SSH

Create or edit:

```bash id="rj8jdh"
~/.ssh/config
```

Add:

```ssh id="u6nnmg"
Host github.com-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal
  IdentitiesOnly yes

Host github.com-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work
  IdentitiesOnly yes
```

### 5. Test Connections

```bash id="f7z92s"
ssh -T git@github.com-personal

ssh -T git@github.com-work
```

### 6. Clone Repositories

Personal account:

```bash id="6zgjh9"
git clone git@github.com-personal:username/repository.git
```

Work account:

```bash id="hyz9uo"
git clone git@github.com-work:company/repository.git
```

That's it.

Your personal repositories will use your personal SSH key, and your work repositories will use your work SSH key.

---

## Repository Structure

```text id="p8k2s1"
docs/
├── introduction.md
├── prerequisites.md
├── linux-setup.md
├── windows-setup.md
├── git-identity.md
└── troubleshooting.md
```

---

## Contributing

Found an issue or want to improve the documentation?

Contributions are welcome. Feel free to open an issue or submit a pull request.

---

## License

This project is licensed under the MIT License.
