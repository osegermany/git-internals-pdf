<!--
SPDX-FileCopyrightText: 2008 Geoffrey Grosenbach <boss@topfunky.com>
SPDX-FileCopyrightText: 2008 Scott Chacon <schacon@gmail.com>
SPDX-FileCopyrightText: 2008 Scotty <schacony@gmail.com>
SPDX-FileCopyrightText: 2023 Richard Soderberg <rsoderberg@gmail.com>

SPDX-License-Identifier: CC-BY-SA-3.0
-->

# Understanding Git

In this section,
we will go over what Git was built for and how it works,
hopefully laying the groundwork
to properly understand what it is doing when we run the commands.

> **NOTE** \
The first commit message for the Git project was 'initial version of "git",
the information manager from hell' - Linus,
4/7/05

When I learned Git,
as many people do,
I learned it in the context of other SCMs I had used - Subversion or CVS.
I have come to believe that this is a horrible way to learn Git.
I felt far more comfortable with it
when I stopped thinking that `git add` was sort of like 'svn add',
but instead understood what it was actually doing.
Then I found I could find new and interesting ways to use
what is really a very powerful and cool toolset.

![](../artwork/diagrams/what-git-is.svg)

So,
let's see what it's doing behind the scenes first.
