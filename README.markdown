# Tools for working with Git at Mozilla.

## git-bz

Push patches from git to bugzilla.

## git-new-workdir

Create a new working directory based off an existing local git repository.

## git-to-hg-commit

Find the hg commit corresponding to a git commit.

## git-push-to-hg

Usage: `git push-to-hg PATH_TO_HG_REPO [GIT_REVS]`

Push commits from git to a new qqueue in an hg repository.  If GIT\_REVS is
omitted, push the commits `$(git merge-base HEAD master)..HEAD` (i.e.
everything in the current branch that's not in master).

## git-qparent

Outputs the last common revision of the current branch and master.  (This command is a synonym for `git merge-base HEAD master`.)

You might use `git-qparent` when running `git rebase -i`.  `git rebase -i master` will move your commits onto the tip of master, whereas `git rebase -i $(git qparent)` will allow you to edit your commits without moving them.

## git-qapplied

Like `hg qapplied`, output the commits in this branch which are not in the master branch.

## git-patch-to-hg-patch

Format a patch from `git format-patch` as an hg patch.
