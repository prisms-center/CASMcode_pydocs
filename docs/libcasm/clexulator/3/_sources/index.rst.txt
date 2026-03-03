.. image:: _static/logo_outline.svg
  :alt: CASM logo
  :width: 600
  :class: only-light

.. image:: _static/logo_dark_outline.svg
  :alt: CASM logo
  :width: 600
  :class: only-dark

libcasm-clexulator
==================

The libcasm-clexulator package is the CASM cluster expansion calculator (clexulator) module. This includes:

- A data structure for representing degrees of freedom (DoF) in
  a configuration
- Neighbor list generation
- Methods for evaluating cluster expansion basis functions and cluster expansion values
- Methods for evaluating order parameters

This package does not include cluster expansion basis function generation, but uses basis functions that have been generated elsewhere (i.e. `casm bset`) and written as CASM Clexulator source code.


About CASM
==========

The libcasm-clexulator package is part of the CASM_ open source software package, which is designed to perform first-principles statistical mechanical studies of multi-component crystalline solids.

CASM is developed by the Van der Ven group, originally at the University of Michigan and currently at the University of California Santa Barbara.

For more information, see the `CASM homepage <CASM_>`_.


License
=======

GNU Lesser General Public License (LGPL). Please see the LICENSE file available on GitHub_.


Documentation
=============

.. toctree::
    :maxdepth: 2

    installation
    usage
    Reference <reference/libcasm/index>
    bibliography

libcasm-clexulator is available on GitHub_.

.. _CASM: https://prisms-center.github.io/CASMcode_docs/
.. _GitHub: https://github.com/prisms-center/CASMcode_clexulator
