.. image:: _static/logo_outline.svg
  :alt: CASM logo
  :width: 600
  :class: only-light

.. image:: _static/logo_dark_outline.svg
  :alt: CASM logo
  :width: 600
  :class: only-dark


libcasm
=======

The Python namespace libcasm is used for CASM_ packages that include C++ implementations. Below are links to the documentation for the latest release of the individual libcasm distribution packages which implement core CASM capabilities.


Packages
========


libcasm-global
--------------

CASM global constants and tools

Packages:

- **libcasm.casmglobal** - CASM global constants
- **libcasm.counter** - Loop over many incrementing variables in one loop

Install:

.. code-block:: bash

    pip install libcasm-global

Docs: `[2.0.4] <../../global/2.0/>`_

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

Docs: `[2.0a9] <../../xtal/2.0/>`_

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

Docs: `[2.0a2] <../../composition/2.0/>`_

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


Docs: `[2.0a3] <../../mapping/2.0/>`_

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


Docs: `[2.0a4] <../../clexulator/2.0/>`_

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

- **libcasm.irreps**

  - Methods for irreducible space decompositions

- **libcasm.enumerate**

  - Methods for enumerating configurations

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


Docs: `[2.0a3] <../../configuration/2.0/>`_

Links: |GitHub_configuration|_ |PyPI_configuration|_


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
