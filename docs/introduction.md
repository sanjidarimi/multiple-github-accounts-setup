# 1. Introduction & Conceptual Architecture

Before writing scripts or generating keys, it is essential to understand **why** default setups fail and **how** SSH handles multiple network profiles.

## The Core Problem

When you interact with GitHub via an SSH URL like `git@github.com:username/repo.git`, your SSH client checks for an active key identity. It defaults to presenting the first key it finds (`~/.ssh/id_rsa` or `~/.ssh/id_ed25519`). 

GitHub reads that key, maps it to a single user profile, and processes the request. If you attempt to access a work repository while presenting your personal key, GitHub will instantly reject it with a permission error because it views you entirely as your personal account.

## The Solution: SSH Host Aliasing

We solve this using customized system routing rules inside a configuration file (`~/.ssh/config`). We create mock domains (aliases) that map down to the identical backend server endpoints but present unique matching credentials.



```mermaid
graph TD
    A[Git Command] --> B{Which Remote URL?}
    B -->|git@github.com-personal:user/repo.git| C[Use id_ed25519_personal]
    B -->|git@github.com-work:company/repo.git| D[Use id_ed25519_work]
    C --> E[GitHub identifies you as Personal Profile]
    D --> F[GitHub identifies you as Work Profile]

```

---

## 🔑 Authentication vs Identity

It is highly common for junior developers to confuse **Authentication** with **Identity**:

> 💡 **Key Takeaway:**
> * **Authentication (Who owns the connection):** Checked by GitHub via your **SSH Keys**. It verifies whether your computer is allowed to push code to a specified repository.
> * **Identity (Who wrote the code):** Checked by Git logs via your **Author Name/Email**. It dictates whose avatar and email show up next to a commit on a repository history timeline.
> 
> 
