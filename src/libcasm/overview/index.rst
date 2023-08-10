.. image:: _static/logo.svg
  :alt: CASM logo
  :width: 600

libcasm
=======

The Python namespace libcasm is used for CASM_ packages that include C++ implementations. Below are links to the documentation for the latest release of the individual libcasm distribution packages which implement core CASM capabilities.


Packages
========

- **libcasm-global**:

  - Docs: `[2.0] <../../global/2.0/>`_
  - Repository: `CASMcode_global <https://github.com/prisms-center/CASMcode_global/>`_
  - Packages:

    - **libcasm.casmglobal** - CASM global constants and tools
    - **libcasm.counter** - Loop over many incrementing variables in one loop

- **libcasm-xtal**:

  - Docs: `[2.0] <../../xtal/2.0/>`_
  - Repository: `CASMcode_crystallography <https://github.com/prisms-center/CASMcode_crystallography/>`_
  - Packages:

    - **libcasm.xtal**

      - Data structures for representing lattices, crystal structures, degrees of freedom (DoF), symmetry operations, and crystal sites.
      - Methods for enumerating superlattices, making super structures, finding primitive and reduced cells, finding symmetry operations, and applying symmetry operations.


About CASM
==========

The libcasm packages are part of the CASM_ open source software package, which is designed to perform first-principles statistical mechanical studies of multi-component crystalline solids.

CASM is developed by the Van der Ven group, originally at the University of Michigan and currently at the University of California Santa Barbara.

For more information, see the `CASM homepage <CASM_>`_.


.. _CASM: https://prisms-center.github.io/CASMcode_docs/
