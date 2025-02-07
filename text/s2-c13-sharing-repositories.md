<!--
SPDX-FileCopyrightText: 2008 Geoffrey Grosenbach <boss@topfunky.com>
SPDX-FileCopyrightText: 2008 Scott Chacon <schacon@gmail.com>

SPDX-License-Identifier: CC-BY-SA-3.0
-->

## Sharing Repositories

### Over Git

Git provides its own special protocol,
which is basically just a really thin wrapper
over the 'git-upload-pack' command
that will tell you what is available,
then you tell it what you have
and it gives you a packfile of the difference.
To start it up manually,
run something like the following command:

```shell
git-daemon --detach --export-all --base-path=/opt/git /opt/git/ambition
```

Though for long term running,
you'll likely want to add this to your inet.d configuration.
See the [git-daemon](
https://mirrors.edge.kernel.org/pub/software/scm/git/docs/git-daemon.html)
docs for specific information on how to set that up.

The git protocol has no built in authentication,
so generally you cannot push over it
(although some people have open push policies and so allow that -
I would not recommend setting that up,
so you'll have to look up how to do that).
If you want your users to have push access,
it's recommended to use the SSH protocol.
Many repositories,
like GitHub,
have SSH enabled for account owners to push over,
and git-daemon enabled for the public to pull over -
which is often the most efficient combination.

- [git-daemon](https://mirrors.edge.kernel.org/pub/software/scm/git/docs/git-daemon.html)

### Over SSH

Git can work entirely over SSH -
it actually does much the same thing that happens over the git protocol,
except it implicitly has authentication built in.
If a user has SSH and write access to the repo and its subdirectories,
then that user can push over SSH as well.

```shell
$ git clone --bare
scp -r project.bare user@host:/repos/project.git
git clone user@host:/repos/project.git
```

Git ships with the `git-shell`,
a slightly more secure shell
that restricts the user to performing only Git-related actions.
If you use SSH public keys,
you won't need to give out any passwords
or create user accounts for other developers
who need to have write-access to the repository.
However,
the restricted `git` user needs to have write access
to the files and directories in the repository,
which can be slightly awkward to setup and maintain.

### Over HTTP

This is pretty easy to setup
because there is no requirement other than a static web server -
it is not like SVN which requires a DAV server to run.
If you want to push over http,
however,
you will need DAV setup -
it's generally a much better idea to use SSH to do so,
however.

The only caveat is
that you need to run `git update-server-info`
each time you commit to the repository.

```shell
git update-server-info
```

It is generally a good idea
to put this in the post-commit hook file on any server
that you want to be able to fetch from over HTTP.

> **NOTE** \
Actually,
you can run this without `update-server-info`
if you never pack objects
and you have a pre-defined list of branches you are always fetching.
All `update-server-info` does
is put a list of branches into one file (`.git/info/refs`)
and a list of pack files into another (`.git/objects/info/pack`)
to get around the fact
that you cannot reliably list contents of a directory over HTTP -
it's not built into the protocol.
So for Git to know what packfiles and branches are available,
it needs to have one URL it can get those from.
