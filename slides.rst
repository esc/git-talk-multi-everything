Introduction
============

Outline
-------

.. raw:: latex

    \tableofcontents

In the Beginning ...
--------------------

* When doing git training I usually take the following approach:

#. Introduce some basic commands
#. Jump into the deep end

   * (object model, content addressable storage, ...)

#. Explain branches and tags using the directed acyclic graph (DAG)
#. Use this model to reevaluate basic Git commands and explain new ones

   * E.g. ``git rebase``

Directed Acyclic Graph
----------------------

.. image:: images/new_graph.pdf
   :scale: 30

Directed Acyclic Graph -- With Branches and Tags
------------------------------------------------

.. image:: images/new_graph_with_refs.pdf
   :scale: 30

How does it Continue?
---------------------

* This is pretty straightforward
* ... However I have not yet said anything about synchronising with remote repos.


* Often people stumble when trying to understand how remotes are tracked in the
  local Git repository.


* Especially when you have multiple feature branches in multiple remote
  repositories.

* In this talk I will cover possible commands and work-flows for dealing with
  multiple repositories and multiple branches within them

* Heavily biased towards my own experiences and preferences

Sources for Confusion
---------------------

* Initially perhaps people just use the *push 'n' pull* work-flow with a single
  remote ``origin`` and single branch ``master``.

  * But wait, why do I get these merge commits? (a.k.a *Diamond Merges*)
  * I thought we were all working on master?
  * Why aren't we using Branches? I thought they were Git's killer feature.

* To avoid the diamond merges, use ``git pull --rebase``

  * ... *rebase* ... right ...

Using Multiple Branches
=======================

Outline
-------

.. raw:: latex

    \tableofcontents

Multi-Branch Basics
-------------------

* Reminder: Branching (and more importantly: merging) in Git is cheap, quick
  and easy.
* Okay, so let's do that.

.. raw:: latex

   \hspace{1em}

* So what about getting the branches to your remote?
* How do others get those branches locally?

  * To review them?
  * To commit or merge to them?

* What happens when the branches have been merged?

Interlude: Remote Tracking Branches
-----------------------------------

* Reminder: Branches are just *pointers* into the DAG
* Keeping track of what is going on in the remote
* Updated by Git during synchronization:

  .. code-block:: console

      $ git fetch origin
      $ git pull
      $ git remote update

Before ``git clone``
--------------------

.. image:: images/new_clone_before.pdf
   :scale: 20

After ``git clone``
-------------------

.. image:: images/new_clone_after.pdf
   :scale: 20

Pushing Branches
----------------

* Just push the ref:

  .. code-block:: console

     $ git push origin HEAD
     $ git push origin feature

* Better yet, throw in a ``-u`` to set the upstream branch:

  .. code-block:: console

     $ git push origin -u HEAD
     $ git push origin -u feature

Interlude: Upstream-Branch
--------------------------

* Setting:

  * ``branch.<name>.remote``
  * ``branch.<name>.merge``

  .. code-block:: ini

     [branch "master"]
         remote = origin
         merge = refs/heads/master

* Allows ``git pull`` w/o arguments
* Allows ``git push`` w/o arguments

  * If you are using one of for  ``push.default``:

    * ``tracking``
    * ``upstream``
    * ``simple``

  * If you are using Git 2.0, where the default is ``simple``

* Shows you number ahead/behind/diverged in ``git status``

Setting and Querying the Upstream-Branch
----------------------------------------

* Can also be set using ``git branch --set-upstream``
* Query using:

  .. code-block:: console

      $ git config --get-regexp branch.<name>.*
      $ git branch -vv
      $ git remote show <remote> # Tries to connect

Using the Upstream-Branch in Other Commands
-------------------------------------------

* Special syntax: ``<ref>#{upstream}``
* The ``<ref>`` is optional
* Can be shortened to ``@{u}``

  .. code-block:: console

      $ git checkout master
      $ git rev-parse --abbrev-ref --symbolic-full-name @{u}
      origin/master

* Usage examples

  .. code-block:: console

      $ git log @{u}.. # commits not in $REMOTE
      $ git rebase -i @{u} # clean-up before push
      $ git merge --ff @{u} # merge the changes from upstream

Committing to Remote Branches
-----------------------------

* You can't commit directly to remote-tracking branches
* You need to create a local branch and commit to that
* Again the upstream-configuration comes in handy

Some ``git checkout`` Magic
---------------------------

* You can just use ``git checkout``, if the branch name is unambiguous:

  .. code-block:: console

     $ git branch -a
     * master
       remotes/esc/master
       remotes/origin/0.4.X
       remotes/origin/HEAD -> origin/master
     $ git checkout 0.4.X
     Branch 0.4.X set up to track remote branch 0.4.X from origin.
     Switched to a new branch '0.4.X'

Some ``git checkout`` Magic
---------------------------

.. code-block:: console

   $ git branch -a
   * 0.4.X
     master
     remotes/esc/master
     remotes/origin/0.4.X
     remotes/origin/HEAD -> origin/master
   $ git config --get-regexp branch.0.4.X.*
   branch.0.4.X.remote origin
   branch.0.4.X.merge refs/heads/0.4.X

If the Name is Ambiguous...
---------------------------

* ... be explicit about the start commit:

  .. code-block:: console

    $ git checkout -b 0.4.X origin/0.4.X
    Branch 0.4.X set up to track remote branch 0.4.X from origin.
    Switched to a new branch '0.4.X'

Deleting Remote Branches
------------------------

* So we started using short-lived feature branches with a single remote
* After a few weeks I see we have 100+ merged feature branches.

* Delete the feature branches in the remote:

  .. code-block:: console

      $ git push origin --delete $BRANCH
      $ git push origin :$BRANCH

Prune Stale Remote-Tracking Branches
------------------------------------

.. code-block:: irc

    amitpraka: Hi, i have a lot of remotes/origin/X branches
      on local..  however git push origin: X or
      git push origin --delete X doesn't work
      complaining of remote ref does not exist

    amitpraka: How do I get rid of my local remotes/origin/X
      w.o deleting each one individually?

    esc: amitprakash: you could try the --prune
      option for git fetch

    amitpraka: esc, thanks.. that worked

Prune Stale Remote-Tracking Branches
------------------------------------

* For others to see the deletion they need ``--prune``:

  .. code-block:: console

      $ git fetch --prune
      $ git pull --prune
      $ git remote update --prune

Using Multiple Remotes (with Multiple Branches)
===============================================

Outline
-------

.. raw:: latex

    \tableofcontents


The Integration Manager Workflow
--------------------------------

.. image:: images/developer-public-en.pdf
   :scale: 40

Submitting Pull-Requests
------------------------

.. image:: images/github-workflow-en.pdf
   :scale: 40

Adding a Second Remote
----------------------

* Throw in a ``-f`` to fetch immediately:

  .. code-block:: console

    $ git remote add $REMOTE -f $URL

* Showing all remotes:

  .. code-block:: console

    $ git remote -vv


Remote-Tracking Branches for Multiple Remotes
---------------------------------------------

.. image:: images/multi_remote.pdf
   :scale: 18

Showing all remote-tracking branches
------------------------------------

.. code-block:: console

   $ git branch -r
     github/esc/master
     github/esc/feature/cli
     origin/HEAD -> origin/master
     origin/master
     origin/feature/gui


... for only a given remote
---------------------------

.. code-block:: console

 $ git config alias.ls-rt
   !f() {
     git for-each-ref refs /remotes/"$1"
       --format='%(refname)' |
     while read line ;
     do
       echo ${line#refs/remotes/};
     done ;
   } ; f
 $ git ls-rt github/esc
 github/esc/master
 github/esc/feature/cli

Getting Updates
---------------

* There are many ways to synchronize with the remote:

  .. code-block:: console

    $ git fetch $REMOTE     # for a given remote
    $ git pull              # with upstream branch 
    $ git pull $REMOTE $REF # with out
    $ git remote update     # potentially only specific remotes

Getting Updates -- Personal Favorite
------------------------------------

* My personal favorite work-flow is:

  .. code-block:: console

    $ git config alias.fa
    fetch --all --tags --prune
    $ git config alias.fu
    merge --ff @{u}
    $ git fa
    $ git fu

* Why?

  * Allows me to fast forward my branches, no implied merge or rebase

    * My shell prompt shows number ahead/behind/diverged

  * ``--tags`` will update re-written tags for me

    * Yes, I know you aren't supposed to rewrite tags

  * ``--prune`` will prune stale remote-tracking branches

.. Warning: Using the 4 word version of ``git fetch``
.. --------------------------------------------------
.. 
.. * ``git pull`` is ``fetch`` + ``merge``
.. * You could just use:
.. 
..   .. code-block:: console
.. 
..     $ git fetch origin master
.. 
.. * However you need to know what ``FETCH_HEAD`` means

Submitting Feature Branches through Pull-Requests
-------------------------------------------------

* Makes use of `hub <https://github.com/defunkt/hub>`_ -- Github from the command line
* And a special alias ``prune-dev``
* ``origin`` is the place I will submit pull-requests to
* ``esc`` is my fork of ``origin`` on github
* ``@{u}`` for ``master`` is ``origin/master``
* ``co`` is an alias for checkout

Alias ``git prune-dev``
-----------------------

* Figure out which branches have been merged
* Delete those branches
* Also, delete them from the remote repository

  .. code-block:: console

      $ git config alias.prune-dev
        !f(){
          merged=$(git branch --merged |
                   grep -v -e '^*' -e master |
                   xargs) ;
          echo 'merged: '$merged ;
          git branch -d $merged ;
          git push esc --delete $merged ; 
        } ; f

Creating the Pull-Request
-------------------------

* First, setup the branch, make the changes, push and submit the PR:

  .. code-block:: console

    $ git co -b new_feature
    $ vi file ; git add file ; git commit
    $ git push esc -u HEAD # set upstream
    $ hub pull-request # submit pull-request

* Review the comments and address any requests:

  .. code-block:: console

    $ vi file ; git add file ; git commit
    $ git push # use upstream info

Cleaning up After Merge
-----------------------

* Fetch the merge(s) and remove the feature branches:

  .. code-block:: console

    $ git co master
    $ git fa # fetch all
    $ git log --oneline ..@{u} # check the changes
    $ git fu # fast-forward master to merged PR
    $ git prune-dev # prune local branches and remote branches

The Last Slide
--------------

* Everyones work-flow is different
* Use the ideas to customize your own

.. raw:: latex

   \hspace{2em}

* The talk is made with:

  * `rst2beamer <https://github.com/rst2beamer/rst2beamer>`_
  * `ccBeamer <http://blog.hartwork.org/?p=52>`_
  * `LaTeX Beamer <https://bitbucket.org/rivanvx/beamer/wiki/Home>`_
  * `Dia <http://projects.gnome.org/dia/>`_

.. raw:: latex

   \hspace{2em}
   \begin{center}
   Questions?
   \end{center}
