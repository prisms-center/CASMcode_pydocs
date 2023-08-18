.. image:: _static/logo.svg
  :alt: CASM logo
  :width: 600

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

Docs: `[2.0.3] <../../global/2.0/>`_

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

Docs: `[2.0a3] <../../xtal/2.0/>`_

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

Docs: `[2.0a1] <../../composition/2.0/>`_

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


Docs: `[2.0a1] <../../mapping/2.0/>`_

Links: |GitHub_mapping|_ |PyPI_mapping|_


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

