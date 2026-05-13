# 🧠 GitHub Multi-Account Setup on One Machine

Personal + Office + More Accounts on **Windows / macOS / Linux**

---

## ✅ What this guide solves

Use this guide if you want to:

- use **multiple GitHub accounts on the same machine**
- keep **personal work** and **office work** separate
- make sure the **correct SSH key** is used for each repo
- make sure commits are linked to the **correct GitHub profile**

This guide uses **SSH only**.  
If you use HTTPS remotes, account switching behaves differently.

---

## ✅ Prerequisites

Make sure Git and OpenSSH are installed.

```bash
git --version
ssh -V
```

If both commands print a version, you are ready.

---

## 🔑 1. Create SSH keys

Create one SSH key per GitHub account.

### Personal

```bash
ssh-keygen -t ed25519 -C "personal-email@gmail.com"
# save as:
~/.ssh/id_ed25519_personal
```

### Office

```bash
ssh-keygen -t ed25519 -C "office-email@company.com"
# save as:
~/.ssh/id_ed25519_office
```

> You can repeat the same pattern for a third or fourth account:
> `id_ed25519_freelance`, `id_ed25519_contract`, etc.

---

## 🔐 2. Start SSH agent and add keys

### macOS / Linux

```bash
eval "$(ssh-agent -s)"

ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_office

ssh-add -l
```

### Windows PowerShell

Make sure the OpenSSH Authentication Agent is enabled and running before adding keys:

```powershell
Get-Service ssh-agent
Set-Service -Name ssh-agent -StartupType Automatic
Start-Service ssh-agent
ssh-add $HOME/.ssh/id_ed25519_personal
ssh-add $HOME/.ssh/id_ed25519_office
ssh-add -l
```

If `ssh-add` says the agent is not running, re-check that the `ssh-agent` service started successfully.

---

## ⚙️ 3. SSH config (most important part)

Open your SSH config:

```bash
nano ~/.ssh/config
```

Add:

```ssh
# Personal GitHub
Host github-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal
  IdentitiesOnly yes

# Office GitHub
Host github-office
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_office
  IdentitiesOnly yes
```

Save and secure it:

```bash
chmod 600 ~/.ssh/config
```

Why `IdentitiesOnly yes` matters:

- it prevents SSH from randomly trying the wrong key first
- it makes multi-account setup much more reliable

---

## 🔗 4. Add SSH public keys to GitHub

Copy the public keys:

```bash
cat ~/.ssh/id_ed25519_personal.pub
cat ~/.ssh/id_ed25519_office.pub
```

Then add each public key to the correct GitHub account:

**GitHub → Settings → SSH and GPG keys → New SSH key**

---

## 🧪 5. Test SSH login

```bash
ssh -T git@github-personal
ssh -T git@github-office
```

Expected result:

```text
Hi username! You've successfully authenticated...
```

If the username is wrong, your SSH config or key mapping is wrong.

---

## 📥 6. Clone repos the right way

Always clone with the SSH host alias from your config.

```bash
# Personal
git clone git@github-personal:username/repo.git

# Office
git clone git@github-office:org/repo.git
```

❌ Avoid this for multi-account setup:

```bash
git clone git@github.com:username/repo.git
```

Because `github.com` does not tell SSH which account-specific key to use.

---

## 🚀 7. One-time global Git safety setup

```bash
git config --global user.name "Your Name"
git config --global user.useConfigOnly true
```

Why this helps:

- Git will not silently guess an email for commits
- you are forced to set the correct repo identity intentionally

You can keep `user.name` global if the same name is correct for all accounts.  
The examples in sections 8 and 9 still include `git config user.name` for completeness, but you can omit that line if your global name is already correct.

---

## 🧑‍💻 8. Personal repo - first commit and push

```bash
git init
git config user.name "Your Name"
git config user.email "personal-email@gmail.com"

git add .
git commit -m "initial commit"

git branch -M main
git remote add origin git@github-personal:your-username/repo-name.git
git push -u origin main
```

✅ Commits will link to your **personal GitHub profile** if that email is verified in that account.

---

## 🏢 9. Office repo - first commit and push

```bash
git init
git config user.name "Your Name"
git config user.email "office-email@company.com"

git add .
git commit -m "initial commit"

git branch -M main
git remote add origin git@github-office:org-or-company/repo-name.git
git push -u origin main
```

✅ Commits will link to your **office GitHub profile** if that email is verified in that account.

---

## 🔍 10. Verify which account a repo is using

Inside any repo, run:

```bash
git remote -v
git config user.name
git config user.email
ssh -T git@github-personal
ssh -T git@github-office
```

Quick mental check:

- `git remote -v` tells you which SSH host alias the repo uses
- `git config user.email` tells you which identity will be used for commits

If the remote says `github-personal`, use personal email.  
If the remote says `github-office`, use office email.

---

## 🔁 11. Fix an existing repo that uses the wrong remote

If you already cloned a repo using `github.com`, update it.

### Personal repo

```bash
git remote set-url origin git@github-personal:your-username/repo-name.git
```

### Office repo

```bash
git remote set-url origin git@github-office:org-or-company/repo-name.git
```

Verify:

```bash
git remote -v
```

---

## ✍️ 12. Fix wrong author on commits

If the last commit used the wrong identity:

```bash
git commit --amend --reset-author --no-edit
```

If the email itself is wrong, set it first:

```bash
git config user.name "Your Name"
git config user.email "correct-email@example.com"
git commit --amend --reset-author --no-edit
```

> If you already pushed the wrong commit, fixing history may affect others. Be careful on shared branches.

---

## 🧩 13. VS Code CLI fix (once)

```text
VS Code → Cmd/Ctrl + Shift + P
Shell Command: Install 'code' command in PATH
```

Restart terminal:

```bash
code .
```

---

## 🧠 Mental model

- **SSH key filename** → identity
- **SSH config Host** → routing
- **Clone URL host** → account used
- **Repo-local user.email** → commit ownership

If any one of these is wrong, the wrong account may be used.

---

## 🆘 Troubleshooting

### `Permission denied (publickey)`

Check:

```bash
ssh-add -l
cat ~/.ssh/config
```

Make sure:

- the correct key was added to `ssh-agent`
- the correct public key was added to the correct GitHub account
- the repo remote uses `github-personal` or `github-office`, not `github.com`

### SSH login works, but commits show wrong profile

Check:

```bash
git config user.email
```

Also confirm that the same email is **verified on that GitHub account**.

### Wrong account is used during push

Check:

```bash
git remote -v
```

If it uses `git@github.com:...`, change it with `git remote set-url`.

### Keys disappear after restart

You may need to start `ssh-agent` again and re-add keys.

---

## ✅ Golden rules

- Personal repo → `github-personal` + personal email
- Office repo → `github-office` + office email
- Always check `git remote -v` before first push
- Always check `git config user.email` before first commit
- Avoid cloning with `github.com` when using multiple accounts

---

## 🚀 Optional advanced setup for experienced developers

If you keep personal and office repos in separate folders, you can also use Git's `includeIf` feature to auto-apply different Git config files by directory.

That is optional.  
For most users, the SSH alias + repo-local email method in this guide is the simplest and safest setup.
