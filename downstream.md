# Managing downstream fork

The purpose of this document is to describe the manual steps required to synchronize the downstream fork with upstream, while keeping the downstream only commits on the top of the branch.

## Requirements

1. Ability to manage downstream only changes
2. Downstream commits always on top of the upstream commits
3. Run CI on all changes
4. The process works for everyone on the team (no specific state required)

## Known issues

GitHub does not support "real" rebase where we can keep the downstream changes on the top of the downstream branch. Instead when syncing our fork with upstream, we'll be doing rebase locally and *force push* to the `downstream` branch of our fork. This will make it harder for the downstream maintainers to manage locally the `downstream` branch. In most cases `git pull --rebase` will do job with a warning about skipping to reapply the existing downstream commits (`warning: skipped previously applied commit ...`). Removing the local `downstream` branch and creating a new one from our fork can be used as a last resort.

## Initial configuration

In the most traditional case, the developer forked the upstream repository and cloned it locally. Two other remotes were added, `upstream` pointing to https://github.com/tektoncd/results and `downstream` pointing to https://github.com/openshift-pipelines/tektoncd-results. How to add those remotes is out of scope for this document. Given SSH is used for interaction with GitHub, `git remote -v` will return:

```
downstream      git@github.com:openshift-pipelines/tektoncd-results.git (fetch)
downstream      git@github.com:openshift-pipelines/tektoncd-results.git (push)
origin  git@github.com:<your-github-user>/results.git (fetch)
origin  git@github.com:<your-github-user>/results.git (push)
upstream        git@github.com:tektoncd/results.git (fetch)
upstream        git@github.com:tektoncd/results.git (push)
```

Our main branch follows `main` from upstream, while `downstream-v2` is the branch in downstream. If the `downstream-v2` branch does not exist, create it: `git branch downstream-v2 downstream/downstream-v2`.

## Steps to manually sync with upstream
1. Sync the upstream branch: `git checkout main && git pull upstream main`. This should always result upstream changes merged using the `Fast-forward` mechanism.
2. Create a new branch `git checkout -b do-not-merge-ci-$(date +%Y%m%d)`
3. Push that branch to your fork and create a PR. As a base repository choose `openshift-pipelines/tektoncd-results` and as a base branch `downstream-v2`. *This PR should not be merged. Its only purpose is to make the CI run and verify the changes*.
4. Wait for the CI to run and successfully verify the changes.
5. Close the PR with a comment "Upstream changes successfully verified".
6. Switch to the `downstream-v2` branch and sync with the `downstream` remote: `git checkout downstream-v2 && git pull --rebase downstream downstream-v2`.
7. Rebase the main branch: `git rebase main`
8. Push the new branch to the downstream fork: `git push -f downstream downstream-v2`

## Downstream only PRs

We are using upstream first approach, but sometimes we need a change only to the downstream fork. To create a PR to the downstream fork is pretty straightforward and will not be discussed here. To avoid merge commits and keep a linear history, the PR should be merge using `rebase and merge` strategy.
