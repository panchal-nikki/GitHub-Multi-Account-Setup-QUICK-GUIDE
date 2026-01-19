Glad it helped bro 🙌
Here’s a **short, clean, copy-paste–ready cheat sheet** you can save in Notes / Notion / README.
This is **production-grade** and works every time on macOS.

---

# 🧠 GitHub Multi-Account Setup (macOS) – QUICK GUIDE

## 🔑 1. Create SSH keys

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

---

## 🔐 2. Start SSH agent & add keys

```bash
eval "$(ssh-agent -s)"

ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_office

ssh-add -l
```

---

## ⚙️ 3. SSH config (MOST IMPORTANT)

```bash
nano ~/.ssh/config
```

```ssh
# Personal GitHub
Host github-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal

# Office GitHub
Host github-office
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_office
```

```bash
chmod 600 ~/.ssh/config
```

---

## 🔗 4. Add keys to GitHub

```bash
cat ~/.ssh/id_ed25519_personal.pub
cat ~/.ssh/id_ed25519_office.pub
```

GitHub → Settings → **SSH & GPG Keys** → Add

---

## 🧪 5. Test SSH

```bash
ssh -T git@github-personal
ssh -T git@github-office
```

---

## 📥 6. Clone repos (RULE)

```bash
# Personal
git clone git@github-personal:username/repo.git

# Office
git clone git@github-office:org/repo.git
```

❌ Never use `git@github.com:...`

---

## 🧑‍💻 7. Git identity per repo

```bash
# Personal repo
git config user.email "personal-email@gmail.com"

# Office repo
git config user.email "office-email@company.com"
```

---

## 🧩 8. VS Code CLI fix (once)

```text
VS Code → Cmd + Shift + P
Shell Command: Install 'code' command in PATH
```

Restart terminal →

```bash
code .
```

---

## 🧠 Mental model (remember forever)

* **SSH key file name** → identity
* **SSH config Host** → routing
* **Clone URL host** → decides which account is used
