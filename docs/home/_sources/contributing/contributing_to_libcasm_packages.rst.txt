.. include:: ../_shared/logo_and_spacing.rst

.. _contributing-to-libcasm-packages:

Contributing to *libcasm* Packages
==================================

Collaboration is welcome and new features can be incorporated by forking one of the *libcasm* repositories on GitHub, creating a bug fix or new feature, and submitting a pull request. If you are interested in developing features that involve a significant time investment we encourage you to first contact the CASM development team at :email:`casm-developers@lists.engr.ucsb.edu`.

Pull requests should:

- Create a branch from the development branch for new features (i.e. *2.X*) and name it to indicate that it implements a new feature (i.e. *2.X-myfeature*) or a bug fix (i.e *2.0.0-patch-issue*)
- Propose a minimal set of changes
- Have code formatted and documented as described below
- Include appropriate tests
- Pass all CI tests
- Include a suggested CHANGELOG.md entry, see `keepachangelog.com <https://keepachangelog.com>`_.

Layout
------

Repository layout
^^^^^^^^^^^^^^^^^

The repository for each *libcasm* distribution packages is organized as follows:

- *include/*: C++ headers
- *src/*: C++ source files
- *tests/unit*: C++ unit tests
- *python/libcasm/<name>/*: Python namespace packages
- *python/src/*: pybind11 wrapper source files
- *python/tests/<name>/*: Python tests
- *python/doc/*: Python documentation


Installation layout
^^^^^^^^^^^^^^^^^^^

When the project is built and installed, components are added to the Python installation location (i.e. *<python package prefix> = <something>/lib/pythonX.Y/site-packages/*) in the *libcasm/* folder at the following locations:

*<python package prefix>/libcasm/*:

- *include/*: C++ headers
- *lib/*: Built C++ libraries
- *share/CASMcode_<name>/cmake/*: CMake distribution data
- *<name>/*: libcasm Python namespace packages, with Python source files and built pybind11 wrapper libraries


Installing from source
----------------------

.. note::

    Care must be taken on Linux that code linking to the CASM C++ libraries is compiled with the same choice of the -D_GLIBCXX_USE_CXX11_ABI compiler flag, otherwise there will be "undefined reference" linking errors (see `Dual ABI <https://gcc.gnu.org/onlinedocs/libstdc++/manual/using_dual_abi.html>`_).  The older CASM Linux C++ libraries distributed on PyPI with tag "manylinux2014" use ``-D_GLIBCXX_USE_CXX11_ABI=0``. Newer compilers and the Linux *libcasm* packages distributed on PyPI with tag "manylinux_2_28" use ``-D_GLIBCXX_USE_CXX11_ABI=1``.  It will be noted in the following where configuration steps may be necessary.

Installation of *libcasm* distribution packages from source requires standard compilers with support for C++17, Python >= 3.9, and *CMake*. The use of `ccache <https://ccache.dev/>`_ is recommended to avoid duplicating compilations. For example:

- On Ubuntu linux:

  .. code-block:: bash

      sudo apt-get install build-essential ccache cmake


- On Mac OSX:

  .. code-block:: bash

      xcode-select --install
      brew install ccache cmake

- In a conda environment:

  .. code-block:: bash

      conda create -n casm --override-channels -c conda-forge python=3 ccache cmake cxx-compiler
      conda activate casm

If installing from a git repository, make sure any submodules are checked out with:

.. code-block:: bash

    git submodule update --init --recursive

Then the CASM package and its dependencies can be installed with:

.. code-block:: bash

    # Configuration options:
    #
    # To install with manylinux2014 package dependencies set:
    #   export SKBUILD_CONFIGURE_OPTIONS="-DCMAKE_CXX_FLAGS='-D_GLIBCXX_USE_CXX11_ABI=0'"
    # To print compiler commands set:
    #   export SKBUILD_BUILD_OPTIONS="--verbose"
    pip install .


Building documentation
----------------------

Install documentation requirements:

.. code-block:: bash

    pip install -r doc_requirements.txt

Clone `CASMcode_pydocs <https://github.com/prisms-center/CASMcode_pydocs>`_, then set an environment variable indicating where to store the docs:

.. code-block:: bash

    mkdir <path-to-pydocs>/docs/libcasm
    export LIBCASM_PYDOCS=<path-to-pydocs>/docs/libcasm


Install the *libcasm* package first, then build and open the documentation:

.. code-block:: bash

    cd python/doc
    # In the following, replace:
    # - <package> with the distribution package name
    # - <vers> with the major.minor version number
    # Example: <package>=xtal, <vers>=2.0
    sphinx-build -b html . $LIBCASM_PYDOCS/<package>/<vers>/
    open $LIBCASM_PYDOCS/<package>/<vers>/index.html


Testing
-------

To install testing requirements, do:

.. code-block:: bash

    pip install -r test_requirements.txt

Use *pytest* to run the tests. To run all tests, do:

.. code-block:: bash

    pytest -rsap python/tests

As an example of running a specific test, do:

.. code-block:: bash

    pytest -rsap python/tests/<filepath>::<function_name>


Installing in editable mode
---------------------------

Editable installation of the pure Python components (i.e. ``pip install -e``) is very useful for development. It is not currently supported by `scikit-build <https://scikit-build.readthedocs.io/>`_ or `scikit-build-core <https://scikit-build-core.readthedocs.io/en/latest/>`_ (the next generation of scikit-build) using the standard build process, but may be possible at some point.

Currently, editable installation can be done by first installing the pure C++ portion of a CASM module and then installing the pybind11 wrapper and Python portions of the module.

Installing C++ components only
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Install build dependencies:

.. code-block:: bash

    pip install -r build_requirements.txt

Once the *libcasm-global* package is installed, *CMake* should be able find and link to other CASM module dependencies in the *<python package prefix>/libcasm/* directory automatically. For C++ only installation, the installation location for CASM C++ libraries must be specified using ``CASM_PREFIX``. The same location will be set as the install location unless the user defines ``CMAKE_INSTALL_PREFIX`` explicitly to override it. CMake will also detect and use *ccache* if it is installed, which is very useful to avoid recompiling objects and greatly speed up development.

Then, to make and install CASM C++ components into the *<python package prefix>/libcasm/* directory do the following, starting in the repository root directory:

.. code-block:: bash

    mkdir build_cxx_only
    cd build_cxx_only

    # Set the CASM prefix. This will give:
    #   <something>/lib/pythonX.Y/sites-packages/libcasm
    export CASM_PREFIX=$(python -c 'import site; print(site.getsitepackages()[0])')/libcasm

    # Some configuration options:
    #
    # For C++ only CASM installation add:
    #   -DCASM_PREFIX=<path-to-casm>
    # To link to manylinux2014 packages add:
    #   -DCMAKE_CXX_FLAGS='-D_GLIBCXX_USE_CXX11_ABI=0'
    # CASM may be slow if not using the Release build type (-O3 -DNDEBUG).
    # To specify build type use Release, Debug, RelWithDebInfo, or MinSizeRel
    #   -DCMAKE_BUILD_TYPE=Release
    #
    cmake -DCMAKE_BUILD_TYPE=Release ..
    make -j4 VERBOSE=1
    make install

C++ unit tests can be built after C++ components are installed as in the previous step. To make and run unit tests, return to the repository root directory and do:

.. code-block:: bash

    mkdir build_cxx_test
    cd build_cxx_test

    # Set the CASM prefix. This will give:
    #   <something>/lib/pythonX.Y/sites-packages/libcasm
    export CASM_PREFIX=$(python -c 'import site; print(site.getsitepackages()[0])')/libcasm

    # Some configuration options:
    #
    # For C++ only CASM installation add:
    #   -DCASM_PREFIX=<path-to-casm>
    # To link to manylinux2014 packages add:
    #   -DCMAKE_CXX_FLAGS='-D_GLIBCXX_USE_CXX11_ABI=0'
    # CASM tests may be slow if not using the Release build type (-O3 -DNDEBUG).
    # To specify build type use Release, Debug, RelWithDebInfo, or MinSizeRel
    #   -DCMAKE_BUILD_TYPE=Release
    #
    cmake -DCMAKE_BUILD_TYPE=Release ../tests
    make -j4 VERBOSE=1
    make test

To uninstall a C++ only installation, use the *install_manifest.txt* file generated by *cmake*:

.. code-block:: bash

    cd build_cxx_only
    xargs rm < install_manifest.txt


Installing pybind11 and pure Python components
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Use the *python/setup.py* file for editable install of the pure Python components:

.. code-block:: bash

    cd python
    
    # Set the CASM prefix. This will give:
    #   <something>/lib/pythonX.Y/sites-packages/libcasm
    export CASM_PREFIX=$(python -c 'import site; print(site.getsitepackages()[0])')/libcasm

    # Some configuration options:
    #
    # To link to manylinux2014 packages set:
    #   export CASM_EXTRA_COMPILE_ARGS='-D_GLIBCXX_USE_CXX11_ABI=0'
    #
    pip install -e .

At this point, changes made to pure Python source files are immediately testable. Testing changes made to the pybind11 wrappers requires re-building with ``pip install -e .``. Testing changes made to the CASM C++ components requires re-building and re-installing the C++ components and then re-building with ``pip install -e .``.

To uninstall the Python package use the distribution package name:

.. code-block:: bash

    # <distname> = libcasm-global, libcasm-xtal, etc.
    pip uninstall <distname>


Testing the combined build process
----------------------------------

To test the combined build process performed by ``pip install .``, it may be useful to skip build isolation:

.. code-block:: bash

    pip install -v --no-build-isolation .

This allows ccache to identify and reuse identical compilation steps and speeds up the build process. To do this, it is necessary to have installed all build requirements already from *build_requirements.txt*. Build isolation should not be skipped for CI tests.

When built together using pip, all C++ and Python components of a distribution package can be uninstalled using the distribution package name:

.. code-block:: bash

    # <distname> = libcasm-global, libcasm-xtal, etc.
    pip uninstall <distname>


C++ development
---------------

C++ formatting
^^^^^^^^^^^^^^

For C++ code formatting, use clang-format with ``-style=google``. Use the *stylize.sh* script to format files staged for commit. To enforce that it is run before committing, place the *pre-commit* file, or equivalent code, in *.git/hooks*. The process looks like:

.. code-block:: bash

    git add <unformatted new and changed files>
    ./stylize.sh
    git add <formatted new and changed files>

    # good idea to check changes
    git status
    git diff --cached
    git commit -m "Added new feature X"


Adding or removing C++ files
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When adding or removing C++ files, the *CMakeLists.txt.in* file in the root of the repository must be updated to include the new files. This file is used to generate the *CMakeLists.txt* file during the build process. The *CMakeLists.txt.in* file contains a list of source files and headers that are included in the build.

When files are added or removed from *include/*, *src/*, or *tests/*, the *CMakeLists.txt* and *tests/CMakeList.txt* files must be updated. This can be done in an automated fashion using a script to copy the *CMakeLists.txt.in* and *tests/CMakeList.txt.in* templates and populate them with the current project files:

.. code-block:: bash

    python make_CMakeLists.py

The files to be included in source distributions are specified by the *MANIFEST.in* file using path pattern matching. This should rarely need to be changed.


C++ style
^^^^^^^^^

- When in doubt, refer to the `Google C++ Style Guide <https://google.github.io/styleguide/cppguide.html>`_.
- Use doxygen for comments.
- Fit in to the existing style for a particular file


Adding C++ tests
^^^^^^^^^^^^^^^^

- Add C++ library tests in *tests/unit/<module>*, with the naming convention *<something>_test.cpp*, using googletest.
- If data files are needed for testing, they can be placed in *tests/unit/<module>/data/*.
- To access data files and create temporary testing directories for reading and writing files, use the methods available in *tests/unit/testdir.hh*.
- If a new module is added, (i.e. a new *casm/<module>*) then new unit tests should be added under *tests/unit/<module/* and *tests/CMakeLists.txt.in* and *make_CMakeLists.py* must be updated to add the new unit tests.
- To run only C++ library tests, follow the instructions for building only the C++ library and tests as part of an editable installation.


Using CASM C++ libraries in external packages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The *libcasm-casmglobal* package allows finding the CASM installation location for use by other packages.

To find the installation location (i.e. *<python package prefix>/libcasm*) use:

.. code-block:: bash

    python -m libcasm.casmglobal --prefix

As an example using this with CMake to configure and build a program using CASM C++ libraries see:

- `tests/CMakeLists.txt.in <https://github.com/prisms-center/CASMcode_crystallography/blob/main/tests/CMakeLists.txt.in>`_: The unit tests provide an example of configuring a program to link with CASM C++ libraries *libcasm-global* and *libcasm-crystallography*.
- `CMakeLists.txt.in <https://github.com/prisms-center/CASMcode_crystallography/blob/main/CMakeLists.txt.in>`_: The main configuration file provides an example of configuring a C++ library to link with *libcasm-global* and update the default installation prefix to be in the same location as other CASM tools. It also demonstrates how to configure a Python extension module built with pybind11 to link with CASM C++ libraries.


Python development
------------------

Bindings of the C++ libraries to Python are made using `pybind11 <https://pybind11.readthedocs.io/>`_. Bindings are located in the *python/src/* directory. The bindings are compiled into a Python extension module using `scikit-build <https://scikit-build.readthedocs.io/en/latest/>`_ and `setuptools <https://setuptools.pypa.io/en/latest/>`_.

Generally, the bound C++ classes and functions are included as a "private" Python module prepended with "_" (i.e. *libcasm.configuration._configuration*) and then imported into the documented Python package (i.e. *libcasm.configuration*) in the *__init__.py* file. This allows mixing bound C++ classes and methods with pure Python classes and methods into a single package with a consistent API and documentation.

Follow the Python development and documentation guidelines :ref:`here <contributing-to-casm-packages-python-development>`.


Release process
----------------

Packages are built into Python wheels by GitHub Actions using `cibuildwheel <https://cibuildwheel.pypa.io/>`_. For an example, see `build_wheels.yml <https://github.com/prisms-center/CASMcode_configuration/blob/main/.github/workflows/build_wheels.yml>`_ in the *libcasm-configuration* package. This involves configuration in the `pyproject.toml <https://github.com/prisms-center/CASMcode_configuration/blob/main/pyproject.toml>`_ file.

Assume that the *libcasm* package repository git remote is called *origin*, that it has a stable *main* branch, a development branch called *2.X*. Then the release process is as follows:

1. Merge new features and changes into the development branch, i.e. 2.X

   1. Remember to update CHANGELOG.md along with the changes
   2. Breaking changes to the Python package API should only be done in a new major version, i.e. 2.X -> 3.X.
   3. New features and non-breaking changes to the Python package API can go into the current development branch, i.e. 2.X

2. When the changes to be included in a new release are ready, create a release branch: 2.X -> 2.0.0

   1. New features and changes should get a minor version bump, i.e. 2.0.Y -> 2.1.0
   2. Bug fixes should get a patch version bump, i.e. 2.0.Y -> 2.0.Z

3. Update the version in all the places that apply, which may include:

   1. *CMakeLists.txt.in* (MAJOR.MINOR.PATCH only)
   2. *tests/CMakeLists.txt.in* (MAJOR.MINOR.PATCH only)
   3. *doc/doxygen_config* (MAJOR.MINOR.PATCH only)
   4. *pyproject.toml*
   5. *setup.py*
   6. *python/doc/conf.py*
   7. *python/setup.py*
   8. *tests/unit/global/version_test.cpp* (or equivalent)
   9. *python/tests/casmglobal/test_casmglobal.py* (or equivalent)
   10. *CHANGELOG.md* (add below unreleased, and set date)

4. Update the *CMakeList.txt* and *tests/CMakeLists.txt* files which are generated by running:

   .. code-block:: bash

      python make_CMakeLists.py

5. Commit changes in (3) and (4), and push to GitHub. Check that all tests pass, making fixes on the release branch if necessary.

6. Once all tests pass and the version updates have been made, create a PyPI release:

   1. Make a directory for the built wheels and source distribution:

      .. code-block:: bash

          mkdir dist/2.0.0_raw

   2. Download built wheels and source distribution from the *build_wheels.yml* GitHub Action artifacts to *dist/2.0.0_raw*
   3. In *build_wheels.yml* the "repair-wheel-command" step is skipped to allow the CASM C++ extension libraries in different packages to be linked. To repair the wheel naming ourselves, run the *label_wheels.py* script and then upload to PyPI:

      .. code-block:: bash

          python label_wheels.py 2.0.0`
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

