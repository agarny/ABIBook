﻿.. _OpenCMISS-git:

==================================
Tutorial: Version Control With Git
==================================

.. sectionauthor:: Adam Reeve

Getting Started
===============

If Git isn't installed already, it can be installed from the git-core
package if you're using Linux or by downloading msysGit for Windows.

The first thing that must be done before using Git is to tell it
your name and email address. These will be associated with any
commits you make::

  git config --global user.name "Adam Reeve"
  git config --global user.email "aree035@aucklanduni.ac.nz"

If you are using a graphical Git client such as Tortoise Git on Windows
or gitg on Linux there will be an option to edit these settings.


Creating a Repository
=====================

You can initialise a new Git repository in an existing directory with::

  git init

Or to create a new directory::

  git init my_project


Cloning an Existing Repository
==============================

You can clone an existing repository from a local path or from a URL. To
clone the repository for this tutorial, run::

  git clone https://github.com/adamreeve/git_tutorial.git

This will create a git_tutorial directory in your current directory, so
change into that directory now::

  cd git_tutorial

Have a look at the project history on the master branch with::

  git log

Also try out some of the options to the log command::

  git log --patch
  git log --stat
  git log --oneline --graph --all

The last example uses the "--all" option to show history in all branches.


Making and Committing Changes
=============================

Run :command:`git status` and you should get a message that there are
no changes to be staged and no staged changes to be committed.

Now make a change to the :file:`hello.py` file and run :command:`git status`
again. You should see that you still have no changes staged to commit
but you have an unstaged change.

Run :command:`git diff` go see what changes you have made. Now add your
change to the staging area::

  git add hello.py

And run :command:`git status` again. You will see that there are no unstaged
changes but one file has changes that are staged.

Now run :command:`git diff` and there should be no changes. This is because
:command:`git diff` compares your working directory with the staging area
by default. To see what changes are in the staging area and would be committed,
run::

  git diff --cached

If you've decided that you no longer want to commit a change that you've
added to the staging area, you'll need to unstage that file by running::

  git reset HEAD hello.py

.. note::
  git tells you how to stage and unstage changes in the output of
  the :command:`git status` command.

If you then decide that your change is rubbish and you want to remove
it completely, you can checkout a clean version of the changed file::

  git checkout -- hello.py

The "--" before the file path isn't required but is recommended as it means that
any further command options are file paths, which prevents confusion when you
have a file named the same as a branch (as :command:`git checkout` is also used
for checking out a branch, as you'll see later).

Now make another change and add it to the staging area, then commit it::

  git commit -m "My awesome change"

You can either specify a commit message on the command line with the "-m"
option or if you leave that option off, git will open a text editor to allow
you to enter a message. By default this is vim, but you might want to change
it to something else. For example, if you're on Linux::

  git config --global core.editor "gedit"

Or if you're on Windows and have TortoiseGit installed, you can use::

  git config --global core.editor "notepad2"

Now make another change to the file and then add this change to the staging
area, then run::

  git commit --amend

The amend option lets you update the previous commit. It will also open the
editor to let you update the commit message if required.
This is useful if you realise you've made a small mistake
in the previous commit and haven't yet pushed it to a public repository.

.. note::
  The commit hash changes after you amend it. This is now
  a different commit to the one before, so the hash has changed.

Now we'll try using git's graphical interface for making a commit. Make
a change to :file:`hello.py` then run::

  git gui

Stage the change you made then make another commit.


Branches
========

It's always a good idea to create a new branch for any new feature you're working
on in a project::

  git branch new_feature

This will create a new branch that points to the commit you have currently
checked out. You can also specify which commit the new branch should point to.
For example, to create a new branch that points to the same commit as the master
branch::

  git branch another_feature master

To delete a branch::

  git branch -d another_feature

This will give an error if the branch hasn't been merged into another branch.

Now checkout the branch you created. If there are any differences between
your previous head commit and the branch you are checking out, your working
directory will be updated::

  git checkout new_feature

You can create a new branch and check it out in one go by using the "-b" option
to the checkout command::

  git checkout -b my_feature

Now make some changes and commit them on your new branch.
You can see a list of branches and the branch you're on at
any time by running::

  git branch

Have a look at the history of your branch and the position of other branches
by running :command:`gitk`.


Merging and Resolving Conflicts
===============================

Now we will practice merging one branch into another branch. We will create
a new local branch that matches the "merge_into" branch from the origin
repository, and merge in the "merge_from" branch. First create the local branch
you will work on::

  git checkout -b merge_into origin/merge_into

Now run::

  gitk --all

This will show a tree with commits from all branches. Note where the heads
of the merge_from, origin/merge_from and origin/merge_into branches are.

Now merge the origin/merge_from branch::

  git merge origin/merge_from

And look at the result of your merge::

  gitk --all

Now we will try another merge, but this time there will be a conflict::

  git checkout -b merge_conflict origin/merge_conflict
  gitk --all
  git merge origin/conflicting

Read the output of the merge command to note that there is a conflict in
the :file:`hello.py` file. Also run :command:`git status`. When you have
conflicts in multiple files you can keep track of which conflicts have
been resolved with the status command.
Open that file in your editor and resolve the
conflict.
Then mark the conflict as resolved by adding the file::

  git add hello.py

And now you have to manually make a merge commit::

  git commit -m "Merge conflicting branch"


Remotes and Remote Branches
===========================

As you originally cloned this repository, you have one remote repository
set up already called "origin". To list the remote repositories you've
added with their urls::

  git remote -v

Branches on a remote repository can be checked out or referred to in
other commands by prefixing them with the remote name. For example, to
show the head commit of the master branch on the origin repository::

  git show origin/master

To see all branches including those on remote repositories, you can use::

  git branch -a


Staging Parts of Files
======================

Most Git graphical interfaces allow you to stage only some changes in
a file. From the Git command line you can do this with the "--patch" or
"-p" option to the add command. Change a line at the top of :file:`hello.py`
and then make another change at the bottom. Now run::

  git add -p hello.py

Say yes to adding the first change but no to the second change,
then run :command:`git status`, :command:`git diff`, and
then :command:`git diff --cached`.


Stashing Changes
================

Git's stash command is useful for storing changes you have made
that you want to save but don't want to commit yet. For example,
you can stash changes and then switch to another branch and do
some work, making commits, then go to another branch and recover
your stashed changes.

Run :command:`git status`. You should have a change added to the
staging area and another unstaged change from the last section.
Otherwise make a change to :file:`hello.py`.
Now stash those changes::

  git stash

And look at what this has done::

  git status
  git stash list
  git stash show -p stash@{0}

Your working directory and index should now be clean, but
your change is safely stored away in the list of stashes.

Now pop your stashed change off the top of the stash list::

  git stash pop
  git status
  git diff
  git stash list

Your changes are now back in your working directory, and have
been removed from the stash list.

Stash your change again to get a clean working directory::

  git stash

Note that you can use :command:`git stash apply` to apply a stashed
change without removing it from the stash list, and that you can
also apply a stashed change on a different branch to the one it was
made on.


Rewriting History
=================

Git has powerful tools for allowing you to rearrange history so that you
can clean up work to make the history more clear. The most useful
one to know is :command:`git rebase --interactive`. Rebasing means
to move a series of commits onto a new base commit. You can also
use the rebase command and keep the base the same, but still edit
and rearrange commits.

Checkout a new branch that points to the same commit as origin/rebase_me::

  git checkout -b rebasing origin/rebase_me

We will rebase these commits onto the latest master branch. First have
a look at what we will rebasing by running :command:`gitk --all` and looking
at the origin/rebase_me branch, or :command:`git log -p origin/master..rebasing`.

Now start the interactive rebase::

  git rebase --interactive origin/master

Reorder the commits so that the "Update docstring" commit is first and the
"Fix typo" commit is squashed into the "More excitement" commit.

Because you have squashed a commit, you will get the opportunity to change
the message of the commit that was squashed into.
Think about whether you should update the original
commit message to account for the change that was squashed.

Run :command:`gitk` to see what you've done.

Note that you can still access any rebased commits by their hash,
and you can find the commits that you have recently checked out with the
:command:`git reflog` command. This means that if you have committed
something it's very hard to permanently lose that work.


More on Remotes
===============

Create an account on GitHub if you don't have one already, then
fork this repository. Now make some changes to this repository (if you
can, make the tutorial better!) and push them to a branch
on your remote repository.
To push you'll first have to create an alias for your remote repository::

  git remote add github https://<your_username>@github.com/<your_username>/git_tutorial.git

Where "<your_username>" has been replaced with your GitHub username. Now to push
your changes to your remote repository::

  git push github <name_of_branch>

Now on the web page for your fork of the git_tutorial repository, click
on the pull request button and follow the steps to create a new pull
request.


* :ref:`genindex`
* :ref:`search`
