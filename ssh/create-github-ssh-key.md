# Create a Dedicated GitHub SSH Key

## Goal

Create a dedicated SSH key for the GitHub account:

```text
hunterdalmia01
```

The key will later be used for:

- GitHub authentication
- Repository cloning
- Git pull/push
- SSH commit signing

---

## 1. Check Existing SSH Keys

Run:

```bash
ls -la ~/.ssh
```

This lets you see existing identities before creating another one.

Example:

```text
id_ed25519
id_ed25519.pub
id_ed25519_nagarro_new
id_ed25519_nagarro_new.pub
config
known_hosts
```

We will create a completely separate key for:

```text
hunterdalmia01
```

---

## 2. Create a New SSH Key

Run:

```bash
ssh-keygen -t ed25519 \
  -C "YOUR_GITHUB_EMAIL" \
  -f ~/.ssh/id_ed25519_hunterdalmia01
```

Example:

```bash
ssh-keygen -t ed25519 \
  -C "your-email@example.com" \
  -f ~/.ssh/id_ed25519_hunterdalmia01
```

You will be asked:

```text
Enter passphrase (empty for no passphrase):
```

Using a passphrase is recommended.

After creation, you should have:

```text
~/.ssh/id_ed25519_hunterdalmia01
~/.ssh/id_ed25519_hunterdalmia01.pub
```

---

## 3. Understand Private vs Public Key

Private key:

```text
~/.ssh/id_ed25519_hunterdalmia01
```

Never:

- Share it
- Copy it into GitHub
- Commit it
- Send it over Slack/email
- Put it in documentation

Public key:

```text
~/.ssh/id_ed25519_hunterdalmia01.pub
```

This is the key that can safely be added to GitHub.

---

## 4. Set Correct Permissions

Private key:

```bash
chmod 600 ~/.ssh/id_ed25519_hunterdalmia01
```

Public key:

```bash
chmod 644 ~/.ssh/id_ed25519_hunterdalmia01.pub
```

---

## 5. Add the Key to macOS SSH Agent

Run:

```bash
ssh-add --apple-use-keychain \
  ~/.ssh/id_ed25519_hunterdalmia01
```

Check loaded identities:

```bash
ssh-add -l
```

Your new key should appear in the output.

---

## 6. Copy the Public Key

On macOS:

```bash
pbcopy < ~/.ssh/id_ed25519_hunterdalmia01.pub
```

Alternatively:

```bash
cat ~/.ssh/id_ed25519_hunterdalmia01.pub
```

Copy the complete line beginning with:

```text
ssh-ed25519
```

---

## 7. Add the Key to GitHub

Login to:

```text
GitHub account: hunterdalmia01
```

Navigate to:

```text
GitHub
→ Settings
→ SSH and GPG keys
→ New SSH key
```

Use:

```text
Title:
Prateek MacBook - hunterdalmia01

Key type:
Authentication Key
```

Paste:

```text
~/.ssh/id_ed25519_hunterdalmia01.pub
```

and save.

---

## 8. Do Not Execute the SSH Key

This is incorrect:

```bash
~/.ssh/id_ed25519_hunterdalmia01
```

It may produce:

```text
zsh: permission denied
```

because an SSH private key is a file, not a command.

Use it through SSH instead:

```bash
ssh -T \
  -i ~/.ssh/id_ed25519_hunterdalmia01 \
  -o IdentitiesOnly=yes \
  git@github.com
```

---

## 9. Test the Key Directly

Run:

```bash
ssh -T \
  -i ~/.ssh/id_ed25519_hunterdalmia01 \
  -o IdentitiesOnly=yes \
  git@github.com
```

Expected result:

```text
Hi hunterdalmia01! You've successfully authenticated,
but GitHub does not provide shell access.
```

The important part is:

```text
Hi hunterdalmia01!
```

If another GitHub username appears, the wrong key/account is being used.

---

## Quick Verification

Run:

```bash
ls -l ~/.ssh/id_ed25519_hunterdalmia01*
```

Then:

```bash
ssh-add -l
```

Then:

```bash
ssh -T \
  -i ~/.ssh/id_ed25519_hunterdalmia01 \
  -o IdentitiesOnly=yes \
  git@github.com
```

Expected GitHub identity:

```text
hunterdalmia01
```