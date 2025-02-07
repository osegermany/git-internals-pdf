<!--
SPDX-FileCopyrightText: 2008 Geoffrey Grosenbach <boss@topfunky.com>
SPDX-FileCopyrightText: 2008 Scott Chacon <schacon@gmail.com>

SPDX-License-Identifier: CC-BY-SA-3.0
-->

## The Git Data Model

In computer science speak,
the Git object data is a *directed acyclic graph*.
That is,
starting at any commit you can traverse its parents in one direction
and there is no chain that begins and ends with the same object.

All commit objects point to a tree and optionally to previous commits.
All trees point to one or many blobs and/or trees.
Given this simple model,
we can store and retrieve vast histories of complex trees
of arbitrarily changing content quickly and efficiently.

This section is meant to demonstrate how that model looks.

### References

In addition to the Git objects,
which are immutable - that is,
they cannot ever be changed,
there are references also stored in Git.
Unlike the objects,
references can constantly change.
They are simple pointers to a particular commit,
something like a tag,
but easily moveable.

Examples of references are branches and remotes.
A branch in Git is nothing more than a file in the `.git/refs/heads/` directory
that contains the SHA-1 of the most recent commit of that branch.
To branch that line of development,
all Git does is create a new file in that directory
that points to the same SHA-1.
As you continue to commit,
one of the branches will keep changing to point to the new commit SHA-1s,
while the other one can stay where it was.
If this is confusing,
don't worry.
We'll go over it again later.

### The Model

The basic data model I've been explaining looks something like this:

![](../artwork/diagrams/dag-model.svg)

The cheap references I've represented as the grey boxes, the immutable objects are the colored round cornered boxes.

### An Example

Lets look at an example of simple usage of Git
and which objects are stored in the Git object database as we go.

To begin with,
we commit an initial tree of three files and two subdirectories,
each directory with one file in it.
Possibly something like this:

```
.
|-- init.rb
`-- lib
    |-- base
    |   `-- base_include.rb
    `-- my_plugin.rb
```

When we first commit this tree,
our Git model may look something like this:

![](../artwork/diagrams/object-dag-tree1.svg)

We have three trees,
three blobs and a single commit that points to the top of the tree.
The current branch points to our last commit
and the `HEAD` file points to the branch we're currently on.
This lets Git know which commit will be the parent for the next commit.

Now let's assume that we change the `lib/base/base_include.rb` file and commit again.
At this point,
a new blob is added,
which changes the tree that points to it,
which changes the tree that points to that tree
and so on,
to the top of the entire directory.
Then a new commit object is added
which points to its parent and the new tree,
then the branch reference is moved forward.

Let's also say at this point we tag this commit as a release,
which adds a new tag object.

At this point,
we'll have the following in Git:

![](../artwork/diagrams/object-dag-tree2.svg)

Notice how the other two blobs that were not changed were not added again.
The new trees that were added point to the same blobs in the data store
that the previous trees pointed to.

Now let's say we modify the `init.rb` file at the base of the project.
The new blob will have to be added,
which will add a new top tree,
but all the subtrees will not be modified,
so Git will reuse those references.
Again,
the branch reference will move forward
and the new commit will point to its parent.

![](../artwork/diagrams/object-dag-tree3.svg)

At this point,
let's stop to look at the objects we now have in our repository.
From this,
we can easily recreate any of the three directories we committed
by following the graph from the most recent commit object,
and having Git expand the trees that are pointed to.

For instance,
if we wanted the first tree,
we could look for the parent of the parent of the HEAD,
or the parent of the tag.
If we wanted the second tree,
we could ask for the commit pointed to by the tag,
and so on.

![](../artwork/diagrams/object-dag.svg)

So,
to keep all the information and history on the three versions of this tree,
Git stores 16 immutable,
signed,
compressed objects.

### Traversal

So,
what do all the arrows in these illustrations really mean?
How does Git actually retrieve these objects in practice?
Well,
it gets the initial SHA-1 of the starting commit object
by looking in the `.git/refs` directory for the branch,
tag or remote you specify.
Then it traverses the objects by walking the trees one by one,
checking out the blobs under the names listed.

![](../artwork/diagrams/traversing-git-objects.svg)
