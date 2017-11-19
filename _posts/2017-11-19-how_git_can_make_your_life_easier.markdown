---
layout: post
title:      "How Git can make your life easier"
date:       2017-11-19 13:30:28 +0000
permalink:  how_git_can_make_your_life_easier
---


I'm not too proud to admit that there have been times when I've completely messed up a program I was working on.  In other words, I made changes that completely broke a program for reasons I could not easily figure out.  After this happened to me a few times, I started saving my files with a version number.  I'd have files named project-v1, project-v2, and so on, so that I could easily restore my program to a working state.  Not the most efficient way to do things, but it worked.  But using Git to track changes is a much better, more systematic way to do things, and it's made my life as a programmer much easier.

Git is one of the first topics covered in the Learn.co curriculum; for the uninitiated, Git is a version control system.  In other words, it lets one or more programmers keep track of changes made to a program.  This comes in handy when you want to undo some changes you made in a project, as described below.

Assuming you have git installed, the first thing to do when you want to use Git to track changes is to run `git init` in the directory your project is in.  This sets up the directory as a so-called Git repository.  Each version of the project which Git stores is called a "commit".  To create a new commit, run the following commands in the terminal in the project directory:

```
git add .
git commit -m "Message describing the commit"
```

The first command "stages" the files you changed, and prepares for the `git` commit.  The second command actually creates a commit in the Git repository with the changes made, along with a message describing the commit.

Once you create a commit, you can easily go back to it.  If you want to go back to the last commit, simply run the code below:

`git reset --hard`

I'll admit that I've personally run this one line in the terminal quite a few times, and that it's made my life as a would-be programmer much easier.  Now, let's say you made a git commit that you want to undo.  In that case, you can simply run

`git reset --hard HEAD~1`

In case you're curious, HEAD~1 is the commit prior to the current commit, while the --hard flag is used to change the files in the project directory back to what they were in the last commit.  Now, you don't have to roll back only one commit at a time.  Running `git log` in a local git repository (which you create using `git init`) will list the commits made to a project, giving you something like this:

```
c7f2876 Commit 4 (Larry, 2 seconds ago)
b0a7a89 Commit 3 (Larry, 27 seconds ago)
d96b0f3 Commit 2 (Larry, 48 seconds ago)
12145fc Commit 1 (Larry, 74 seconds ago)
```

Each commit is listed above as follows:

`commit ID commit message (author, time since commit)`

For instance, the second commit from the start of the project has a commit ID of d96b0f3, a commit message of "Commit 2", and was made by "Larry" 48 seconds prior to running `git log`.  To rollback to an earlier commit, you can run `git reset --hard git_id.`  For example, to rollback to "Commit 2" above you would run

`git reset --hard d96b0f3`

The files in the the project directory would then revert to the state that they were in at Commit 2.  Furthermore, Commit 3 and Commit 4 would disappear when you run `git log`.  This may make it seem like `git reset --hard `is completely and utterly irreversible.  Which would make` git reset --hard` a very, very scary command.

Fortunately, Git makes it relatively easy to move forward from an ill-advised `git reset --hard`.  Assuming you made a Git commit before the `git reset --hard`, that is. Git doesn't actually remove old commits for 90 days or so.  You can see old commits that haven't been removed, including ones after a commit which you reset to, by running `git reflog.`  In the example above, this gives:

```
d96b0f3 HEAD@{0}: reset: moving to d96b0f3
c7f2876 HEAD@{1}: commit: Commit 4
b0a7a89 HEAD@{2}: commit: Commit 3
d96b0f3 HEAD@{3}: commit: Commit 2
12145fc HEAD@{4}: commit (initial): Commit 1
```

As seen above, Commit 3 and Commit 4 still exist despite the reset described earlier and their absence when running `git log`.  Phew, what a relief!  You can undo a reset and move back to a commit by resetting to the desired commit ID.  For instance, to move back to Commit 4 in the example above you'd run

`git reset --hard c7f2876`

This will reset the project directory to the way it was in Commit 4 before rolling back to the earlier Commit 2.

Of course, Git can do much more than simply rolling a program back to an earlier state.  Git can also be used to manage different branches of a project, to coordinate the work of multiple programmers, or to publish work to Github for the world to see. But, one of the first things that I found Git useful for was undoing my mistakes.  And Git's ability to systematically undo changes just might make your life easier, too.
