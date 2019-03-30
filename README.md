# Git Collaboration


## Learning Objectives

After this reading we should know how to:

1. Create and checkout a new branch for your repository with `git checkout -b <branch name>`
2. Create commits within a branch using `git commit -m "Your message"`
3. Merge branches with `git merge`
4. Work with remote branches using `git fetch` and `git merge` or `git pull`
5. Explain why the git workflow is so important


## Introduction

A key to collaborating with git is to keep discrete and individual lines of work isolated from each other. Consider the following scenario:

It's your first week at your new job as a Software Engineer. You came at just the right time, the customers of the company you work for have been asking for updates to the app. It's time to work on that new update! You start working on the big feature (for the sake of this readme let's keep it simple by calling it "new-feature") and make a few commits but don't entirely finish the feature. Your `git log` might look like:

```
512bec5 Still broken, working on new-feature (yourname, 2 hours ago)
62d840 Almost done with new-feature (yourname, 1 day ago)
fbee832 Started new-feature (yourname, 2 days ago)
```

Based on our log above we can see:
* Two days ago we started working on our new-feature
* Yesterday we were almost done
* Today we made progress, but it's still broken

In our current state, if we had to push the repository live and deploy the latest version of our code to production, our users would see the half-finished, currently broken new-feature. That's no good.

But no big deal, right? We can just wait until we're done with the new-feature to deploy our code and push the repository live to our users. Here's what happens though, we notice a big bug that is currently breaking the application for all users. The bug is an easy fix, one simple change and deployment of your code can make everything work again. Unfortunately, even if you made that commit, you can't currently deploy it because while that commit might fix the bug, you'd still be pushing up your half-finished and broken new-feature. Let's take a look at our current `git log` again:

```
r4212d1 Fix to application breaking bug (yourname, just now)
512bec5 Still broken, working on new-feature (yourname, 2 hours ago)
62d840 Almost done with new-feature (yourname, 1 day ago)
fbee832 Started new-feature (yourname, 2 days ago)
```

See, we can't push *all* those commits. Wouldn't it have been great if we simply isolated our work on the new-feature into its own copy of our code so that until it's done, we could have deployed the commit that fixes the application? We can do exactly this using a feature in git called branches.


## Create and Checkout a New Branch

### Making a branch with `git branch`

Below we're going to quickly cover making a repository that we can use as a sandbox to experiment with the collaborative features of git. You will become familiar with GitHub collaboration concepts from the reading alone, but if you learn better by doing please follow along and copy + paste these commands in your terminal.

In our terminal, let's navigate to the folder where we keep our code projects and make a new directory for an application titled, `mission-critical-application`.

```
code-projects $ mkdir mission-critical-application
code-projects $ cd mission-critical-application
mission-critical-application $ git init
mission-critical-application $ touch application.rb
mission-critical-application $ git add application.rb
mission-critical-application $ git commit -m "First working version of application.rb"
```

Let's review what's happening above, line by line:
1. We made a new directory (a file folder) with `mkdir mission-critical-application`.
2. We moved into that directory with `cd mission-critical-application`.
3. We turned that directory into a local git repository with `git init`.
4. We created our application file inside our directory with `touch application.rb`.
5. Although this is not reflected above, let's imagine we programmed the entire first working version of the app in `application.rb` (*it was awesome, great job*).
6. We added the `application.rb` file to git with `git add application.rb`.
7. We committed the first working version of the application with `git commit -m "First working version of application.rb"`.
8. Again, not reflected above, but imagine we deployed the application to production and people started using it (*it too was awesome, great job*).

With our application online and customers rolling in, we notice a bug. We quickly add a fix in the form of a file, we're going to title `first-bug-fix.rb` (for example).

```
mission-critical-application $ touch first-bug-fix.rb
mission-critical-application $ git add first-bug-fix.rb
mission-critical-application $ git commit -m "First bug fix"
```

Let's visualize our git log as a timeline composed of two commits.

![First Two Commits](https://dl.dropboxusercontent.com/s/ikorf1qvvp4tay0/2015-11-02%20at%2011.15%20AM.png)

### About the `master` branch

Notice that these commits are occurring in a linear sequence of events, almost like a timeline? Let's think of this "timeline" as a branch. Whenever you are committing your code in git, you are adding them on a timeline of code called a branch. The branch you are on at the start of any repository is called `master` (or the master branch), think of it as your "main timeline".

![Master Branch](https://dl.dropboxusercontent.com/s/v75as2cf6xr8n8a/2015-11-02%20at%2011.17%20AM.png)

How do we confirm which branch we're on? `git status` will always tell us what branch we're on.

```
mission-critical-application $ git status
On branch master
nothing to commit, working tree clean
```

The `master` branch is our default branch. One of the responsible ways to use git is to make sure that the `master` branch is *always clean with working code* so if we ever need to add a bug fix, we can deploy a new version of the application immediately.

**Important: We do not put broken code in master so that we can always deploy master.**

### Starting a new feature with `git branch new-feature`

To keep master clean, we should start all new features in an isolated "feature branch". This is how our timeline will change:

![Feature Branch](https://dl.dropboxusercontent.com/s/d61r0fxyriaf5oj/2015-11-02%20at%2011.52%20AM.png)

After commit two, we will branch out of `master` and create a new timeline for commits and comments specifically related to the new feature. Our master timeline remains unchanged and clean. Now that we've covered the importance of creating new branches, let's actually create one!

To make a new branch simply type: `git branch <branch name>`. In the case of the branch relating to our new feature, we'll name our branch `new-feature` like so:

```
mission-critical-application $ git branch new-feature
```

To see a list of all our branches we can type: `git branch -a`

```
mission-critical-application $ git branch -a
* master
  new-feature
```

The `*` in front of the branch `master` indicates that `master` is currently our working branch and git tells us that we also have a branch called `new-feature`. If we made a commit right now, that commit would still be applied to our `master` branch.  That's not what we want! Let's learn how to switch to the branch we just created.

### Switching branches with `git checkout`

We need to move into our `new-feature` timeline or branch so that git knows that all commits made apply to only that unit of work. We can move between branches with `git checkout <branch name>`.

```
mission-critical-application $ git status
On branch master
nothing to commit, working tree clean
mission-critical-application $ git checkout new-feature
Switched to branch 'new-feature'
mission-critical-application $ git status
On branch new-feature
nothing to commit, working tree clean
```

We started on `master` and then checked out to our `new-feature` branch with `git checkout new-feature`, thereby moving into that timeline.

Let's make a commit in this `new-feature` branch and get the feature started by making a new file, `new-feature-file.rb` to represent the code for the new feature.

```
mission-critical-application $ touch new-feature-file.rb
mission-critical-application $ git add new-feature-file.rb
mission-critical-application $ git commit -m "Started new feature"
[new-feature 332a618] Started new feature
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new-feature-file.rb
```

Above we just made a commit to the timeline of the `new-feature` branch.

Let's pause to do a happy dance! We just learned how to:
1. Create a branch
2. Checkout (move into) that branch
3. Make a commit in that branch

These three steps are some of the most crucial ones in the git workflow. We have just saved ourselves from corrupting our master branch and now we're free to safely experiment in the playground that is our own branch. Congratulations!

Okay, back to work — right as we got started on our new feature, we get another bug report and have to move back into master to fix the bug and then deploy master. How do we move from the `new-feature` branch back to `master`? What will our code look like when we move back to `master`? Will we see the remnants of the `new-feature` branch and code represented by the `new-feature-file.rb`? Try answering these questions before moving on.

**Protip: You can create and checkout a new branch in one command using: `git checkout -b new-branch-name`. That will both create the branch `new-branch-name` and move into it by checking it out.**

#### Moving back to `master` with `git checkout master`

You can always move between branches with `git checkout`. Since we are currently on the `new-feature` branch, we can move back to master with `git checkout master`.

```
mission-critical-application $ git checkout master
Switched to branch 'master'
```

And we could move back to `new-feature` with `git checkout new-feature`

```
mission-critical-application $ git checkout new-feature
Switched to branch 'new-feature'
```

And back again with:

```
mission-critical-application $ git checkout master
Switched to branch 'master'
```

![Switching between branches](https://dl.dropboxusercontent.com/s/qzajqsd9f6njauc/2015-11-02%20at%2012.12%20PM.png)

One thing you'll notice in your `master` branch is the code you wrote on your `new-feature` branch, namely the file we added titled, `new-feature-file.rb` is not present in the current directory.

```
mission-critical-application $ ls
application.rb first-bug-fix.rb
```

The `master` branch only has the code from the most recent commit to the branch's timeline. The code from our `new-feature` branch is safely tucked away in it's own timeline, waiting patiently in isolation from the rest of our code in `master` for us to finish the feature.

Once you're on master you are free to make a commit to fix the bug, which we'll represent with a new file, `second-bug-fix.rb`.

```
mission-critical-application $ touch second-bug-fix.rb
mission-critical-application $ git add second-bug-fix.rb
mission-critical-application $ git commit -m "Second bug fix"
```

Let's look at our timeline now.

![Commit on Master](https://dl.dropboxusercontent.com/s/9ipgkog7yv8hrok/2015-11-02%20at%2012.18%20PM.png)

We were able to update the timeline in master with the bug fix without touching any of the code in `new-feature`. If we look closely we see, the `new-feature` branch remains one commit behind `master`. This is because the second bug fix commit occurred in `master` and the `new-feature` branch was created off of the last commit in `master` (in this case, at commit number two). Alternatively, you could describe `master` as being one commit ahead of the `new-feature` branch.

Let's go back into `new-feature` to complete the feature and commit our code, then revisit the timeline. Remember how to move from `master` back to `new-feature`?

```
mission-critical-application $ git status
On branch master
nothing to commit, working tree clean
mission-critical-application $ git checkout new-feature
Switched to branch 'new-feature'
```

Let's rename `new-feature-file.rb` to `new-feature.rb` to signify the code we wrote to complete the feature and commit this change. We can rename a file with the `mv <original filename> <new filename>` bash command.

```
mission-critical-application $ mv new-feature-file new-feature
mission-critical-application $ git add new-feature
mission-critical-application $ git commit -m "Finished feature"
[new-feature bfe50fc] Finished feature
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new-feature
```

Let's look at our timeline now.

![Completed Feature Branch](https://dl.dropboxusercontent.com/s/xtoehu7tv5zim6v/2015-11-02%20at%2012.31%20PM.png)

The final step to implement our `new-feature` code is to figure out how to "merge" that timeline into the master timeline.


## Merging Branches with `git merge`

Our goal is to combine the timeline of commits that occurred on the `new-feature` branch with the `master` branch so that our master timeline looks like:

![Merged Timeline](https://dl.dropboxusercontent.com/s/bf0cktf3ag549z2/2015-11-02%20at%201.15%20PM.png)

By merging the timelines, `master` will have all of the commits from the `new-feature` branch as though those events occurred on the `master` timeline.

When we merge a branch using `git merge`, it is important to currently be on our "target branch", which is the branch you want to merge into. For example, the first step for our `new-feature` merge is to checkout to `master` because that is where we want the commits to end up.

```
mission-critical-application $ git checkout master
Switched to branch 'master'
```

Once on your target branch, type: `git merge <branch name>` where `<branch name>` is the branch we want to merge. In this case, `git merge new-feature` will do the trick.

```
mission-critical-application $ git merge new-feature
Updating e5830af..bfe50fc
Fast-forward
 new-feature      | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new-feature
```

Now that the branches have been merged, when you type `ls` (list segments) in your terminal you'll see the `new-feature.rb` file from the `new-feature` branch in our current working directory (the `master` branch).

![Merging Gif](https://media.giphy.com/media/mW0zaDZZ9aYzS/giphy.gif)


## Working With Remote Branches Using `git fetch` and `git merge` or `git pull`

### What are remotes?

Github's [documentation](https://help.github.com/en/articles/about-remote-repositories) describes a remote URL (or repository) as,

> Git's fancy way of saying "the place where your code is stored." That URL could be your repository on GitHub, or another user's fork, or even on a completely different server.

Your local branches can attach to remote branches that live on the internet, generally on GitHub, that your team members might contribute to and you can download locally.  Git is lovely and kind of enough to identify these remote URLs by their names, and while we can change a remote's name, it is named "origin" by default. We will see the remote name "origin" appear several times below.  Don't worry if it's all a little overwhelming at this point, Git is *POWERFUL* and we're covering a lot. For right now it's okay to just think of "origin" as: the name for our remote URL which is just a place where our code is stored for other folks to access.

If you want to continue following along in your terminal create a repository on the Github website for our "mission-critical application" and run `git remote add origin <remote URL>` in your terminal. This will create a remote repository for our local repository that we have already initialized using `git init` earlier this readme. The GitHub's remote URL usually looks something like: `git@github.com/<github username>/<repo name>.git`.

### Using `git fetch` With Remote Branches

Now that we're familiar with the concept of remote repos (repositories) in GitHub...let's say you wanted to update your local copy of the repo with all the branches that might have been added to the GitHub remote repo. Say hello to, `git fetch`.

```
mission-critical-application $ git fetch
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 4 (delta 3), reused 3 (delta 2), pack-reused 0
Unpacking objects: 100% (4/4), done.
From github.com:aviflombaum/mission-critical-application
   bfe50fc..0ae1da2  master     -> origin/master
 * [new branch]      remote-feature-branch -> origin/remote-feature-branch
```

From within `master` (though technically what branch we type `git fetch` from does not matter), we executed `git fetch`. The lines starting with `remote:` in your terminal may differ from what's above but, the last three lines of output are really important. Let's take a closer look:

```
From github.com:aviflombaum/mission-critical-application
   bfe50fc..0ae1da2  master     -> origin/master
 * [new branch]      remote-feature-branch -> origin/remote-feature-branch
```

The first line, `From github.com:aviflombaum/mission-critical-application` is informing us which remote our `git fetch` updated from, namely, the remote repository located at: https://github.com/aviflombaum/mission-critical-application.

When we `fetch` with git, we are asking to copy *all* changes on the remote to our local git repository, **but not actually integrate any**. The next line, `bfe50fc..0ae1da2  master     -> origin/master` is telling us that a new commit was found in `origin/master`. `origin/master` means the GitHub (remote) version of `master`. Even though git fetched a new commit from `origin/master`, it did not merge it into the local master.

![Fetch without integration](https://dl.dropboxusercontent.com/s/iy2jovft8ykrxbd/2015-11-02%20at%202.08%20PM.png)

Our remote copy on GitHub has a file, `remote-bug-fix`, presumably some code that another developer pushed up to our remote version of the `master` branch to fix a bug. Even after we fetched, our local copy still doesn't appear to have that file. Interesting...

### Using `git merge` With Remote Branches

After you fetch, you have access to the remote code but you still have to merge it. How do you merge a change fetched from `origin/master` into your current master? From within your local master branch, type: `git merge origin/master`, referring to the branch's full path, `<remote name>/<branch name>`, in our case `origin/master`.

```
mission-critical-application $ git merge origin/master
Updating bfe50fc..0ae1da2
Fast-forward
 remote-bug-fix | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 remote-bug-fix
mission-critical-application $ ls
 application.rb		new-feature-file    new-feature		
 remote-bug-fix
```

The commits fetched via `git fetch` are now merged from the **remote** `origin/master` branch into our **local** `master` branch. Now `ls` reveals that the file is present on the remote, `remote-bug-fix` is integrated into our local copy of `master` as well.

When we fetched, git also outputted: `* [new branch]      remote-feature-branch -> origin/remote-feature-branch`. Similarly, git fetched a new branch and if we want to check it out or merge it we can using `git checkout` or `git merge`. Let's checkout what code is on `remote-feature-branch`, a branch another developer made for another feature and pushed up to GitHub so they can share it with us.

```
mission-critical-application $ git checkout remote-feature-branch
Branch remote-feature-branch set up to track remote branch remote-feature-branch from origin.
Switched to a new branch 'remote-feature-branch'
```

When we checkout to a remote branch that we fetched, git will create a local branch to track that remote and switch to that local branch. We can now do work, push it back up to GitHub, and another developer can fetch those changes down. Yay! Now we're cooking (collaborating).

![Lil B and Andy Milonakis Doing Cooking Dance](https://media.giphy.com/media/Kb7iHzliCrIOI/giphy.gif)

`git fetch` is a pretty low-level git command we don't use that much because it always requires two steps. First we have to `git fetch` and then do `git merge` to actually integrate those changes into our working branch. Generally, if you are in `master` you want to immediately `fetch` and `merge` any changes to the remote master.

### Combining `git fetch` With `git merge` By Using `git pull`

If we want to both fetch *and* merge, which is what we want to do 99% of the time, we can just type `git pull`. `git pull` is literally the combination of both `git fetch` and `git merge`.

When you `git pull` the following things will occur:

1. You will `git fetch` all remote changes, including those on the current branch, existing branches, and new branches.
2. Any changes that are on a remote branch will be automatically merged, because it is being tracked by your local branch. For example: if you are on `master` and there is a change to `origin/master`, those changes will be automatically merged.


## Conclusion

Git is complex, and collaborating with people in this manner is just hard - there's no easy way to allow *hundreds* of people to all work on the same code base. These workflows are just being introduced to you. You'll have lots of time during pair programming exercises to practice them and memorize what each command does. Don't try to learn all the git commands at once, instead just start to get an understanding of why the commands introduced in this reading are important and when to use them.  

![XKCD Git](http://imgs.xkcd.com/comics/git.png)

But don't do this! :)


## Check for Understanding

At the end of this readme we've summarized the list of commands we introduced for your reference, but before taking a peak at the summary — test yourself and check for understanding. Without referencing the summary, try answering the following questions before moving on.

1. Why do we use GitHub?
2. What is a branch?
3. How do we create and checkout into a branch?
4. How do we make a commit with a message?
5. What are remote GitHub repositories (repos)?
6. How do we update our local copy of the repo with all the remote branches?
7. How do we merge updated remote branches?
8. How do we update our local copy of the repo *and* merge remote branches in one git command?

Treat answering these questions as a benchmark for understanding the GitHub workflow. Happy collaborating!

## Summary

1. `git branch`: Confirm current branch
2. `git branch -a`: List of all branches
3. `git branch <branch name>`: Make and name a new branch
4. `git checkout <branch name>`: Checkout a branch
5. `git checkout -b <branch name>`: Create and checkout a new branch
6. `git commit -m "Your message here"`: Create commits with a message within a branch
7. `git merge <branch name>`: Merge branches from master
8. `git fetch`: Update branches from remotes
9. `git merge <remote name>/<branch name>`: Merge updated remote branches
10. `git pull`: Update and merge remote branches
11. `git log`: A log of commits with timestamps
12. `git status`: Provides info on the "status" of your branch
13. `git remote add origin <remote url>`: Creates a remote for the local copy of your repo


### Resource

* [Understanding the GitHub flow](https://guides.github.com/introduction/flow/)

<a href='https://learn.co/lessons/git-collaboration-readme' data-visibility='hidden'>View this lesson on Learn.co</a>

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/git-collaboration-readme'>Git Collaboration</a> on Learn.co and start learning to code for free.</p>

<p class='util--hide'>View <a href='https://learn.co/lessons/git-collaboration-readme'>Git Collaboration</a> on Learn.co and start learning to code for free.</p>
