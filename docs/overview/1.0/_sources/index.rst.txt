.. image:: _static/logo.svg
  :alt: CASM logo
  :width: 600

casm
====

The Python namespace casm is used for pure Python CASM_ packages. Below are links to the documentation for the latest release of the individual casm distribution packages.


Packages
========

- **casm-python**:

  - Reference: `[1.2] <../../casm/python/1.2/>`_  `[1.0] <../../casm/python/1.0/>`_ `[0.3] <../../casm/python/0.3/>`_
  - Repository: `CASMpython <https://github.com/prisms-center/CASMpython/>`_
  - Packages:

    - **casm.api**

      - Access CASM v1 C API using ctypes

    - **casm.info**

      - Query info about prims, supercells, configurations, and neighbor lists

    - **casm.learn**

      - Integrate with `scikit-learn <https://scikit-learn.org>`_ to fit expansion coefficients

    - **casm.project**

      - Initialize projects and execute CASM commands
      - Read and write CASM project data
      - Select and query configurations

    - **casm.scripts**

      - ``casm`` - Integrates C++ command line program (``ccasm``) and Python command line interfaces (``casm learn``, ``casm calc``, etc.) into a single command line program
      - ``casm calc`` - Command line interface for casm.vaspwrapper
      - ``casm convert`` - Use `ASE <https://wiki.fysik.dtu.dk/ase/>`_ to convert to and from CASM formats and other crystal structure formats
      - ``casm learn`` - Command line interface for casm.learn

    - **casm.vasp**

      - Read and write VASP files
      - Run VASP calculations

    - **casm.vaspwrapper**

      - Glue between CASM and VASP
      - Setup VASP input files for CASM configurations
      - Parse VASP output as CASM structures with properties


About CASM
==========

The casm packages are part of the CASM_ open source software package, which is designed to perform first-principles statistical mechanical studies of multi-component crystalline solids.

CASM is developed by the Van der Ven group, originally at the University of Michigan and currently at the University of California Santa Barbara.

For more information, see the `CASM homepage <CASM_>`_.


.. _CASM: https://prisms-center.github.io/CASMcode_docs/
