#+TITLE:     Git Reference Card
#+AUTHOR:    Fabrice Niessen
#+EMAIL:     (concat "fniessen" at-sign "pirilampo.org")
#+DESCRIPTION:
#+KEYWORDS:
#+LANGUAGE:  en

#+SETUPFILE: ~/org/setup/html-theme-readtheorg.setup

* About Git

- [[http://cheat.errtheblog.com/s/git][Git cheat sheet]]
- [[http://zrusin.blogspot.be/2007/09/git-cheat-sheet.html][Git cheat sheet]] -- a reference to everyday commands
- [[http://gitref.org/][Git reference site]] -- a quick way to learn and remember git commands
- Git community book is meant to help you learn how to use Git as quickly and
  easily as possible
- http://fr.slideshare.net/lemiorhan/git-branching-mode
- http://git.or.cz/course/svn.html
- http://www-cs-students.stanford.edu/~blynn/gitmagic/
- [[http://www.youtube.com/watch?v%3D4XpnKHJAok8][Tech Talk: Linus Torvalds on git]]
- Presentation of git concepts : http://gitolite.com/gcs.html

The [[http://git-scm.com/book/en/Git-Branching-Rebasing][git-scm]] site below is a great resource for using Git.

** Understanding the Git Workflow

> I thought this was an interesting read:
>
>   http://sandofsky.com/blog/git-workflow.html

Nice read, I thought cleaning up before merge is good indeed.

Suppose a beginner like me who want to contribute to a bigger projects like
org, wouldn't want put all his stupid, do/undo commits in the org main
repository.

So cleaning up(rewriting history) of his own branch with good commit messages
and annotations would help maintainers to review the code. That also gives
more sane look for someone who is coming back to see the commits years later.

** Comparing Git workflows

https://www.atlassian.com/git/tutorials/comparing-workflows

- Centralized workflow
- Feature branch workflow
- Gitflow workflow
- Forking workflow

** Réécrire l'historique

http://git-scm.com/book/fr/Utilitaires-Git-R%C3%A9%C3%A9crire-l-historique

** Branches

In the famous "Pro Git":

[...]
  Many Git developers have a workflow that embraces this approach, such
  as having only code that is entirely stable in their `master' branch --
  possibly only code that has been or will be released. They have
  another parallel branch named develop or next that they work from or
  use to test stability -- it isn't necessarily always stable, but
  whenever it gets to a stable state, it can be merged into `master'.
[...]
  You can keep doing this for several levels of stability. Some larger
  projects also have a `proposed' or `pu' (proposed updates) branch that
  has integrated branches that may not be ready to go into the `next' or
  `master' branch. The idea is that your branches are at various levels
  of stability; when they reach a more stable level, they're merged into
  the branch above them.

** Other

> The easiest way for me to apply that would be if you would create a git
> commit, then run "git format-patch origin/master" and mail the resulting
> files, (the "git send-email" command can be used here, or you can insert
> the files into a mail-composition buffer and modify them as needed).

http://blog.printf.net/articles/2010/10/04/git-patches-in-gnus

http://mwolson.org/bzr/dvc/lisp/xgit-gnus.el

http://www.koders.com/lisp/fid108D7D608671C4928AB9F68D121AE86C7CF4BEA9.aspx?s=woman

http://kerneltrap.org/mailarchive/git/2008/6/22/2197274

Run =git gc= from time to time to recover some disk space

** O

My workflow with feature branches is create the branch; do the work;
when it's almost done, merge from master and test; then merge back
into master and push.  There's no pulling here except from upstream to
master.

> For longer term work that's kept in feature branches I prefer to
> rebase on top of upstream rather than merge and usually do a full
> rewrite before pushing it upstream as well.

Well, I prefer merging instead, see above.

* Git rebase

http://www.jarrodspillers.com/2009/08/19/git-merge-vs-git-rebase-avoiding-rebase-hell/

>> Well, he could just say "git pull --rebase", right?
>
> That doesn't clean up the intermediate commits, it just transplants
> them.

And in more detail, it effectively

  1) runs git fetch to update the local copy of the relevant upstream
     branch,

  2) removes all of your pending commits from the current branch,

  3) moves the current branch forward to match the local upstream copy
     that was just fetched,

  4) and then attempts to reapply all of the patches that were removed
     in step 2 to the updated current branch (i.e. the rebase).

If anything goes wrong with a patch in step 4, you'll be walked through
a resolution process (as with all rebases).  And at any point, if you
want to start over, you can run "git rebase --abort".

* Git Log

** Git grep

See
http://stackoverflow.com/questions/2928584/how-to-grep-search-committed-code-in-the-git-history

and these:

With =git log -G <regexp>=, the commit is shown in the log if your search
matches any line that was added, removed, or changed.

Find when some code was removed?  =git bisect= with a "test" script that greps
for the code and returns "true" if the code exists and "false" if it does
not.

* Notes

For the ~--fixes~ replacement, I liked the idea of using notes, as per
http://git-scm.com/blog/2010/08/25/notes.html

#+begin_src sh
  git notes --ref=bugzilla add -m 'bug #15' 0385bcc3
  git log --show-notes=bugzilla
#+end_src

* Revert

** Come back to last known good version

> fwiw, I updated via git this morning and entered the same problem with
> the same error message. I'm swamped and didn't have time to debug
> further (though I did check list-load-path-shadows and didn't see a
> problem there).

That's what Git is for:

#+begin_src sh
git reflog -5 HEAD
#+end_src

will show you where your branch head was for the last five operations
that moved it.  If you want to go back 2 steps you'd say

#+begin_src sh
git checkout HEAD@{2}
#+end_src

for instance.  No need to deal with pesky tar files.

* Apply patch against specific commit

Apply patch against current db51b8 commit in master.

#+begin_src sh
git checkout db51b8 -b crude
git am /tmp/0001-Crude-patch-to-simplify-dependencies.patch
#+end_src

* Apply patches

Using ~git format-patch~ and ~git am~, rather than ~git diff~ and ~git apply~.

* Git Diff

I want the diff between the head of "foo" and the common ancestor between "foo"
and "master".

> Do you mean "git diff $(git merge-base HEAD master)"?

A diff between master's and foo's common ancestor and the tip of foo:

    git diff master...foo           # Three dots.

In other words, it's is a diff between A and B in the graph below.

    o---o---A---o---o----o master
             \
              \--o---o---B foo

* Git workflow

I don't know if this will help, but personally -- with git, in most
cases I only have one (or very few) clones, and I just switch branches
(a *lot*).

For example, if I were on say tmp/fix-foo, and I wanted to switch to
work on master, but I still had local changes that I wanted to come back
to later, I'd probably either use stash, or a trivial commit.

For example (via stash):

  $ git stash 'stuff I want to deal with later'
  $ git checkout master
  ... do whatever...
  $ git checkout tmp/fix-foo
  $ git stash pop

And now I'm exactly where I was (plus or minus any build detritus that
differs between the two branches.).

Though often it's more convenient to use an actual dummy commit since
git's stash is somewhat "one dimensional".  Here's the same thing via a
temporary commit:

  $ git commit -am 'stuff I want to deal with later'
  $ git checkout master
  ... do whatever...
  $ git checkout tmp/fix-foo
  $ git reset HEAD^  # "Undoes" the top commit, putting the changes back on disk.

And I'm again back where I was.

Some caveats:

  * If you have any new files, you'll need to "git add ..."  them before
    the "commit -am" or stash above, otherwise they'll be left out (and
    also be left alone in the current working directory).

Another item in the category of "knowing what's going on" -- the fancy
git prompt component that Debian (at least) provides by default, i.e.:

      GIT_SHOW_DIRTYSTATE=true
      GIT_SHOW_STASHSTATE=true
      PS1='...$(__git_ps1)...'

Finally, I'd definitely recommend eventually learning "git rebase
--interactive ..." *for local use*.  I find it extremely useful.

(Note: I didn't talk about magit because I'm not using it heavily yet,
 but it's probably useful to understand some of the command line
 operations regardless.)

> I just noticed that I cluttered the git tree with my last commit.  All
> my intermediate commits which were supposed to be visible only to me
> suddenly appeared in the public repository.  Sorry!
>
> I'll study some tutorials before the next commit.  Promised.

The only way _not_ to have intermediate commits be visible (short of
creating a diff and applying it as a single commit) is to prettify your
branch before merging.  git rebase -i is useful for that.

** Ledger

John and I agreed to follow the git-flowน model for development, which
means the master branch will only see updates when a new version is
released as of now and development will happen on the next branch, so
please make pull request against next from now on.

Answer:

Thanks for all your hard work, Alexis!  The only thing I wanted to mention
about git-flow, and the reason it was abandoned previously: is that releases
were far and few between, and so master was stagnating and everyone was
starting to use the next branch directly anyway (thus next became the new
master).

So I think that if we want git-flow to be successful for us, we should be
committed to fairly frequent releases on the master branch, which may likely
mean we move to a four-point version number and do point releases on a weekly
or semiweekly basis.  How does that sound?  Essentially, any time you have a
build which passes all tests and you think is sane, it's a candidate for a
mini-release to master.

Next answer:

Yes, I understand that, what I mean is perhaps as we move along during the
development of 3.1.1, we also put out quick releases of 3.1.1.1, 3.1.1.2,
etc.  These wouldn't necessitate creating a new release branch, but would
rather be interim merges of release/3.1.1 into master, plus a tag.

This way, people consuming from master don't have to wait very long.  My only
fear with git-flow is that if master gets really slow (say, a release every
few months), either people will think the project has stagnated, or everyone
will switch to cloning from 'next', and then our work to maintain the
integrity of 'master' won't mean a whole lot (which is what happened last
time).  We need to keep all the branches relevant to the users who track them.

* Git log

** O

> git log is being piped into less, which is objectionable.  If I want it
> in less, I'm quite capable of saying so.  Is there a flag I can give to
> stop my stdout being hijacked, or can I configure it away somehow?

As soon as you redirect it won't be piped into less.  But you can either
specify the global git option --no-pager, e.g.,

  git --no-pager log

or configure

  git config --global core.pager cat

But with that, you'll always see Jim Blandy's commit from 1985 in "git
log" and then have to scroll up some million lines...

** O

> But as I understand it, the head of each branch is identified by a
> pointer, and all previous commits are in a chain starting at this
> pointer.  So git should know which branch each commit is on.  Why can it
> not display this information?

git log --source --all

** O

> A branch is nothing more than a label in Git, all you really need to
> know is the SHA1 for the branch head to (re-)create it.

Not really: you need the objects behind it as well.  When deleting a
branch, the object store of Git keeps them around for a few weeks and
will clean them out then on the next garbage collection.  If you
recreate the branch head any time before that, this will work without
problem.  If you recreate it afterwards, the repository will be
incomplete.  Fetching branches is more reliable than creating references
and hoping that the objects will happen to be around.

* Branches and master

After having repeated moments of tension with a non-workable master,
LilyPond moved to a different setup.  "Check that everything builds,
including the documentation" takes more than an hour on my system, a
moderately up-to-date dual processor machine.  We have contributors with
considerably less machine power.  We have a few volunteers with
considerably more.

Prospective patches are sent to a tracker.  Volunteers regularly pick
them up with an automated procedure, run all regtests and view the
flagged visual differences in the regression tests and logs and
(weighted and thresholded) memory usage statistics.  The last step of
viewing the differences is manual and making a comment/judgment is
manual, the rest automatic.  So all patches have seen testing against
_some_ version of LilyPond.

When committing any commit, it is committed to a branch called
"staging".  *NO HUMAN COMMITS TO MASTER.*  staging is picked up regularly
by an automated process, compiled, regtest-checked (without visual
comparison), documentation and translations built (includes all the web
sites).  If compilation completes successfully, the result is pushed to
master.

This automated process can fail for a number of reasons.  Mails get sent
out without stopping the scheduled attempts.

Those reasons are: failure (of course).
master cannot be fast-forwarded to staging (some human pushed to master,
or a staging test run on another machine with a conflicting staging
branch completed first): may need manual fixing
the tested staging is not strictly behind the upstream staging at the
time testing completes: that means that somebody cleared staging and
replaced it by something else while testing progressed: an emergency
stop if you find you pushed something bad and noticed immediately: in
that case resetting staging is enough to stop the automated run from
pushing the no-longer-accepted version of staging even if it tests fine.

The "who messed up master?" panics have gone.  Actually, the frequency
of messups overall has not been a lot recently, but "staging is messed
up" is a comparatively harmless problem.  It halts the queue.  It's only
messy when you have a lot of different contributions queued in staging
and one is bad.  However, the frequency of the automated runs make this
unlikely, and you can always wait for the queue to clear a bit if you
are not really sure your change is good and want to save others from
having to recommit and/or substitute a rebased version of staging with
your bad commits removed.

