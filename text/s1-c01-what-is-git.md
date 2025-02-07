<!--
SPDX-FileCopyrightText: 2008 Scott Chacon <schacon@gmail.com>
SPDX-FileCopyrightText: 2023 Peter Whittaker <PeterWhittaker@SphyrnaSecurity.com>

SPDX-License-Identifier: CC-BY-SA-3.0
-->

## What is Git?

Git is a stupid content tracker.
That is probably the best description of it -
don't think of it in a 'like (insert favorite SCM system),
but...' context,
but more like a really interesting file system.

Git tracks content - files and directories.
It is at its heart a collection of simple tools
that implement a tree history storage and directory content management system.
It is simply used as an SCM,
not really designed as one.

> **NOTE** \
"In many ways you can just see git as a filesystem ---
it's content-addressable,
and it has a notion of versioning,
but I really really designed it
coming at the problem from the viewpoint of a filesystem person
(hey,
kernels is what I do),
and I actually have absolutely zero interest
in creating a traditional SCM system." -
[Linus](https://marc.info/?l=linux-kernel&m=111314792424707)

When most SCMs store a new version of a project,
they store the code delta or diff.
When Git stores a new version of a project,
it stores a new *tree* - a bunch of blobs of content
and a collection of pointers that can be expanded back out
into a full directory of files and subdirectories.
If you want a diff between two versions,
it doesn't add up all the deltas,
it simply looks at the two trees and runs a new diff on them.

This is what fundamentally allows the system to be easily distributed -
it doesn't have issues figuring out how to apply a complex series of deltas,
it simply transfers all the directories and content that one user has
and another does not have but is requesting.
It is efficient about it -
it only stores identical files and directories once
and it can compress and transfer its content using delta-compressed packfiles -
but in concept,
it is a very simple beast.
Git is at its heart very stupid-simple.
