# Multiple GitHub Accounts with SSH

## Goal

Use multiple GitHub accounts on the same Mac without one account's SSH key being used for another account.

Example repository:

```text
git@github.com:hunterdalmia01/developer-handbook.git
```

Dedicated SSH key:

```text
~/.ssh/id_ed25519_hunterdalmia01
```

We will create the SSH alias:

```text
github-hunterdalmia
```

---

## 1. Verify the SSH Key Exists

```bash
ls -l ~/.ssh/id_ed25519_hunterdalmia01*
```

Expected:

```text
id_ed25519_hunterdalmia01
id_ed25519_hunterdalmia01.pub
```

---

## 2. Open SSH Configuration

```bash
nano ~/.ssh/config
```

You may already have entries for other GitHub accounts.

For example:

```sshconfig
Host github-nagarro
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_nagarro_new
    IdentitiesOnly yes
```

Do not remove them.

Add a separate configuration for `hunterdalmia01`.

---

## 3. Add the GitHub Account Alias

Add:

```sshconfig
Host github-hunterdalmia
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_hunterdalmia01
    IdentitiesOnly yes
    AddKeysToAgent yes
    UseKeychain yes
```

The resulting setup may look like:

```sshconfig
Host github-nagarro
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_nagarro_new
    IdentitiesOnly yes
    AddKeysToAgent yes
    UseKeychain yes

Host github-hunterdalmia
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_hunterdalmia01
    IdentitiesOnly yes
    AddKeysToAgent yes
    UseKeychain yes
```

---

## 4. Protect the SSH Config File

```bash
chmod 600 ~/.ssh/config
```

---

## 5. Test the Alias

Run:

```bash
ssh -T git@github-hunterdalmia
```

Expected:

```text
Hi hunterdalmia01! You've successfully authenticated,
but GitHub does not provide shell access.
```

If another username appears, stop and inspect:

```bash
cat ~/.ssh/config
```

---

## 6. Understand the Alias

Instead of using:

```text
git@github.com:hunterdalmia01/developer-handbook.git
```

we use:

```text
git@github-hunterdalmia:hunterdalmia01/developer-handbook.git
```

The mapping becomes:

```text
github-hunterdalmia
        ↓
github.com
        ↓
~/.ssh/id_ed25519_hunterdalmia01
        ↓
GitHub account hunterdalmia01
```

---

## 7. Clone the Repository

Clone using the alias:

```bash
git clone \
git@github-hunterdalmia:hunterdalmia01/developer-handbook.git
```

Enter the repository:

```bash
cd developer-handbook
```

---

## 8. Verify the Remote

```bash
git remote -v
```

Expected:

```text
origin  git@github-hunterdalmia:hunterdalmia01/developer-handbook.git (fetch)
origin  git@github-hunterdalmia:hunterdalmia01/developer-handbook.git (push)
```

---

## 9. Fix an Already-Cloned Repository

If the repository was cloned using:

```text
git@github.com:hunterdalmia01/developer-handbook.git
```

change only its remote:

```bash
git remote set-url origin \
git@github-hunterdalmia:hunterdalmia01/developer-handbook.git
```

Verify:

```bash
git remote -v
```

---

## 10. Configure Git Identity Only for This Repository

Set:

```bash
git config --local user.name "Prateek Dalmia"
```

Set the email associated with the `hunterdalmia01` GitHub account:

```bash
git config --local user.email "YOUR_GITHUB_EMAIL"
```

Do not use:

```bash
git config --global user.email ...
```

when different repositories belong to different accounts.

---

## 11. Verify Repository-Specific Identity

```bash
git config --local user.name
git config --local user.email
```

Then:

```bash
git config --show-origin --get user.name
git config --show-origin --get user.email
```

Expected source:

```text
file:.git/config
```

This confirms that this identity applies only to:

```text
developer-handbook
```

---

## 12. Test Repository Authentication

Run:

```bash
ssh -T git@github-hunterdalmia
```

Then:

```bash
git fetch origin
```

If both succeed, repository authentication is correctly configured.

---

## Multiple Account Example

The machine can now safely contain:

```text
Nagarro repositories
        ↓
github-nagarro
        ↓
id_ed25519_nagarro_new


Personal repositories
        ↓
github-hunterdalmia
        ↓
id_ed25519_hunterdalmia01
```

A Nagarro repository could use:

```text
git@github-nagarro:cashmere-showcase/translations-app.git
```

while your personal handbook uses:

```text
git@github-hunterdalmia:hunterdalmia01/developer-handbook.git
```

The two identities remain independent.

---

## Useful Commands

Check current remote:

```bash
git remote -v
```

Check current Git identity:

```bash
git config --local user.name
git config --local user.email
```

Check current GitHub account:

```bash
ssh -T git@github-hunterdalmia
```

Check loaded SSH keys:

```bash
ssh-add -l
```

Debug SSH selection:

```bash
ssh -vT git@github-hunterdalmia
```

The verbose output will show which identity file SSH attempted to use.