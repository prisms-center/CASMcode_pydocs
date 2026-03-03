.. _cluster-expansion-details:

Cluster expansion details
=========================

The CASM Clexulator
-------------------

CASM generates custom code for very efficient calculation of basis functions given a particular :class:`~libcasm.xtal.Prim` and choice of cluster expansion basis functions. This source code is written to a file and then may be compiled, linked, and used at runtime via the class  :class:`~libcasm.clexulator.Clexulator` (clexulator = CLuster EXpansion calcULATOR).

For periodic cluster expansions, CASM custom generates:

- `<primname>_Clexulator_<bsetname>.cc`: a clexulator source code file that evaluates the basis functions
- `basis.json`: a JSON file describing the cluster basis functions.

These files are written to a basis set directory, which is usually organized as:

.. code-block::

    /path/to/basis_sets/bset.<bsetname>/
    ├── <primname>_Clexulator_<bsetname>.cc
    └── basis.json

Here, `<primname>` is the name given to the :class:`~libcasm.xtal.Prim` and `<bsetname>` is a unique name for the cluster expansion basis set.

.. _local-clexulator-files:

For local cluster expansions, CASM custom generates:

- `<primname>_Clexulator_<bsetname>.cc`: a clexulator source code file for a prototype local cluster expansion
- `basis.json`: a JSON file describing the cluster basis functions for the prototype
- `<equivalent_index>/<primname>_Clexulator_<bsetname>_<equivalent_index>.cc`: source code files for each symmetrically equivalent local cluster expansion per unit cell
- `equivalents_info.json`: a JSON file which describes the prototype local expansion basis set's cluster basis and the symmetry operations which relate the prototype local expansion basis set to the symmetrically equivalent local cluster expansion basis sets.

From this information, CASM can compile functions to evaluate all the local cluster expansion basis functions and use a single set of expansion coefficients for each of the symmetrically equivalent local cluster expansions.

The files necessary for a local cluster expansion are all written to a local basis set directory, which is usually organized as:

.. code-block::

    /path/to/basis_sets/bset.<bsetname>/
    ├── 0
    │   └── <primname>_Clexulator_<bsetname>_0.cc
    ├── 1
    │   └── <primname>_Clexulator_<bsetname>_1.cc
    ├── ...
    ├── <primname>_Clexulator_<bsetname>.cc
    ├── equivalents_info.json
    └── basis.json

As before, `<primname>` is the name given to the :class:`~libcasm.xtal.Prim` and `<bsetname>` is a unique name for the local cluster expansion basis set.


Neighbor lists
--------------

:class:`~libcasm.clexulator.Clexulator` source code contains the definition for a single translational equivalent of each cluster basis function. A neighbor list is used to determine the correct degree of freedom (DoF) values to evaluate the cluster basis function at a particular lattice translation. There are two types of neighbor lists:

- :class:`~libcasm.clexulator.PrimNeighborList`: A prim neighbor list, defines an ordering of unit cells in the local neighborhood of a unit cell for a particular :class:`~libcasm.xtal.Prim`.
- :class:`~libcasm.clexulator.SuperNeighborList`: A neighbor list that uses the ordering specified by a prim neighbor list to construct a neighbor list for each unit cell in a supercell. One of these must be generated for each supercell in which cluster basis functions are evaluated.

For more details on constructing or using neighbor lists, see the section :ref:`Neighbor lists <neighbor-lists-index>`.

When a :class:`~libcasm.clexulator.Clexulator` is constructed, a :class:`~libcasm.clexulator.PrimNeighborList` is constructed with the same ordering as the one used to write the basis functions, or if an existing :class:`~libcasm.clexulator.PrimNeighborList` is provided, it is validated that the ordering is the same. Then the :class:`~libcasm.clexulator.PrimNeighborList` can be used to construct :class:`~libcasm.clexulator.SuperNeighborList` for each supercell in which the cluster expansion will be evaluated.


Environment variables for compiling and linking Clexulator
----------------------------------------------------------

Before constructing a :class:`~libcasm.clexulator.Clexulator` or :class:`~libcasm.clexulator.LocalClexulator`, set the environment variable ``CASM_PREFIX`` to the location where CASM is installed in order to enable proper compilation and linking of the CASM clexulator using the CASM libraries:

.. code-block:: bash

    export CASM_PREFIX=$(python -m libcasm.casmglobal --prefix)


In some cases, finer control of compilation and linking options may be necessary, which can be done as described in the :class:`~libcasm.clexulator.make_clexulator` documentation. For example, compiling and linking with gcc may require:

.. code-block:: bash

    export CASM_SOFLAGS="-shared -Wl,--no-as-needed"


Constructing a Clexulator
-------------------------

A :class:`~libcasm.clexulator.Clexulator` is constructed using a :class:`~libcasm.clexulator.PrimNeighborList` and the path to a CASM clexulator source file, using the factory function :class:`~libcasm.clexulator.make_clexulator`:

.. code-block:: Python

    from libcasm.clexulator import make_clexulator, PrimNeighborList
    prim_neighbor_list = PrimNeighborList()
    clexulator = make_clexulator(
        source="/path/to/.../<primname>_Clexulator_<bsetname>.cc",
        prim_neighbor_list=prim_neighbor_list,
    )


The :class:`~libcasm.clexulator.PrimNeighborList` provided to :class:`~libcasm.clexulator.make_clexulator` is expanded as necessary to include all sites required by the cluster basis functions.


Constructing a LocalClexulator
------------------------------

A :class:`~libcasm.clexulator.LocalClexulator` is constructed in a similar manner as :class:`~libcasm.clexulator.Clexulator`, using the factory function :class:`~libcasm.clexulator.make_local_clexulator`:

.. code-block:: Python

    from libcasm.clexulator import make_local_clexulator, PrimNeighborList
    prim_neighbor_list = PrimNeighborList()
    local_clexulator = make_local_clexulator(
        source="/path/to/.../<primname>_Clexulator_<bsetname>.cc",
        prim_neighbor_list=prim_neighbor_list,
    )

