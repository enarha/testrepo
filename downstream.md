# Managing downstream fork

The purpose of this document is to describe the manual steps required to synchronize the downstream fork with upstream, while keeping the downstream only commits on the top of the branch.

## Requirements

1. Ability to manage downstream only changes
2. Downstream commits always on top of the upstream commits
3. Ability to review and run CI on the changes
4. The process works for everyone on the team (no specific state required)
5. Document the change? (see 6.3 below)

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
1. Sync the upstream branch: `git checkout main && git pull upstream main`. If new changes were brought from upstream, make sure they were merged using the `Fast-forward` mechanism and no merge commit was created.
2. Sync the downstream branch: `git checkout downstream-v2 && git pull downstream downstream-v2`
3. While on the downstream-v2 branch, create and checkout a new rebase-YYYYMMDD branch where YYYYMMDD is the date representation e.g 20220307: `git checkout -b rebase-YYYYMMDD`
4. Rebase the main branch: `git rebase main`
5. Push the new branch to your fork: `git push origin rebase-YYYYMMDD`
6. Create a PR from that branch to the downstream repository and downstream-v2 branch. In the description of the PR indicate:
    - New features
    - Changes that require updates to our deployment configuration
    - Breaking changes

## Downstream only PRs

We are using upstream first approach, but sometimes we need a change only to the downstream fork. To create a PR to the downstream fork is pretty straightforward and will not be discussed here. The important bit though is how the PR is merged. It must be merged using `rebase and merge` approach to avoid creating a merge commit.
