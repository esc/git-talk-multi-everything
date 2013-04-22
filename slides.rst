Focus
-----

* When doing git training i usually take the following approach:

#. Introduce some basic commands
#. Jump into the deep end (object model, content addressable storage, ...)
#. Explain branches and tags using the Directed Acyclic Graph
#. Use this model to reevaluate basic git commands and explain new ones
#. E.g. ``git rebase``

* This is pretty straightforward
* However I have not yet said anything about synchronising with remote repos.

Sources for Confusion
---------------------

* So; committing locally, branching, tagging, maybe even rebase has been
  learnt. Then we introduce ``push`` and ``pull`` which somewhat opens another
  can of worms.

* Oh, I can just use the *push 'n' pull* workflow

  * But wait, why do I get these merge commits, I thought we were all working
    on master?
  * So how 

* ``git pull`` is ``fetch`` + ``merge``
* "Oh great, so I can just use ``git fetch``?"
* "And what exactly is the ``FETCH_HEAD``?
* "Remote-tracking-branches, what a mouthfull!"
* Wait, you mean I can't commit to remote-tracking-branches, but need an extra local
  branch to commit to them?
* ``--track`` and ``--set-upstream`` who is tracking what? 
* What exactly does ``push.default`` do?

* So we use feature branches in a hybrid model, after a few weeks I see we
  have 100+ merged feature branches.

  * Okay great so I learnt about ``:FEATURE_BRANCH`` and/or ``--delete``, but
    my co-worker.
