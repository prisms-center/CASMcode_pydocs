.. image:: _static/logo_outline.svg
  :alt: CASM logo
  :width: 600
  :class: only-light

.. image:: _static/logo_dark_outline.svg
  :alt: CASM logo
  :width: 600
  :class: only-dark

libcasm-configuration
=====================

The libcasm-configuration package is the CASM configuration comparison and enumeration module. This includes:

- Classes for representing supercells, configurations, clusters, and occupation events
- Methods for comparing and enumerating unique configurations, clusters, occupation events, and local environments
- Methods for generating orbits of symmetrically equivalent configurations, clusters, and occupation events
- Methods for copying configurations to make sub- or super-configurations
- Methods for generating symmetry groups, and constructing and applying symmetry representations
- Methods for performing irreducible space decompositions and finding symmetry adapted order parameters
- Methods for creating configurations with properties from mapped structures


Dependencies
============

For symmetry group classification, we use the `spglib <https://spglib.readthedocs.io/en/latest/>`_ package (see :func:`~libcasm.configuration.io.symgroup_to_dict_with_group_classification`).


About CASM
==========

The libcasm-clexulator package is part of the CASM_ open source software package, which is designed to perform first-principles statistical mechanical studies of multi-component crystalline solids.

CASM is developed by the Van der Ven group, originally at the University of Michigan and currently at the University of California Santa Barbara.

For more information, see the `CASM homepage <CASM_>`_.


License
=======

GNU Lesser General Public License (LGPL). Please see the LICENSE file available on GitHub_.


Documentation
-------------

.. toctree::
    :maxdepth: 2

    Installation <installation>
    Usage <usage/usage>
    Reference <reference/libcasm/index>


libcasm-configuration is available on GitHub_.

.. _CASM: https://prisms-center.github.io/CASMcode_docs/
.. _GitHub: https://github.com/prisms-center/CASMcode_configuration

