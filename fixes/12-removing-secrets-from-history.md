# [VULN-01 follow-up] Removing Secrets from Git History

**Caught by:** Gitleaks (secrets scan)
**The trap:** deleting the secret and committing does **not** fix it.

## Why the pipeline still fails after you "remove" the secret

Git history is **append-only**. Deleting a line and committing creates a *new*
commit — the old commit still contains the blob with the secret, and it's
still reachable in history.

Our pipeline checks out full history on purpose:

```yaml
- uses: actions/checkout@v4
  with:
    fetch-depth: 0   # full history, not just the latest commit
```

So Gitleaks scans **every commit**, finds the secret in the old one, and
keeps failing. That's correct behaviour — the secret really is still in the
repo, and anyone who clones it gets it:

```bash
git log -p -S 'supersecret123'   # it's right there in history
```

## The fix, in order

### Step 1 — ROTATE THE SECRET (this is the real fix)

Do this **first**, before touching history. Assume the secret is already
compromised: bots scan public GitHub pushes within seconds.

| Secret type | Action |
|---|---|
| AWS access key | Deactivate + delete in IAM, issue a new one |
| JWT signing secret | Generate a new one and redeploy (invalidates old tokens — that's the point) |
| DB password / API token | Rotate at the provider |

> Rewriting history is **cleanup**. Rotation is the **fix**. If you only
> rewrite history, you've hidden the evidence but the key still works.

### Step 2 — Purge it from history

Use `git filter-repo` (GitHub's recommended tool; `git filter-branch` is
deprecated and very slow).

```bash
pip install git-filter-repo
```

**Redact a specific string everywhere it appears:**

```bash
echo 'supersecret123==>REDACTED' > replacements.txt
git filter-repo --replace-text replacements.txt
```

**Or remove an entire file from all history:**

```bash
git filter-repo --invert-paths --path app/.env
```

`filter-repo` deliberately drops your remote as a safety measure, so re-add
it and force-push:

```bash
git remote add origin https://github.com/<you>/<repo>.git
git push --force --all
git push --force --tags
```

**BFG Repo-Cleaner** is a friendlier alternative for the common cases:

```bash
bfg --replace-text passwords.txt     # or: bfg --delete-files .env
git reflog expire --expire=now --all && git gc --prune=now --aggressive
git push --force
```

### Step 3 — Know what force-pushing does NOT clean

This is where people get a false sense of safety:

- **Everyone must re-clone.** Every commit SHA after the rewrite point
  changes. Teammates who `git pull` will create a merge mess.
- **Open PRs break** and may need to be reopened.
- **Forks keep the old objects.** A fork is a separate repo — your rewrite
  doesn't touch it. Each fork must be deleted or cleaned independently.
- **GitHub may still serve old commits by direct SHA** from cached views.
  To fully purge those, contact GitHub Support and ask them to
  garbage-collect the stale references.

All of which loops back to: **rotation is the only part fully under your
control.**

## Step 4 — Prevent it: shift left to the pre-commit hook

CI scanning is the safety net. The actual prevention is blocking the secret
before it's ever committed.

### Option A: plain git hook (zero dependencies)

Copy `fixes/pre-commit-hook/pre-commit` into your repo:

```bash
cp fixes/pre-commit-hook/pre-commit .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

It runs `gitleaks protect --staged` and aborts the commit if a secret is
found in the staged changes.

### Option B: the `pre-commit` framework (shareable across the team)

`fixes/pre-commit-hook/.pre-commit-config.yaml` is ready to drop in the repo
root:

```bash
pip install pre-commit
cp fixes/pre-commit-hook/.pre-commit-config.yaml .
pre-commit install
```

Now every `git commit` runs Gitleaks first. Because the config lives in the
repo, teammates get the same protection with one `pre-commit install`.

> `.git/hooks/` is **not** committed to the repo, so a plain hook protects
> only your machine. The `pre-commit` framework solves that.

### Option C: let GitHub block the push

Enable **Secret scanning → Push protection** in
Settings → Code security. GitHub then rejects pushes containing recognised
secret patterns — a server-side backstop that works even if someone skips
the hook with `--no-verify`.

## Handling intentional / test secrets

Don't weaken the rules globally. Add the finding's fingerprint to
`.gitleaksignore`:

```
# .gitleaksignore
app/server.js:aws-access-token:31
```

Get the fingerprint from the Gitleaks report for that finding.

---

## Note for this repo

**PageTurn's secrets are deliberate and fake.** `AKIAIOSFODNN7EXAMPLE` is
AWS's own documented example key, and `supersecret123` protects nothing.
They should **stay** in history so the Gitleaks stage keeps demonstrating a
real finding. Everything above is what you'd do on a real repo.
