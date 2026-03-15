# Upstream Sync Policy

This repository tracks:

- `upstream` → `https://github.com/FossifyOrg/Messages.git`
- `origin` → `https://github.com/Watson-Dallin/Kompakt-Messages.git`

## Branch Strategy

- `main`: mirror of upstream `main` (clean integration branch)
- `kompakt-ui-base`: long-lived customization integration branch
- `kompakt-ui-phase-*`: short-lived feature branches

## Sync Cadence

- Target: sync from upstream weekly (or immediately for security-critical fixes).

## Standard Sync Flow

```bash
# 1) update remote refs
git fetch --all --prune

# 2) refresh local main from upstream
git checkout main
git reset --hard upstream/main

# 3) push refreshed main to origin
git push origin main --force-with-lease

# 4) rebase customization base on refreshed main
git checkout kompakt-ui-base
git rebase main

# 5) push base branch updates
git push origin kompakt-ui-base --force-with-lease
```

## Feature Branch Flow

```bash
git checkout kompakt-ui-base
git pull --rebase origin kompakt-ui-base
git checkout -b kompakt-ui-phase-<name>

# work / commit

git push -u origin kompakt-ui-phase-<name>
```

## Conflict Rules

1. Preserve Fossify domain logic unless a Kompakt requirement explicitly needs a change.
2. Keep Kompakt-specific behavior isolated to presentation/theme/navigation layers.
3. Avoid broad refactors during sync windows.
4. Resolve conflicts in smallest possible diffs and verify messaging flows after merge.

## Post-Sync Verification Checklist

- Build completes
- App launches
- Thread list opens
- Read/reply/send SMS works
- Notifications still behave correctly

## Emergency Rollback

If a sync introduces regression:

```bash
git checkout kompakt-ui-base
git reflog
# identify pre-sync SHA
git reset --hard <good_sha>
git push origin kompakt-ui-base --force-with-lease
```
