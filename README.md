# gitr
gitr done across multiple git repositories concurrently

It's not uncommon now to see projects that span multiple git repos. 
Often, adding a new feature requires changes to many repos, not just one. 
This is a real hassle with git. Sure, there are submodules and subtrees, but they are cumbersome and error prone. 
Imagine if you could simply run git commands against all those repos at once?

Open your eyes; you're imagining `gitr`.

## Install
1. Have python 2.7 installed
2. Clone the repo.
3. Symlink `gitr` to a location in your $PATH, or add the repo location to your $PATH
4. You're done!

## Usage
If you know how to use git, you already know how to use `gitr`:

    gitr [repo path]... [any git command]
  
All you need to get started is to checkout the desired repos into the same parent directory.
Change into that directory and start typing git commands: only just add an extra `r`.

Say the following directory structure exists, and that `roles` and `profiles` are git repos:

    modules/
      roles/
      profiles/

Then with `gitr`, we can operate on these repos concurrently.

Show status:
```
$ cd modules/
$ gitr roles/ profiles/ status
Operating on [['roles', ''], ['profiles', '']]
#################### roles ####################
On branch master
Your branch is up-to-date with 'origin/master'.

nothing to commit, working directory clean

#################### profiles ####################
On branch master
Your branch is up-to-date with 'origin/master'.

nothing to commit, working directory clean
```
Checkout a branch for a new feature:
```
$ gitr roles/ profiles/ checkout -b my-feature-branch
Operating on [['roles', ''], ['profiles', '']]
#################### roles ####################
Switched to a new branch 'my-feature-branch'

#################### profiles ####################
Switched to a new branch 'my-feature-branch'
```
Create new files and see their status:
```
$ touch roles/manifests/new-role.pp
$ touch profiles/manifests/new-profile.pp
$ gitr roles/ profiles/ status

Operating on [['roles', ''], ['profiles', '']]
#################### roles ####################
On branch my-feature-branch
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	roles/manifests/new-role.pp

nothing added to commit but untracked files present (use "git add" to track)

#################### profiles ####################
On branch my-feature-branch
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	profiles/manifests/new-profile.pp

nothing added to commit but untracked files present (use "git add" to track)
```
Add the files in both repos at the same time:
```
$ gitr add roles/manifests/new-role.pp profiles/manifests/new-profile.pp
Operating on [['roles', 'manifests/new-role.pp'], ['profiles', 'manifests/new-profile.pp']]
#################### roles ####################

#################### profiles ####################
```
(We could have also just added the one file, or used `gitr roles/ profiles/ add -A`)

What's the satus now?
```
$ gitr roles/ profiles/ status
Operating on [['roles', ''], ['profiles', '']]
#################### roles ####################
On branch my-feature-branch
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   roles/manifests/new-role.pp


#################### profiles ####################
On branch my-feature-branch
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   profiles/manifests/new-profile.pp

```
Commit the results to both repos at the same time using the same commit message:
```
$ gitr roles/ profiles/ commit -m "Commiting role and profile for the new service."
Operating on [['roles', ''], ['profiles', '']]
#################### roles ####################
[my-feature-branch 474d3d6] Commiting role and profile for the new service.
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 manifests/new-role.pp

#################### profiles ####################
[my-feature-branch 706169b] Commiting role and profile for the new service.
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 manifests/new-profile.pp
```
Push to both repos at once:
```
$ gitr roles/ profiles/ push -u origin my-feature-branch
Operating on [['roles', ''], ['profiles', '']]
#################### roles ####################
To ssh://git@github.com:colinhunt/roles.git
 * [new branch]      my-feature-branch -> my-feature-branch
Branch my-feature-branch set up to track remote branch my-feature-branch from origin.

#################### profiles ####################
To ssh://git@git@github.com:colinhunt/profiles.git
 * [new branch]      my-feature-branch -> my-feature-branch
Branch my-feature-branch set up to track remote branch my-feature-branch from origin.
```

Now imagine instead of just 2 repos, it was 20 repos, and you'll get a sense of the time you can save
and errors you can avoid by using `gitr`.

## Config
You don't need to do any configuration at all to use `gitr`. However, there are some settings to make using it even easier.

`gitr` looks in a folder called `.gitr-config` in the parent directory for its config.

If you create a file called `manifests.yml` with the following content:
```
---
manifest:
  - roles
  - profiles
  # - components
```

Then `gitr` will operate on the `roles` and `profiles` repos by default, but not the `components` repo. This can save
having to type out the repos in the command each time. So now instead of

    gitr roles/ profiles/ status
    
you may simply type

    gitr status
    
to see the status (or run any other git command) against these repos. 
This is extremely useful if you often operate on the same repos all the time.

Note: you may still supply the repo paths to override the manifest.

That's all the config `gitr` supports for now.

Now go and gitr!
