.. image:: _static/logo_outline.svg
  :alt: CASM logo
  :width: 600
  :class: only-light

.. image:: _static/logo_dark_outline.svg
  :alt: CASM logo
  :width: 600
  :class: only-dark


Python packages
===============

CASM consists of a collection of packages that implement different functionality.
Below are links to the documentation for the latest release of the each package. The Python namespace `libcasm` is used for CASM_ packages that include C++ implementations. The Python namespace `casm` is used for pure Python CASM_ packages.


libcasm-global
--------------

CASM global constants and tools

Packages:

- **libcasm.casmglobal** - CASM global constants
- **libcasm.counter** - Loop over many incrementing variables in one loop

Install:

.. code-block:: bash

    pip install libcasm-global

Docs: `[2.0.6] <../../libcasm/global/2.0/>`_

Links: |GitHub_global|_ |PyPI_global|_


libcasm-xtal
------------

CASM crystallography

Packages:

- **libcasm.xtal**

  - Data structures for representing lattices, crystal structures, degrees of freedom (DoF), symmetry operations, and crystal sites.
  - Methods for enumerating superlattices, making super structures, finding primitive and reduced cells, finding symmetry operations, and applying symmetry operations.

Install:

.. code-block:: bash

    pip install libcasm-xtal

Docs: `[2.0a12] <../../libcasm/xtal/2.0/>`_

Links: |GitHub_xtal|_ |PyPI_xtal|_


libcasm-composition
-------------------

CASM composition axes, conversions, and calculations

Packages:

- **libcasm.composition**

  - Methods for constructing standard composition axes
  - Methods for converting between mol and parametric composition
  - Methods for calculating the composition of configurations, including sublattice compositions

Install:

.. code-block:: bash

    pip install libcasm-composition

Docs: `[2.0a4] <../../libcasm/composition/2.0/>`_

Links: |GitHub_composition|_ |PyPI_composition|_


libcasm-mapping
---------------

CASM structure mapping methods

Packages:

- **libcasm.mapping**

  - Methods for searching for low-cost lattice, atom, and structure mappings, taking into account symmetry
  - Methods for generating interpolated structures based on mapping results
  - Methods for generating symmetrically equivalent mappings
  - Data structures and methods for creating custom mapping searches

Install:

.. code-block:: bash

    pip install libcasm-mapping


Docs: `[2.0a6] <../../libcasm/mapping/2.0/>`_

Links: |GitHub_mapping|_ |PyPI_mapping|_


libcasm-clexulator
------------------

CASM cluster expansion calculator (clexulator) package

Packages:

- **libcasm.clexulator**

  - A data structure for representing degrees of freedom (DoF) in
    a configuration
  - Neighbor list generation
  - Methods for evaluating cluster expansion basis functions and cluster expansion values
  - Methods for evaluating order parameters

This package does not include cluster expansion basis function generation, but uses basis functions that have been generated elsewhere (i.e. `casm bset`) and written as CASM clexulator source code.

Install:

.. code-block:: bash

    pip install libcasm-clexulator


Docs: `[2.0a7] <../../libcasm/clexulator/2.0/>`_

Links: |GitHub_clexulator|_ |PyPI_clexulator|_


libcasm-configuration
---------------------

CASM configuration comparison and enumeration

Packages:

- **libcasm.clusterography**

  - Methods for comparing and enumerating unique clusters and orbits of equivalent clusters
  - Methods for constructing neighborhoods for impact tables

- **libcasm.configuration**

  - Classes for representing supercells and configurations
  - Methods for comparing configurations
  - Methods for copying configurations to make sub- or super-configurations
  - Methods for creating configurations with properties from mapped structures

- **libcasm.enumerate**

  - Methods for enumerating configurations

- **libcasm.irreps**

  - Methods for irreducible space decompositions

- **libcasm.local_configuration**

  - Methods for representing, comparing, and transforming local configurations

- **libcasm.occ_events**

  - Class for representing occupation events for kinetic Monte Carlo (KMC)
  - Methods for comparing and enumerating occupation events

- **libcasm.sym_info**

  - Methods for generating and using crystal factor groups and point groups
  - Mehtods for using group-subgroup relationships, multiplication tables, and conjugacy classes
  - Methods for constructing symmetry group representations for integral site coordinates and configuration degrees of freedom (DoF) values


Install:

.. code-block:: bash

    pip install libcasm-configuration


Docs: `[2.0a8] <../../libcasm/configuration/2.0/>`_

Links: |GitHub_configuration|_ |PyPI_configuration|_


casm-bset
---------

CASM cluster expansion basis set construction module

Packages:

- **casm.bset**

  - Generate coupled cluster expansion Hamiltonians.
  - Generate C++ code for a CASM clexulator.
  - Generalized methods to construct symmetry adapted functions.

Install:

.. code-block:: bash

    pip install casm-bset

Configuration:

- See the `casm-bset documentation <https://prisms-center.github.io/CASMcode_pydocs/casm/bset/2.0/installation.html#environment-variable-configuration>`_ for environment configuration instructions.


Docs: `[2.0a4] <../../casm/bset/2.0/>`_

Links: |GitHub_bset|_ |PyPI_bset|_


libcasm-monte
-------------

CASM building blocks for Monte Carlo simulations

Packages:

- **libcasm.monte**

  - Sampling classes and functions
  - Equilibration, convergence checking, and statistics calculation
  - Generic results IO
  - Supercell index conversions
  - Generic event definitions, construction, and selection
  - Example Ising model implementations


Install:

.. code-block:: bash

    pip install libcasm-monte


Docs: `[2.0a6] <../../libcasm/monte/2.0/>`_

Links: |GitHub_monte|_ |PyPI_monte|_


libcasm-clexmonte
-----------------

CASM cluster expansion Monte Carlo simulations

Install:

.. code-block:: bash

    pip install libcasm-clexmonte


Docs: `[2.0a6] <../../libcasm/clexmonte/2.0/>`_

Links: |GitHub_clexmonte|_ |PyPI_clexmonte|_


casm-project
-----------------

**Note**: casm-project is under development and not yet released. The interface may change in future releases.

The casm-project package makes it easier to construct, fit, and use a cluster expansion in CASM version >= 2 by:

- providing quick access to the most commonly used methods,
- automatically reading project data needed by those methods from a CASM project directory,
- automatically writing the results to the standard location in a CASM project directory.


Docs: `[prerelease] <../../casm/project/2.0/>`_

Links: |GitHub_project|_ |PyPI_project|_


About CASM
==========

The libcasm packages are part of the CASM_ open source software package, which is designed to perform first-principles statistical mechanical studies of multi-component crystalline solids.

CASM is developed by the Van der Ven group, originally at the University of Michigan and currently at the University of California Santa Barbara.

For more information, see the `CASM homepage <CASM_>`_.


.. _CASM: https://prisms-center.github.io/CASMcode_docs/

.. |GitHub_global| image:: _static/github-mark.png
  :alt: Link to CASMcode_global GitHub repository
  :width: 24
.. _GitHub_global: https://github.com/prisms-center/CASMcode_global/

.. |PyPI_global| image:: _static/python-logo-only.png
  :alt: Link to libcasm-global PyPI package
  :width: 20
.. _PyPI_global: https://pypi.org/project/libcasm-global/


.. |GitHub_xtal| image:: _static/github-mark.png
  :alt: Link to CASMcode_crystallography GitHub repository
  :width: 24
.. _GitHub_xtal: https://github.com/prisms-center/CASMcode_crystallography/

.. |PyPI_xtal| image:: _static/python-logo-only.png
  :alt: Link to libcasm-xtal PyPI package
  :width: 20
.. _PyPI_xtal: https://pypi.org/project/libcasm-xtal/


.. |GitHub_composition| image:: _static/github-mark.png
  :alt: Link to CASMcode_composition GitHub repository
  :width: 24
.. _GitHub_composition: https://github.com/prisms-center/CASMcode_composition/

.. |PyPI_composition| image:: _static/python-logo-only.png
  :alt: Link to libcasm-composition PyPI package
  :width: 20
.. _PyPI_composition: https://pypi.org/project/libcasm-composition/


.. |GitHub_mapping| image:: _static/github-mark.png
  :alt: Link to CASMcode_mapping GitHub repository
  :width: 24
.. _GitHub_mapping: https://github.com/prisms-center/CASMcode_mapping/

.. |PyPI_mapping| image:: _static/python-logo-only.png
  :alt: Link to libcasm-mapping PyPI package
  :width: 20
.. _PyPI_mapping: https://pypi.org/project/libcasm-mapping/


.. |GitHub_clexulator| image:: _static/github-mark.png
  :alt: Link to CASMcode_clexulator GitHub repository
  :width: 24
.. _GitHub_clexulator: https://github.com/prisms-center/CASMcode_clexulator/

.. |PyPI_clexulator| image:: _static/python-logo-only.png
  :alt: Link to libcasm-clexulator PyPI package
  :width: 20
.. _PyPI_clexulator: https://pypi.org/project/libcasm-clexulator/


.. |GitHub_configuration| image:: _static/github-mark.png
  :alt: Link to CASMcode_configuration GitHub repository
  :width: 24
.. _GitHub_configuration: https://github.com/prisms-center/CASMcode_configuration/

.. |PyPI_configuration| image:: _static/python-logo-only.png
  :alt: Link to libcasm-configuration PyPI package
  :width: 20
.. _PyPI_configuration: https://pypi.org/project/libcasm-configuration/


.. |GitHub_bset| image:: _static/github-mark.png
  :alt: Link to CASMcode_bset GitHub repository
  :width: 24
.. _GitHub_bset: https://github.com/prisms-center/CASMcode_bset/

.. |PyPI_bset| image:: _static/python-logo-only.png
  :alt: Link to casm-bset PyPI package
  :width: 20
.. _PyPI_bset: https://pypi.org/project/casm-bset/


.. |GitHub_monte| image:: _static/github-mark.png
  :alt: Link to CASMcode_monte GitHub repository
  :width: 24
.. _GitHub_monte: https://github.com/prisms-center/CASMcode_monte/

.. |PyPI_monte| image:: _static/python-logo-only.png
  :alt: Link to libcasm-monte PyPI package
  :width: 20
.. _PyPI_monte: https://pypi.org/project/libcasm-monte/


.. |GitHub_clexmonte| image:: _static/github-mark.png
  :alt: Link to CASMcode_clexmonte GitHub repository
  :width: 24
.. _GitHub_clexmonte: https://github.com/prisms-center/CASMcode_clexmonte/

.. |PyPI_clexmonte| image:: _static/python-logo-only.png
  :alt: Link to libcasm-clexmonte PyPI package
  :width: 20
.. _PyPI_clexmonte: https://pypi.org/project/libcasm-clexmonte/


.. |GitHub_project| image:: _static/github-mark.png
  :alt: Link to CASMcode_project GitHub repository
  :width: 24
.. _GitHub_project: https://github.com/prisms-center/CASMcode_project/

.. |PyPI_project| image:: _static/python-logo-only.png
  :alt: Link to libcasm-project PyPI package
  :width: 20
.. _PyPI_project: https://pypi.org/project/libcasm-project/
