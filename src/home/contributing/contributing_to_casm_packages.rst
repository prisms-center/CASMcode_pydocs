.. include:: ../_shared/logo_and_spacing.rst

.. _contributing-to-casm-packages:

Contributing to *casm* Packages
===============================

Collaboration is welcome, and new features can be incorporated by forking one of the *casm* repositories on GitHub, creating a bug fix or new feature, and submitting a pull request. If you are interested in developing features that involve a significant time investment, we encourage you to first contact the CASM development team at :email:`casm-developers@lists.engr.ucsb.edu`.

Pull requests should:

- Create a branch from the development branch for new features (e.g., *2.X*) and name it to indicate that it implements a new feature (e.g., *2.X-myfeature*) or a bug fix (e.g., *2.0.0-patch-issue*).
- Propose a minimal set of changes.
- Have code formatted and documented as described below.
- Include appropriate tests.
- Pass all CI tests.
- Include a suggested *CHANGELOG.md* entry. See `keepachangelog.com <https://keepachangelog.com>`_.

Layout
------

Repository layout
^^^^^^^^^^^^^^^^^

The repository for each *casm* distribution package is organized as follows:

- *casm/<name>/*: Python namespace packages
- *tests/<name>/*: Python tests
- *doc/*: Python documentation


Installation layout
^^^^^^^^^^^^^^^^^^^

When the project is built and installed, components are added to the Python installation location (i.e. *<python package prefix> = <something>/lib/pythonX.Y/sites-packages/*) in the *libcasm/* folder at the following locations:

*<python package prefix>/casm/*:

- *<name>/*: *casm* Python namespace packages


Installing from source
----------------------

Installation of *casm* distribution packages from source requires Python >= 3.9.

The CASM package and its dependencies can be installed with:

.. code-block:: bash

    pip install .

Building documentation
----------------------

Install documentation requirements:

.. code-block:: bash

    pip install -r doc_requirements.txt

Clone `CASMcode_pydocs <https://github.com/prisms-center/CASMcode_pydocs>`_, then set an environment variable indicating where to store the docs:

.. code-block:: bash

    mkdir <path-to-pydocs>/docs/casm
    export LIBCASM_PYDOCS=<path-to-pydocs>/docs/casm

Install the *casm* package first, then build and open the documentation:

.. code-block:: bash

    cd python/doc
    # In the following, replace:
    # - <package> with the distribution package name
    # - <vers> with the major.minor version number
    # Example: <package>=xtal, <vers>=2.0
    sphinx-build -b html . $CASM_PYDOCS/<package>/<vers>/
    open $CASM_PYDOCS/<package>/<vers>/index.html

Testing
-------

To install testing requirements, do:

.. code-block:: bash

    pip install -r test_requirements.txt

Use *pytest* to run the tests. To run all tests, do:

.. code-block:: bash

    pytest -rsap tests

As an example of running a specific test, do:

.. code-block:: bash

    pytest -rsap tests/<filepath>::<function_name>

.. _contributing-to-casm-packages-python-development:

Python development
------------------

To install formatting requirements, do:

.. code-block:: bash

    pip install -r dev_requirements.txt


Python linting
^^^^^^^^^^^^^^

For Python code linting, use `ruff <https://beta.ruff.rs/docs/>`_. For example:

.. code-block:: bash

    ruff check --fix casm tests


Python formatting
^^^^^^^^^^^^^^^^^

For Python code formatting, use `black <https://black.readthedocs.io>`_. For example:

.. code-block:: bash

    black casm tests

Python documentation
^^^^^^^^^^^^^^^^^^^^

Please follow the following conventions:

- When in doubt about styling, refer to common practices as found in `numpydoc <https://numpydoc.readthedocs.io/en/latest/format.html>`_, `pandas <https://pandas.pydata.org/docs/development/contributing_documentation.html>`_, or `scikit-learn <https://scikit-learn.org/dev/developers/contributing.html#documentation>`_.
- Refer to programs, packages, files, paths, arguments, and variables using *italics*. For example, when referring to constructor arguments or function variables in docstring text, italicize them like:

  .. code-block::

      Specifies clusters that should be uses to construct orbits regardless of the
        `max_length` or `cutoff_radius` parameters.

- Refer to code snippets (including setting arguments or variables like ``x=3``), commands, command line options, and environment variables using ``code blocks``. For example, when describing that a variable has a particular value or how it is used in a code snippet, then use either inline code:

  .. code-block::

      ... using ``max_length=[0, 0, 6.5, 6.0, 4.5]`` ...

  or use a code block:

  .. code-block::

      ... using:

      .. code-block:: Python

          max_length = 6.5

      ...

- Make use of :literal:`.. rubric:: Special Methods` to create a section in a class docstring to document any special members of a class, such as comparison operators (*<*, *<=*, *>*, *>=*, etc.) or arithmetic operators (`*`, `*=`, `+`, `+=`, `-`, `-=`, etc.).


Adding tests
^^^^^^^^^^^^

- Add Python tests for *casm.<subpackage>* in *python/tests/<subpackage>*, using pytest.
- If data files are needed for testing, they can be placed in *python/tests/<subpackage>/data/*.
- To access data files use the *shared_datadir* fixture available from the `pytest-datadir <https://pypi.org/project/pytest-datadir/>`_ plugin.
- To create temporary testing directories for reading and writing files, use the `tmpdir and tmpdir_factory <https://docs.pytest.org/en/7.4.x/how-to/tmp_path.html#the-tmpdir-and-tmpdir-factory-fixtures>`_ fixtures available from pytest.
- For tests that involve an expensive setup process, such as compiling Clexulators, a session-length shared datadir can be constructed once and re-used as done `here <https://github.com/prisms-center/CASMcode_clexulator/blob/main/python/tests/clexulator/conftest.py>`_ in CASMcode_clexulator.
- Expensive tests can also be set to run optionally using flags as demonstrated in CASMcode_clexulator.


Adding dependencies
^^^^^^^^^^^^^^^^^^^^

Dependencies should be added thoughtfully, balancing the need for functionality with the goal of keeping the package easy to install and maintain. If a dependency is only required for building, testing, or documentation, add it to *build_requirements.txt*, *test_requirements.txt*, or *doc_requirements.txt* instead of adding it as dependency.

Release process
----------------

Packages are built into pure Python wheels by GitHub Actions. For an example, see `build.yml <https://github.com/prisms-center/CASMcode_bset/blob/main/.github/workflows/build.yml>`_ in the *casm-bset* package.

Assume that the *casm* package repository git remote is called *origin*, that it has a stable *main* branch, a development branch called *2.X*. Then the release process is as follows:

1. Merge new features and changes into the development branch, i.e. 2.X

   1. Remember to update CHANGELOG.md along with the changes
   2. Breaking changes to the Python package API should only be done in a new major version, i.e. 2.X -> 3.X.
   3. New features and non-breaking changes to the Python package API can go into the current development branch, i.e. 2.X

2. When the changes to be included in a new release are ready, create a release branch: 2.X -> 2.0.0

   1. New features and changes should get a minor version bump, i.e. 2.0.Y -> 2.1.0
   2. Bug fixes should get a patch version bump, i.e. 2.0.Y -> 2.0.Z

3. Update the version in all the places that apply, which may include:

   1. *pyproject.toml*
   2. *setup.py*
   3. *doc/conf.py*
   4. *CHANGELOG.md* (add below unreleased, and set date)

5. Commit changes in (3) and (4), and push to GitHub. Check that all tests pass, making fixes on the release branch if necessary.

6. Once all tests pass and the version updates have been made, create a PyPI release:

   1. Make a directory for the built wheel and source distribution:

      .. code-block:: bash

          mkdir dist/2.0.0

   2. Download built wheel and source distribution from the *build.yml* GitHub Action artifacts to *dist/2.0.0*
   3. Then upload the built wheel and source distribution to PyPI:

      .. code-block:: bash

          python -m twine upload dist/2.0.0/*

7. Create a tag for the release. For release branch 2.0.0, use v2.0.0:

   .. code-block:: bash

      git tag v2.0.0
      git push origin v2.0.0

8. Merge the release branch into main: 2.0.0 -> main
9. Merge main into the development branch: main -> 2.X
10. Create a github release: v2.0.0

    1. Create a new release
    2. Describe the release using the *CHANGELOG.md* entry
    3. Attach the source distribution
    4. Publish

11. Build docs and add to CASMcode_pydocs:

    .. code-block:: bash

       export LIBCASM_PYDOCS=<path/to/pydocs>/docs/libcasm
       cd python/doc
       sphinx-build -b html . $LIBCASM_PYDOCS/<package>/2.0/
       cd <path/to/pydocs>
       git add -u
       git commit -m "update global/2.0 docs to 2.0.X"
       git push




