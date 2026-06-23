# Managing Multiple GitHub Accounts Setup on One Computer Using SSH

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](docs/CONTRIBUTING.md)

A comprehensive, beginner-friendly guide designed for developers, freelancers, and open-source contributors who need to seamlessly manage multiple GitHub accounts (e.g., Personal, Work, Client) on a single machine without authentication conflicts.

---

## 📌 Why Do You Need This?

By default, Git configurations apply globally across your entire operating system. If you attempt to use multiple GitHub accounts on one computer, you will inevitably run into authorization conflicts, accidentally commit work code using your personal email, or get hit with the dreaded `Permission denied (publickey)` error.

This repository provides a robust, standardized architecture using **SSH Host Aliases** to elegantly segment your identities, ensuring you always commit to the right project with the correct profile.

### Core Features
* **Complete Isolation:** Separate your professional identity from your personal open-source contributions.
* **Zero Conflict:** No more messing with global configuration overrides before every single push.
* **Universal Compatibility:** Step-by-step native instructions for macOS, Windows (Git Bash/PowerShell), and Linux.
* **Advanced Troubleshooting:** A comprehensive index of errors, causes, and instant fixes.

---

## 💻 Supported Operating Systems

| OS | Sub-Systems Supported | Recommended Shell |
| :--- | :--- | :--- |
| **macOS** | Apple Silicon & Intel | Zsh / Terminal |
| **Windows** | Windows 10 / 11 | Git Bash / PowerShell |
| **Linux** | Ubuntu, Debian, Fedora, Arch, etc. | Bash / Zsh |

---

## 🗂️ Documentation Navigation

Explore our step-by-step chapters to set up your environment:

1.  [**Introduction & Architecture**](docs/introduction.md) — How SSH and Git isolation work together.
2.  [**Prerequisites**](docs/prerequisites.md) — System requirements and pre-flight checks.
3.  [**OS Setup Guides**](docs/introduction.md):
    * [Linux Setup Guide](docs/linux-setup.md)
    * [Windows Setup Guide](docs/windows-setup.md)
    * [macOS Setup Guide](docs/macos-setup.md)
4.  [**Git Identity Configuration**](docs/git-identity.md) — Managing names and emails perfectly.
5.  [**Cloning & Working with Repositories**](docs/cloning-repositories.md) — The magic of custom SSH URLs.
6.  [**Troubleshooting & Resolution Index**](docs/troubleshooting.md) — Fix any issue in under 2 minutes.
7.  [**Frequently Asked Questions (FAQ)**](docs/faq.md) — 20+ answers to your edge-case questions.
8.  [**Production Best Practices**](docs/best-practices.md) — Security audits, key rotation, and backup strategies.

---

## 🚀 Quick Start (TL;DR)

If you are an advanced user who just needs the commands, here is the shorthand sequence:

```bash
# 1. Generate keys
ssh-keygen -t ed25519 -C "personal@email.com" -f ~/.ssh/id_ed25519_personal
ssh-keygen -t ed25519 -C "work@company.com" -f ~/.ssh/id_ed25519_work

# 2. Add keys to your local SSH Agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_work

# 3. Configure ~/.ssh/config
# Open or create the file, then paste:
Host github.com-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal

Host github.com-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work

# 4. Clone your repository using the alias
git clone git@github.com-personal:username/repo.git