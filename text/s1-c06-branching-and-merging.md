<!--
SPDX-FileCopyrightText: 2008 Geoffrey Grosenbach <boss@topfunky.com>
SPDX-FileCopyrightText: 2008 Scott Chacon <schacon@gmail.com>
SPDX-FileCopyrightText: 2023 Ezra Citron <93603564+ezra-oodle@users.noreply.github.com>
SPDX-FileCopyrightText: 2023 Robin Vobruba <hoijui.quaero@gmail.com>
SPDX-FileCopyrightText: 2023 gilch <gilch@users.noreply.github.com>

SPDX-License-Identifier: CC-BY-SA-3.0
-->

## Branching and Merging

Here we come to one of the real strengths of Git,
cheap inline branching.
This is a feature that truly sets it apart
and will likely change the way you think about developing code
once you get used to it.

When you are working on code in Git,
storing trees in any state and keeping pointers to them is very simple,
as we've seen.
In fact,
in Git,
the act of creating a new branch
is simply writing a file in the `.git/refs/heads` directory
that has the SHA-1 of the last commit for that branch.

> **NOTE** \
Creating a branch is nothing more than just writing 40 characters to a file.

Switching to that branch simply means
having Git make your working directory look like the tree that SHA-1 points to
and updating the `HEAD` file so each commit from that point on
moves that branch pointer forward
(in other words,
it changes the 40 characters in `.git/refs/heads/[current_branch_name]`
to be the SHA-1 of your last commit).

### Simple Case

![](../artwork/diagrams/branches1.svg)

Now,
let's see how Git handles branching,
fetching and merging operations abstractly.
For the following illustrations,
we will represent the entire tree and the commit it points to
as a single object.

Suppose that we work on a project for a while,
then we get an idea for something that may not work out,
but we want to do a quick proof-of-concept.
We create a new branch called `experiment` off of our main branch,
which is by convention called `master`.
We then switch to the new branch and create a few commits.

![](../artwork/diagrams/branch-story1.svg)

Then,
our boss comes in and says we need a hot fix to production.
So we switch back to our `master` branch,
make the change,
push the release and then tag the new commit with the release number.
Then we go back to our `experiment` branch,
continue working and commit again.

![](../artwork/diagrams/branch-story2.svg)

At this point,
we show the new branch code to our co-workers
and everyone likes the new changes.
We decide we want to merge them back into our main branch,
so we merge the changes and delete our `experiment` branch.

Our history of commit objects now looks like this:

![](../artwork/diagrams/branch-story3.svg)

### Remotes

Now lets take a look at remotes.
Remotes are basically pointers to branches
in other peoples copies of the same repository,
often on other computers.
If you got your repository by cloning it,
rather than initializing it,
you should have a remote branch of where you copied it from
automatically added as `origin` by default.
Which means the tree that was checked out during your initial clone
would be referenced as `origin/master`,
which means "the master branch of the origin remote."

Lets say you clone someone's repository and make a few changes.
You would have two references,
one to `origin/master` which points to where the master branch was
on the person's repository you cloned from when you did so,
and a `master` branch that points to the most recent local commit.

![](../artwork/diagrams/remote-story1.svg)

Now let's say you run a `fetch`.
A fetch pulls all the refs and objects that you don't already have
from the remote repository you specify.
By default,
it is origin,
but you can name your remotes anything,
and you can have more than one.
Suppose we fetch from the repository that we originally cloned from
and they had been doing some work.
They have now committed a few times on their master branch,
but they also branched off at one point to try an idea,
and they named the branch `idea` locally,
then pushed that branch.
We now have access to those changes as `origin/idea`.

![](../artwork/diagrams/remote-story2.svg)

We look at the `idea` branch and like where they're going with it,
but we also want the changes they've made on their `master` branch,
so we do a 3-way merge of their two branches and our `master`.
We don't know how well this is going to work,
so we make a `tryidea` branch first and then do the merge there.

![](../artwork/diagrams/remote-story3.svg)

Now we can run our tests
and merge everything back into our `master` branch if we want.
Then we can tell our friend we cloned from
to fetch from our repository,
where we've merged their two branches for them
and integrated some of our changes as well.
They can choose to accept or reject that patch.

### Rebasing

Let's say you and another developer,
Jen,
are working on the same project simultaneously.
She clones from you,
and works for a while and commits.
You have committed in the meantime and want to get your work in sync,
so you add her repository as the remote `jen`,
do a fetch and merge her changes in,
creating a new merge commit.
(All commits that are simply merges
are given a darker color in this example)

![](../artwork/diagrams/rebase1.svg)

At this point,
you both do work and commit changes
and then you fetch and merge from her again.
Then she does another commit and you fetch and merge once more.
At this point,
you'll have a commit history that looks something like this:

![](../artwork/diagrams/rebase2.svg)

Perfectly fine,
but it can get a little confusing
when you litter the history with all those commits
that do nothing but merge unshared changes.
The longer you keep out of sync,
the worse this can get.

This is where the rebasing command comes in.
With rebase,
Git will checkout the *upstream branch*,
in this case,
Jen's `master` branch,
and then replay all the changes you've done since you forked on top of those files,
as if you had forked your work off at *that* point and done all your changes later,
rather than earlier.

Rebase will literally produce a series of patch files of your work
and start applying them to the upstream branch,
automatically making new commits with the same messages as before
and orphaning your older ones.
These objects can then be removed,
since nothing points to them,
when you run the garbage collection tools
(see "The Care and Feeding of Git" section).

![](../artwork/diagrams/rebase3.svg)

So let's see what happens
if we rebase rather than merge in the same scenario.
Here we have our first merge
and we can see that it orphans _Commit 1_
and applies the changes between _Commit 0_
and _Commit 1_ to the files in _Remote Commit 1_,
creating a new _Commit 2_.

Then,
as you'll remember,
you and Jen both commit again.
You'll notice that now it looks like she cloned you and committed
and then you changed that code,
rather than you both working at the same time and merging.

![](../artwork/diagrams/rebase4.svg)

At this point,
instead of merging two more times like we did originally,
we rebase the next two commits she makes.

![](../artwork/diagrams/rebase5.svg)

![](../artwork/diagrams/rebase6.svg)

And finally,
we are left with a commit history that looks like Figure 1,
rather than Figure 2,
which is what we would have if we had merged instead.

![](../artwork/diagrams/rebase7-final.svg)

> **NOTE** \
You should remember to only do this on local branches before you push
or on repositories that nobody has fetch access to -
if anyone pulls down the objects that will become abandoned during a rebase,
it gets a bit frustrating.

### Use Cases

So why is this helpful,
exactly? It means that you can keep your development cycles loosely coupled.
Here is an example of a common workflow with cheap branches.

You have a `master` branch that is *always* stable -
you never merge anything into it that you wouldn't put into production.
Then you have a `development` branch
that you merge any experimental code into
before you imagine pulling it into the `master` branch.

> **NOTE** \
It's a common error to think of the `master` branch
as being equivalent to Subversion's `trunk`.
However,
a custom `development` branch is much closer in practice
to the Subversion `trunk`,
where experimental work is done.

You create a new branch each time you begin to work on a story or feature,
branching it off your current `development` branch each time,
so if you get blocked and need to put it on hold,
it doesn't affect anything else.
When you do get back to them,
you rebase them to the current `development`
and it is just like you started from there.
Often times you merge the branch back into `development`
and delete it the same day that you created it.

If you get a huge project or idea -
say refactoring the entire code base to the newest version of your framework
or switching database vendors or something,
you create a long-term branch,
continuously rebase it to keep it in line with other development,
and once everything is tested and ready,
merge it in with your master.

Working with others is unbelievably easy.
You ask in an IRC room
if someone has implemented a feature in a library you are using.
Turns out that someone has,
and you are sent the URL of their public Git repo for that project.
You add it as a remote,
fetch it,
create a new `merge-feature` branch off your `development` branch,
merge in the new changes and you're done.
There's no awkward emailing of patches -
you can just add contributors as a remote
and try out their branches before deciding to merge them in.
If it breaks things or is not a good patch,
you simply delete the `merge-feature` branch and that's it.

![Project collaboration using multiple branch forks and merges](
../artwork/screenshots/branching.png)

> **NOTE** \
A common problem with open source projects managed with Subversion
is that a patch is sent
which was made against and older version of the code base.
With Git,
it's as easy as applying the outdated patch in a new branch,
then rebasing from `master` to bring it up to date
with the current code base.

You branch and rebase or merge several times a day
in and out of several different branches,
some of which last for hours and some are continually there.
Once you get used to this pattern,
it completely changes the way you approach your development
and the way you contribute and collaborate.
