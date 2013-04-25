Introduction
============

Outline
-------

.. raw:: latex

    \tableofcontents

In The Beginning ...
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

Sources for Confusion
---------------------

* Initially perhaps people just use the *push 'n' pull* workflow with a single
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

Multi-Branch basics
-------------------

* Reminder: Branching (and more importantly: merging) in Git is cheap, quick
  and easy.
* Okay, so let's do that.

* So what about getting the branches to your remote?
* How do others get those branches locally?

  * To review them?
  * To commit or merge to them?

Interlude: Remote Tracking Branches
-----------------------------------

* Reminder: Branches are just *pointers* into the DAG
* Remote-tracking
* Keeping track of what is going on in the remote
* Updated by Git during synchronization

  * (via ``git fetch origin``, ``git pull``, ``git remote update``, ...)

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

Just push the ref:

.. code-block:: console

   $ git push origin HEAD
   $ git push origin fix/setup

Better jet, throw in a ``-u`` to set the upstream branch:

.. code-block:: console

   $ git push origin -u HEAD
   $ git push origin -u fix/setup

Interlude: Upstream-Branch
--------------------------

* Setting

  * ``branch.<name>.merge``
  * ``branch.<name>.remote``

.. code-block:: ini

   [branch "master"]
       remote = origin
       merge = refs/heads/master

* Allows ``git pull`` w/o arguments
* Allows ``git push`` w/o arguments

  * If you are using one of for  ``push.default``

    * ``tracking``
    * ``upstream``
    * ``simple``

  * If you are using Git 2.0, where the default is ``simple``

Setting and Querying the Upstream-Branch
----------------------------------------

* Can also be set using ``git branch --set-upstream``
* Query using

  * ``git config --get-regexp branch.<name>.*``
  * ``git branch -vv``
  * ``git remote show <remote>`` (Tries to connect)


The Integration Manager Workflow
--------------------------------

.. image:: images/developer-public-en.pdf
   :scale: 40

Submitting Pull-Requests
------------------------

.. image:: images/github-workflow-en.pdf
   :scale: 40

Using the 4 word version of ``git fetch``
-----------------------------------------

* ``git pull`` is ``fetch`` + ``merge``
* "Oh great, so I can just use ``git fetch origin master``?"
* "And what exactly is the ``FETCH_HEAD``?

Remote Tracking Branches
------------------------

* ``--track`` and ``--set-upstream``: who is tracking what?
* What exactly does ``push.default`` do?

Stale Branches
--------------

* So we started using short-lived feature branches in a hybrid model, after a
  few weeks I see we have 100+ merged feature branches.

* Delete the feature branches in the remote:

.. code-block:: console

    $ git push origin --delete <BRANCH>
    $ git push origin :<BRANCH>

* But for others to see the deletion you need ``--prune``

.. code-block:: console

    $ git fetch --prune
    $ git pull --prune
    $ git remote update --prune

A quote
-------

.. code-block:: irc

    « amitpraka» Hi, i have a lot of remotes/origin/X branches
    on local..  however git push origin: X or
    git push origin --delete X doesn't work
    complaining of remote ref does not exist
    « amitpraka» How do I get rid of my local remotes/origin/X
    w.o deleting each one individually?
    «       esc» amitprakash: you could try the --prune
    option for git fetch
    « amitpraka» esc, thanks.. that worked

The Github Model
----------------

* The integration manager workflow



Adding a second remote
----------------------

* Throw in a ``-f`` to fetch immediately
* ``git remote add <NAME> -f <URL>``

Remote-Tracking Branches for multiple remotes
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
 $ git ls-rt esc
 github/esc/master
 github/esc/feature/cli



