* Prerequisites

The purpose of this document is describe the manual steps required to synchronize the downstream fork with upstream, while keeping the downstream only commits on the top of the branch.

Merge commits in the downstream repo led to duplication of commits while rebasing the upstream main branch. To avoid them, we chose to use `rebase merging` policy for merging the downstream only changes.

In the most traditional case, the developer forked the upstream repo and cloned it locally. Two other remotes were added, `upstream` pointing to https://github.com/tektoncd/results and `downstream` pointing to https://github.com/openshift-pipelines/tektoncd-results. How to add those remotes is out of scope for this document. So given SSH is used for interaction with GitHub, `git remote -v` will return:

```
downstream      git@github.com:openshift-pipelines/tektoncd-results.git (fetch)
downstream      git@github.com:openshift-pipelines/tektoncd-results.git (push)
origin  git@github.com:<your-github-user>/results.git (fetch)
origin  git@github.com:<your-github-user>/results.git (push)
upstream        git@github.com:tektoncd/results.git (fetch)
upstream        git@github.com:tektoncd/results.git (push)
```

The main branch follows "main" from upstream, while "downstream-v2" is the branch in downstream.

* Steps to manually sync with upstream
1. Sync the upstream branch: `git checkout main && git pull upstream main`. If new changes were brought from upstream, make sure they were merged using the `Fast-forward` mechanism and no merge commit was created.
2. Sync the downstream branch: `git checkout downstream-v2 && git pull downstream downstream-v2`
3. While on the downstream-v2 branch, create and checkout a new rebase-YYYYMMDD branch where YYYYMMDD is the date representation e.g 20220307: `git checkout -b rebase-YYYYMMDD`
4. Rebase the main branch: `git rebase main`
5. Push the new branch to your fork: `git push origin rebase-YYYYMMDD`
6. Create a PR from that branch to the downstream repo and downstream-v2 branch. In the description of the PR indicate:
    - New features
    - Changes that require updates to our deployment configuration
    - Breaking changes

* Downstream only PRs

We are using upstream first approach, but sometimes we need to change only the downstream fork. To create a PR to the downstream fork is pretty straightforward and will not be discussed here. The important bit though is how the PR is merged. It must be merged using `rebase and merge` approach to avoid creating a merge commit.
