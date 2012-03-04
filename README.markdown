# Tools for working with Git at Mozilla.

## git-bz

Push patches from git to bugzilla.

## git-new-workdir

Create a new working directory based off an existing local git repository.

## git-to-hg-commit

Find the hg commit corresponding to a git commit.

## git-push-to-hg

Usage: `git push-to-hg [-t/--tip] PATH_TO_HG_REPO [GIT_REVS]`

Push commits from git to a new qqueue in an hg repository.  If GIT\_REVS is
omitted, push the commits `$(git merge-base HEAD master)..HEAD` (i.e.
everything in the current branch that's not in master).

If `-t` or `--tip` is specified, update the hg repository to its tip before
pushing.  Otherwise, update the hg repository to the revision atop which the
git commits are based.

## git-qparent

Outputs the last common revision of the current branch and master.  (This command is a synonym for `git merge-base HEAD master`.)

You might use `git-qparent` when running `git rebase -i`.  `git rebase -i master` will move your commits onto the tip of master, whereas `git rebase -i $(git qparent)` will allow you to edit your commits without moving them.

## git-edit-files

Open all the files modified in the specified rev range in your `$EDITOR`.  (If
no rev range is specified, open the files modified in your current branch.)

## git-qapplied

Like `hg qapplied`, output the commits in this branch which are not in the master branch.

## git-patch-to-hg-patch

Format a patch from `git format-patch` as an hg patch.

## git-branchname

Output the name of the active git branch, but if there's no git repository
below the cwd, output nothing.  This is useful when you want to display the
current branch name on the command line.

For example, I have in my ~/.bashrc:

    function vcs-branchname() {
      git_branch=`git branchname`
      if [[ "$git_branch" != "" ]]; then
        echo " ($git_branch)"
      fi
    }

    PROMPT_COLOR="35m"
    PROMPT_COMMAND='BRANCH_NAME=`vcs-branchname`'
    PS1='\[\033[01;$PROMPT_COLOR\]\u@\h\[\033[00m\]:\[\033[01;$PROMPT_COLOR\]\w\[\033[00m\]$BRANCH_NAME\$ '

which makes my prompt look like

    jlebar@hostname:~/current/path (name-of-git-branch)$
