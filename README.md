# 🧠 GitHub Multi-Account Setup (macOS) – COMPLETE CHEAT SHEET

---

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

## 🔗 4. Add SSH keys to GitHub

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

# 🚀 INITIAL COMMIT & PUSH (VERY IMPORTANT)

## 🔹 One-time global config

```bash
git config --global user.name "Nikki Panchal"
git config --global user.useConfigOnly true
```

---

## 🧑‍💻 PERSONAL REPO – First Push

```bash
git init
git config user.email "personal-email@gmail.com"

git add .
git commit -m "initial commit"

git branch -M main
git remote add origin git@github-personal:your-username/repo-name.git
git push -u origin main
```

✅ Commits will link to **personal GitHub profile**

---

## 🏢 OFFICE REPO – First Push

```bash
git init
git config user.email "office-email@company.com"

git add .
git commit -m "initial commit"

git branch -M main
git remote add origin git@github-office:org-or-company/repo-name.git
git push -u origin main
```

✅ Commits will link to **office GitHub profile**

---

## 🔁 Fix wrong author (if you already committed)

```bash
git commit --amend --reset-author --no-edit
```

---

## 🧩 VS Code CLI fix (once)

```text
VS Code → Cmd + Shift + P
Shell Command: Install 'code' command in PATH
```

Restart terminal:

```bash
code .
```

---

## 🧠 Mental model (remember forever)

* **SSH key filename** → identity
* **SSH config Host** → routing
* **Clone URL host** → account used
* **Repo email** → commit ownership

---

## ✅ Golden Rules

* Personal repo → `github-personal` + personal email
* Office repo → `github-office` + office email
* Never commit without setting email
* Never clone using `github.com`
