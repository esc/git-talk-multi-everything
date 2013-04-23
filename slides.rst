Focus
-----

* When doing git training I usually take the following approach:

#. Introduce some basic commands
#. Jump into the deep end

   * (object model, content addressable storage, ...)

#. Explain branches and tags using the directed acyclic graph (DAG)
#. Use this model to reevaluate basic Git commands and explain new ones

   * E.g. ``git rebase``

* This is pretty straightforward
* ... However I have not yet said anything about synchronising with remote repos.

Sources for Confusion
---------------------

* So, committing locally, branching, tagging, maybe even rebase has been
  learnt. Then we introduce ``push`` and ``pull`` which somewhat opens another
  can of worms.

* Oh, I can just use the *push 'n' pull* workflow

  * But wait, why do I get these merge commits? (a.k.a *Diamond Merges*)
  * I thought we were all working on master?

* Oh right, to avoid the diamond merges, I can use ``git pull --rebase``
* ... *rebase* ... right ...

Using the 4 word version of ``git fetch``
-----------------------------------------

* ``git pull`` is ``fetch`` + ``merge``
* "Oh great, so I can just use ``git fetch origin master``?"
* "And what exactly is the ``FETCH_HEAD``?

Remote Tracking Branches
------------------------

* Keeping track of what is going on in the remote

* ``--track`` and ``--set-upstream``: who is tracking what?
* What exactly does ``push.default`` do?
* What exactly does ``branche.<name>.merge`` do?

Stale Branches
--------------

* So we started using short-lived feature branches in a hybrid model, after a
  few weeks I see we have 100+ merged feature branches.

* Delete the feature branches in the remote:

.. code-block::

    $ git push origin --delete <BRANCH>
    $ git push origin :<BRANCH>

* But for others to see the deletion you need ``--prune``

.. code-block::

    $ git fetch --prune
    $ git pull --prune
    $ git remote update --prune

The Github Model
----------------

* The integration manager workflow



Adding a second remote
----------------------

* Throw in a ``-f`` to fetch immediately
* ``git remote add <NAME> -f <URL>``

Showing all remote-tracking branches
------------------------------------

.. code-block:: console

   $ git branch -r
   esc/HEAD -> esc/master
   esc/master
   jjhelmus/master
   pcp13/master
   scipy-lectures/master

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
 $ git ls-rt jjhelmus
 jjhelmus/master



