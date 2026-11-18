# fork-sync

Keeps selected forks in this organization level with their upstream default branch.

[`sync-forks.yml`](.github/workflows/sync-forks.yml) runs every 5 minutes. For each fork in its matrix it compares the
fork's branch tip with upstream's and, when they differ, pushes upstream's commits to the fork as a fast-forward. Commit
SHAs on the fork match upstream exactly.

## Behavior

- The push is never forced. If a fork has commits upstream lacks, its job fails and the fork is left untouched.
- GitHub schedules cron runs on a best-effort basis, so runs can start several minutes late.
- GitHub disables scheduled workflows in a public repository after 60 days without repository activity. Re-enable the
  workflow from the Actions tab if that happens.

## Adding a fork

1. Generate a key pair: `ssh-keygen -t ed25519 -N "" -C fork-sync -f key`.
2. Add `key.pub` to the fork as a deploy key with write access.
3. Add the private key to this repository as an Actions secret.
4. Add a matrix entry naming the fork, its upstream, the branch, and the secret.

Each deploy key grants write access to one fork only. The built-in `GITHUB_TOKEN` cannot be used because it may not push
commits that change files under `.github/workflows`.
