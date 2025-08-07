.. image:: _static/logo_outline.svg
  :alt: CASM logo
  :width: 600
  :class: only-light

.. image:: _static/logo_dark_outline.svg
  :alt: CASM logo
  :width: 600
  :class: only-dark

casm-tools
==========

The casm-tools package provides command line programs based on capabilities implemented
in CASM. This includes:

- casm-calc: Setup, run, and import results of structure calculations
- casm-convert: [coming soon] Convert structures and configurations between CASM and
  other formats (using `ASE <https://wiki.fysik.dtu.dk/ase/>`_).
- casm-map: Structure mapping and import
- casm.tools.shared: Helper functions for I/O, integrating with ASE, and context
  managers.

casm-calc
---------

The casm-calc command line program uses `ASE <https://wiki.fysik.dtu.dk/ase/>`_ to
setup, run, and report results of structure calculations for use by CASM. It is
intended to give basic usable examples for VASP and select other software packages that
can be used as-is or as a starting point.

A suggested way to cite this program for performing VASP calculations is as follows:

.. code-block:: text

    "Structure calculations were performed with the `casm-calc` program provided
    by CASM [1], using the VASP calculator provided by ASE [2]."

    1. B. Puchala, J. C. Thomas, A. R. Natarajan, J. G. Goiri, S. S. Behara, J. L.
       Kaufman, A. Van der Ven, CASM—A software package for first-principles based
       study of multicomponent crystalline solids, Computational Materials Science
       217 (2023) 111897.
    2. Ask Hjorth Larsen, Jens Jørgen Mortensen, Jakob Blomqvist, Ivano E. Castelli,
       Rune Christensen, Marcin Dułak, Jesper Friis, Michael N. Groves, Bjørk Hammer,
       Cory Hargus, Eric D. Hermes, Paul C. Jennings, Peter Bjerre Jensen, James
       Kermode, John R. Kitchin, Esben Leonhard Kolsbjerg, Joseph Kubal, Kristen
       Kaasbjerg, Steen Lysgaard, Jón Bergmann Maronsson, Tristan Maxson, Thomas Olsen,
       Lars Pastewka, Andrew Peterson, Carsten Rostgaard, Jakob Schiøtz, Ole Schütt,
       Mikkel Strange, Kristian S. Thygesen, Tejs Vegge, Lasse Vilhelmsen, Michael
       Walter, Zhenhua Zeng, Karsten Wedel Jacobsen The Atomic Simulation Environment—A
       Python library for working with atoms J. Phys.: Condens. Matter Vol. 29 273002,
       2017.


casm-map
--------

The casm-map command line program performs structure mapping and import, including:

- Searching for structure mappings
- Creating mapped structures
- Creating interpolated structures
- Generating structures with equivalent relative orientations
- Importing structures into CASM projects as CASM configurations with calculated
  properties.

This program makes extensive use of lower-level methods which are implemented in
`libcasm-xtal <https://github.com/prisms-center/CASMcode_crystallography>`_,
`libcasm-mapping <https://github.com/prisms-center/CASMcode_mapping>`_, and
`libcasm-configuration <https://github.com/prisms-center/CASMcode_configuration>`_.

Methods for searching for low-cost lattice, atom, and structure mappings, taking into
account symmetry are based on the approach described in :cite:t:`THOMAS2021a`.

A suggested way to cite this program is as follows:

.. code-block:: text

    "Structure mappings were found by the method of Thomas et al. [1]
    using the `casm-map` program [2] provided by CASM [3]."

    1. B. Puchala, J. Thomas, and A. Van der Ven, "casm-map...".
    2. B. Puchala, J. C. Thomas, A. R. Natarajan, J. G. Goiri,
        S. S. Behara, J. L. Kaufman, A. Van der Ven, CASM—A software
        package for first-principles based study of multicomponent
        crystalline solids, Computational Materials Science 217
        (2023) 111897.
    3. J. C. Thomas, A. R. Natarajan, and A. Van der Ven, Comparing
        crystal structures with symmetry and geometry, npj
        Computational Materials, 7 (2021), 164.


About CASM
==========

The casm-tools package is part of the CASM_ open source software suite, which is
designed to perform first-principles statistical mechanical studies of multi-component
crystalline solids.

CASM is developed by the Van der Ven group, originally at the University of Michigan
and currently at the University of California Santa Barbara.

For more information, see the `CASM homepage <CASM_>`_.


License
=======

GNU Lesser General Public License (LGPL). Please see the LICENSE file available on
GitHub_.


Documentation
=============

.. toctree::
    :maxdepth: 2

    Installation <installation>
    Usage <usage>
    Reference <reference/casm/index>
    Bibliography <bibliography>

casm-tools is available on GitHub_.

.. _CASM: https://prisms-center.github.io/CASMcode_docs/
.. _GitHub: https://github.com/prisms-center/CASMcode_tools
