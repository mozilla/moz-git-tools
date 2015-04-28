# Tools for working with Git at Mozilla.

In order to set this up, clone this repository somewhere,
run the `git submodule init` and then `git submodule update`
command, and add your clone to $PATH.

Many of these tools rely on a notion of your current branch's "upstream
branch".  For example, `git push-to-try` pushes to try all patches in your
current branch that aren't upstream.  See the `git-tracks` section below for
details on how to change your upstream branch.

## git-bz

Push commits from git to bugzilla.  For example, to push the two top commits in
your repository, run

    git bz attach -e HEAD^^..

(This will complain if your commits don't mention the same bug number in their
summaries.)

If a commit's message starts with "FOLD", it will be folded into the previous
commit before pushing to bugzilla.

(Actually, git-bz can do more than push commits from git to bugzilla.  But I
don't use it for anything else, so I'm not sure which of the other features
work with bugzilla.mozilla.org.)

## git-bzexport

Usage: `git bzexport [-t/--tip] PATH_TO_HG_REPO [GIT_REVS]`

Push commits from git to bugzilla using the `bzexport` Mercurial extension from
the [Mozilla version control tools repo](https://hg.mozilla.org/hgcustom/version-control-tools).

The `bzexport` extension has some advantages over `bz attach` - for example, if
multiple Bugzilla reviewers match the reviewer name you specify, `bzexport` will
let you choose between them using a menu - so some users may prefer it.

This command is implemented using `git-push-to-hg`, so see that command's
documentation for more details on the argument syntax.

## git-new-workdir

Create a new working directory based off an existing local git repository.

## git-tracks

Gets the name of the current branch's upstream branch.  With `-d` or
`--default`, git-tracks outputs "origin/master" if there's no upstream branch.

You can set this with `git branch --set-upstream CURRENT_BRANCH
UPSTREAM_BRANCH`.  (Don't do `git branch --set-upstream BRANCH`; that won't
work right!)

Many other tools in this package use `git tracks -d` as your branch's "upstream
branch".

## git-to-hg-commit

Find the hg commit corresponding to a git commit.

## git-push-to-hg

Usage: `git push-to-hg [-t/--tip] PATH_TO_HG_REPO [GIT_REVS]`

Push commits from git to a new qqueue in an hg repository.  If GIT\_REVS is
omitted, push the commits `$(git merge-base HEAD $(git-tracks))..HEAD` (i.e.
everything in the current branch that's not upstream).

If `-t` or `--tip` is specified, pull and update the hg repository to latest
tip before pushing.  Otherwise, update the hg repository to the revision atop
which the git commits are based.

Normally, `git-push-to-hg` pushes your commits to a patch queue called
`git-temp`. If that patch queue already exists, it is destroyed and recreated.
If `--append` is specified, however, any existing `git-temp` patch queue will be
reused. This allows you to easily push commits from multiple git branches to the
same patch queue, or to push commits one at a time instead of all at once.

## git-push-to-try

Usage: `git push-to-try [-r/--rev REVISION_OR_RANGE] [-t/--tip] PATH_TO_HG_REPO TRYCHOOSER_PARAMS`

Push the commits `$(git merge-base HEAD $(git-tracks))..HEAD` (i.e. everything
in the current branch that's not upstream) to try, by way of the given hg
repository.

If `-r/--rev REVISION_OR_RANGE` is supplied, then push those commits to try
instead of `$(git merge-base HEAD $(git-tracks))..HEAD`. For example, if you are
working on a feature branch that was branched off of master, and want to push
everything on that branch to try, use `--rev master..HEAD`.

TRYCHOOSER\_PARAMS should be, e.g. `-b do -p all -u all -t none`.

## git-push-to-trychooser

Usage: `git push-to-trychooser [-t/--tip] PATH_TO_HG_REPO [GIT_REVS]`

The same as `git push-to-hg`, but also runs the interactive trychooser command
before pushing the commits to try from the given hg repository.

To use this, you must install the `trychooser` Mercurial extension from
[its repository](https://bitbucket.org/sfink/trychooser). (There are some
out-of-date versions of this extension floating around, so be sure to use this
repository.)

## git-qparent

Outputs the last common revision of the current branch and upstream.
(This command is a synonym for `git merge-base HEAD $(git-tracks)`.)

## git-qrebase

An alias for `git rebase -i $(git qparent)`.  This lets you interactively
rebase your current branch without moving the commits to a new upstream base.

## git-edit-files

Open all the files modified in the specified rev range in your `$EDITOR`.  (If
no rev range is specified, open the files modified in your current checkout.)

## git-fix-whitespace

Eliminate any trailing whitespace from your uncommitted changes.

Note that this will reset your index; that is, any changes you've `git add`'ed
will need to be added again.  But it won't (or at least, shouldn't!) erase
any changes.

## git-qapplied

Like `hg qapplied`, output the commits in this branch which are not upstream.

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

## pre-commit

A pre-commit hook which checks for .orig files and trailing whitespace.

To install this hook, symlink it into your repository's `.git/hooks` directory (with the name pre-commit).
