<!--
SPDX-FileCopyrightText: 2008 Geoffrey Grosenbach <boss@topfunky.com>
SPDX-FileCopyrightText: 2008 Scott Chacon <schacon@gmail.com>

SPDX-License-Identifier: CC-BY-SA-3.0
-->

## The Index

Git has two places that content states are stored.
The working directory stores one state at a time
in a human-editable format.
When committed,
that state becomes permanent and repeatable
by being stored in the object database.
However,
how do you determine what changes in your working directory
will go into your next commit?
Perhaps you have edited three files
and you want two to go into the first commit
(because they are related changes)
and the other file into a second commit.
This is where the index file comes in.

The index was called the cache for a while,
because that's largely what it does.
It is a staging area for changes
that are made to files or trees
that are not committed to your repository yet.
It acts as sort of a middle ground
between your working directory and your repository.
When you run `git commit`,
the resulting tree and commit object
will be built based on the contents of the index.

> **NOTE** \
Early on,
the index was called the *cache* or the *current directory cache*.

It is also used to speed up some operations.
It keeps track of the state of all the files in your working directory,
so you can quickly see what has been modified since the last commit.
