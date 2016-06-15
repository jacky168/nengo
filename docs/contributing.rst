*********************
Contributing to Nengo
*********************

.. default-role:: obj

Developer installation
======================

If you want to change parts of Nengo,
you should do a developer installation.

.. code-block:: bash

   git clone https://github.com/nengo/nengo.git
   cd nengo
   python setup.py develop --user

If you are in a virtual environment, you can omit the ``--user`` flag.

How to run unit tests
=====================

Nengo contains a large test suite, which we run with pytest_.
In order to run these tests, install the packages
required for testing with

.. code-block:: bash

   pip install -r requirements-test.txt

You can confirm that this worked by running the whole test suite with::

  py.test --pyargs nengo

Running individual tests
------------------------

Tests in a specific test file can be run by calling
``py.test`` on that file. For example::

  py.test nengo/tests/test_node.py

will run all the tests in ``test_node.py``.

Individual tests can be run using the ``-k EXPRESSION`` argument. Only tests
that match the given substring expression are run. For example::

  py.test nengo/tests/test_node.py -k test_circular

will run any tests with `test_circular` in the name, in the file
``test_node.py``.

Plotting the results of tests
-----------------------------

Many Nengo tests have the built-in ability to plot test results
for easier debugging. To enable this feature,
pass the ``--plots`` to ``py.test``. For example::

  py.test --plots --pyargs nengo

Plots are placed in ``nengo.simulator.plots`` in whatever directory
``py.test`` is invoked from. You can also set a different directory::

  py.test --plots=path/to/plots --pyargs nengo

Getting help and other options
------------------------------

Information about ``py.test`` usage
and Nengo-specfic options can be found with::

  py.test --pyargs nengo --help

Writing your own tests
----------------------

When writing your own tests, please make use of
custom Nengo `fixtures <http://pytest.org/latest/fixture.html>`_
and `markers <http://pytest.org/latest/example/markers.html>`_
to integrate well with existing tests.
See existing tests for examples, or consult::

  py.test --pyargs nengo --fixtures

and::

  py.test --pyargs nengo --markers

.. _pytest: http://pytest.org/latest/

How to build the documentation
==============================

The documentation is built with Sphinx and has a few requirements.
The Python requirements are found in ``requirements-test.txt``
and ``requirements-docs.txt``, and can be installed with ``pip``:

.. code-block:: bash

   pip install -r requirements-test.txt
   pip install -r requirements-docs.txt

However, one additional requirement for building the IPython notebooks
that we include in the documentation is Pandoc_.
If you use a package manager (e.g., Homebrew, ``apt``)
you should be able to install Pandoc_ through your package manager.
Otherwise, see
`this page <http://johnmacfarlane.net/pandoc/installing.html>`_
for instructions.

After you've installed all the requirements,
run the following command from the root directory of ``nengo``
to build the documentation.
It will take a few minutes, as all examples are run
as part of the documentation building process.

.. code-block:: bash

   python setup.py build_sphinx

.. _Pandoc: http://johnmacfarlane.net/pandoc/

Code style
==========

We adhere to
`PEP8 <http://www.python.org/dev/peps/pep-0008/>`_,
and use ``flake8`` to automatically check for adherence on all commits.
If you want to run this yourself,
then ``pip install flake8`` and run

.. code-block:: bash

   flake8 nengo

in the ``nengo`` repository you cloned.

Class member order
------------------

In general, we stick to the following order for members of Python classes.

1. Class-level member variables (e.g., ``nengo.Ensemble.probeable``).
2. Parameters (i.e., classes derived from `nengo.params.Parameter`)
   with the parameters in ``__init__`` going first in that order,
   then parameters that don't appear in ``__init__`` in alphabetical order.
   All these parameters should appear in the Parameters section of the docstring
   in the same order.
3. ``__init__``
4. Other special (``__x__``) methods in alphabetical order,
   except when a grouping is more natural
   (e.g., ``__getstate__`` and ``__setstate__``).
5. ``@property`` properties in alphabetical order.
6. ``@staticmethod`` methods in alphabetical order.
7. ``@classmethod`` methods in alphabetical order.
8. Methods in alphabetical order.

"Hidden" versions of the above (i.e., anything starting with an underscore)
should either be placed right after they're first used,
or at the end of the class.
Also consider converting long hidden methods
to functions placed in the ``nengo.utils`` module.

.. note:: These are guidelines that should be used in general,
          not strict rules.
          If there is a good reason to group differently,
          then feel free to do so, but please explain
          your reasoning in code comments or commit notes.

Docstrings
----------

We use ``numpydoc`` and
`NumPy's guidelines for docstrings
<https://github.com/numpy/numpy/blob/master/doc/HOWTO_DOCUMENT.rst.txt>`_,
as they are readable in plain text and when rendered with Sphinx.

We use the default role of ``obj`` in documentation,
so any strings placed in backticks in docstrings
will be cross-referenced properly if they
unambiguously refer to something in the Nengo documentation.
See `Cross-referencing syntax
<http://www.sphinx-doc.org/en/stable/markup/inline.html#cross-referencing-syntax>`_
and the `Python domain
<http://www.sphinx-doc.org/en/stable/domains.html>`_
for more information.

A few additional conventions that we have settled on:

1. Default values for parameters should be specified next to the type.
   For example::

     radius : float, optional (Default: 1.0)
         The representational radius of the ensemble.

2. Types should not be cross-referenced in the parameter list,
   but can be cross-referenced in the description of that parameter.
   For example::

     solver : Solver
         A `.Solver` used in the build process.

Git workflow
============

Development happens on `Github <https://github.com/nengo/nengo>`_.
Feel free to fork any of our repositories and send a pull request!
However, note that we ask contributors to sign
:ref:`an assignment agreement <caa>`.

Rules
-----

We use a pretty strict ``git`` workflow
to ensure that the history of the ``master`` branch
is clean and readable.

1. Every commit in the ``master`` branch should pass testing,
   including static checks like ``flake8`` and ``pylint``.
2. Commit messages must follow guidelines (see below).
3. Developers should never edit code on the ``master`` branch.
   When changing code, create a new topic branch for your contribution.
   When your branch is ready to be reviewed,
   push it to Github and create a pull request.
4. Pull requests must be reviewed by at least two people before merging.
   There may be a fair bit of back and forth before
   the pull request is accepted.
5. Pull requests cannot be merged by the creator of the pull request.
6. Only `maintainers <https://github.com/orgs/nengo/teams/nengo-maintainers>`_,
   can merge pull requests to ensure that the history remains clean.

Commit messages
---------------

We use several advanced ``git`` features that
rely on well-formed commit messages.
Commit messages should fit the following template.

.. code-block:: none

   Capitalized, short (50 chars or less) summary

   More detailed body text, if necessary.  Wrap it to around 72 characters.
   The blank line separating the summary from the body is critical.

   Paragraphs must be separated by a blank line.

   - Bullet points are okay, too.
   - Typically a hyphen or asterisk is used for the bullet, followed by
     single space, with blank lines before and after the list.
   - Use a hanging indent if the bullet point is longer than a
     single line (like in this point).

Reviewing
=========

Nengo is developed by a community of developers
with varying backgrounds in software development,
neuroscience, machine learning, and many other areas.
We rely on each other to review our work
and ensure that our code is
correct, consistent, and documented.
Every Nengo pull request (PR) is reviewed by two people.
Any Nengo developer can do a review,
and anyone who has had a PR accepted into Nengo
is a Nengo developer.

Here are the steps developers should take when doing a review.

1. Assign yourself to the PR to let others know you're reviewing it.
2. Familiarize yourself with the part of the codebase that the PR changes.
3. Read through the code changes.
4. Test the PR branch.
5. Make inline comments for minor changes.
6. Make FIXUP commits for other changes.
7. Make a final decision on the PR.

More details on some of these steps below.

Reading code diffs
------------------

Reading through code diffs is a skill that takes a fair bit
of practice -- but the only way to practice is by doing it!
A useful exercise when starting out is to read the code diffs
for many PRs even if you don't plan to review that PR.

There are two ways to read code diffs.

1. Read the diff of the entire PR.
   In Github, this is found in the "Files changed" tab of the PR.
   This works best for quick and average PRs
   that change only one area of the codebase.
2. Read the diff commit-by-commit.
   In Github, this is found in the "Commits" tabs of the PR.
   There are links in each commit to the previous and next commits
   to make reading the diff easier.
   This works best for lengthy PRs,
   and is made easier when the PR author keeps related changes
   in the same commit.

In general, if looking at the diff of the entire PR is difficult,
then switch to reading the diff commit-by-commit.
If reading the diff commit-by-commit is difficult,
then ask the PR author to make the history of the PR easier to read.

In reading through the code diff,
you should be sensitive to both what the code does,
and how it does it.
If you think you can express the same logic
with less code and/or in a more obvious way,
then please propose the change as described below.

Testing the PR branch
---------------------

In general, we rely on the test suite to ensure that
the code introduced in a PR is correct
(i.e., works as intended, doesn't break people's models).
For new features, the PR should include tests
to ensure the new code is correct.
For bugfixes, the PR should include a test that fails
without the changes in the PR.
For refactorings, optimizations, and other improvements
that do not fix bugs or add new features,
existing tests should cover the new code.
In all of these cases,
run the test suite locally to ensure that the tests pass.

If the change does not have tests,
follow the manual testing steps in the PR description.
If no manual testing steps are specified,
then ask the PR author for testing steps.

Making inline comments
----------------------

Github allows you to make comments
on specific lines of a diff.
You can do this in both ways of reading through diffs
described above (all at once, or commit-by-commit).

Inline commits should be used for questions and minor changes only.
Good uses of inline comments include:

- Asking for clarification of what some small chunk of code does.
- Asking for the reasoning behind some code choice.
- Pointing out a typo.
- Pointing out a possible style improvement.

A bad use of an inline comment is to ask for
a major change to the PR.
These comments tend to be
frustrating for PR authors to respond to,
and in general are unbalanced in terms of reviewer effort
versus PR author effort.
For major changes, instead make a FIXUP commit.

Inline comments should not block the merging of a PR.
The maintainer merging the PR will make the typo / style fixes
they deem appropriate during the merge
if the PR author doesn't get around to fixing them.
If the discussion raises a new issue or feature request,
make a new issue to track that so that it doesn't
block PR progress.

Making FIXUP commits
--------------------

Instead of asking the PR author for changes,
we prefer reviewers to make FIXUP or SQUASH commits
to propose changes to a PR.
FIXUP commits allow the reviewer to propose explicit
changes that the PR author can say yes or no to,
rather than placing the burden on the PR author
and allowing for miscommunication.

FIXUP commits are so named because
the maintainer will FIXUP those commits into
the appropriate part of the PR branch's history
before merging that branch into ``master``.
Though not used as often,
you can also make SQUASH commits,
which are like FIXUP commits,
but contain some useful information in the commit message
which should be incorporated in the final commit message.
See the `Git book's rewriting history chapter
<https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History>`_
for more information.

Note that FIXUP commits are suggested changes.
The PR author may object to them,
at which point it is up to the other reviewer
to reach a consensus about what is best.
If a consensus cannot be reached,
then the PR may have to be closed.

TODO: choose between:
- in the commit history (obvious where it should go)
- at the very end (don't have to force push)

Making a final decision
-----------------------

In order to shorten the amount of back-and-forth
in a given PR,
we ask that reviewers make a decision about the PR
and post that decision as a comment on the PR
after making inline comments and FIXUP commits.

Your decision should be one of the following:

1. This PR is good to merge, or will be good to merge with my changes.
2. This PR could be good to merge, but it requires significant changes
   that I am working on.
3. This PR is not apporpriate for this project.

Note that the third option should not be taken lightly,
but is necessary for the long-term success of a project.
A PR left open too long is worse than a PR that is
closed with a good reason and a clear next step.
Never close a pull request without giving a reason
and a next step for the PR author.

Here are some good reasons for closing a PR,
with next steps.

1. This PR adds something that we do not think will be
   used frequently, or duplicates existing functionality.
   Please consider submitting this PR to
   `nengo_extras <https://github.com/nengo/nengo_extras>`_,
   another suitable place,
   or make a separate repository for it and let us know
   about that repository.
2. This PR has some unresolved issues that have not been addressed
   in a reasonable amount of time.
   We would still like the changes in this PR,
   so please address our comments and make a new PR
   with those changes included.
3. This PR causes tests to fail, and it's not clear
   how to make the tests pass again.
   Please get the tests to pass and resubmit this PR.
   We are happy to help if parts of the code aren't clear!

This is by no means an exhaustive list,
and PRs adding to this list are appreciated!
For a longer discussion about
the art of closing PRs,
see `this blog post <https://blog.jessfraz.com/post/the-art-of-closing/>`_.

Getting help
============

If you have any questions about developing Nengo
or how you can best climb the learning curve
that Nengo and ``git`` present, please
`file an issue <https://github.com/nengo/nengo/issues/new>`_
and we'll do our best to help you!
