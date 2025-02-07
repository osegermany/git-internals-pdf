<!--
SPDX-FileCopyrightText: 2008 Geoffrey Grosenbach <boss@topfunky.com>
SPDX-FileCopyrightText: 2008 Scott Chacon <schacon@gmail.com>

SPDX-License-Identifier: CC-BY-SA-3.0
-->

## Working Directory

The working directory is the checkout of the current branch you are working on.
What is really important to note here is that this code is a working copy -
it is not really important.

This is something that developers from most other SCMs
have a hard time understanding and tends to scare them mightily.
If you check out a different branch,
Git will make your working directory look like that branch,
removing any checked in content that is currently in your working directory
that is not in the new tree.
This is why Git will only let you checkout another branch
if everything is checked in -
there are no uncommitted modified files.
The reason for this
is that Git will remove files
that are not necessary in the branch you are checking out -
it needs to make sure that you can get them back again.

Most users don't like to see content automatically removed from their directories,
but that's one of the mental shifts you'll need to make.
Your working directory is temporary -
everything is stored permanently in your Git repository.
Your working directory is just a copy of a tree
so you can edit it and commit changes.
