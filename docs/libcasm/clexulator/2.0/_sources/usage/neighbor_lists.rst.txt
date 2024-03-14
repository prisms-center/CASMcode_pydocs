.. _neighbor-lists-index:

Neighbor lists
==============

:class:`~libcasm.clexulator.Clexulator` source code contains the definition for a single translational equivalent of each cluster basis function. A neighbor list is used to determine the correct degree of freedom (DoF) values to evaluate the cluster basis function at a particular lattice translation. There are two types of neighbor lists:

- :class:`~libcasm.clexulator.PrimNeighborList`: A prim neighbor list, defines an ordering of unit cells in the local neighborhood of a reference unit cell for a particular :class:`~libcasm.xtal.Prim`.
- :class:`~libcasm.clexulator.SuperNeighborList`: A supercell neighbor list, uses the ordering specified by a prim neighbor list to construct a neighbor list for each unit cell in a supercell. One of these must be generated for each supercell in which cluster basis functions are evaluated.

In the :class:`~libcasm.clexulator.PrimNeighborList`, unit cells are ordered by lexicographically sorting the vectors :math:`[r, i, j, k]`, where :math:`i,j,k` are the integer unit cell coordinates which specify the unit cell origin as multiples of the prim lattice vectors, and :math:`r` is the integer distance :math:`x^{\mathsf{T}} W x`, where :math:`x=[i, j, k]`, and :math:`W` is an integer lattice weight matrix which is typically chosen based on the lattice vectors to make an approximately spherical neighborhood in Cartesian coordinates. Whenever a new unit cell is inserted in the :class:`~libcasm.clexulator.PrimNeighborList`, a bounding box based on :math:`W` is used to ensure that all other unit cells with :math:`r` of equal or lesser value are also inserted.

In the :class:`~libcasm.clexulator.SuperNeighborList`, sites are ordered by lexicographically sorting the vectors :math:`[r, i, j, k, b]`, where :math:`r, i, j, k` are defined as before, and `b` is the sublattice index of the site. Only sites with sublattice indices specified by the :class:`~libcasm.clexulator.PrimNeighborList` constructor parameter `sublattice_indices` are included in the neighbor list. By default, any :class:`~libcasm.xtal.Prim` sublattice that has 2 or more allowed occupants or 1 or more allowed continuous degree of freedom (DoF) is included.


Constructing neighbor lists
---------------------------

In most cases, CASM chooses an appropriate set of default parameters for the :class:`~libcasm.clexulator.PrimNeighborList` automatically from the :class:`~libcasm.xtal.Prim` using the factory function :func:`~libcasm.clexulator.make_default_prim_neighbor_list` as in the following example:

.. code-block:: Python

    import libcasm.xtal as xtal
    import libcasm.xtal.prims as xtal_prims
    from libcasm.clexulator import make_default_prim_neighbor_list

    xtal_prim = xtal_prims.HCP(a=1.0, occ_dof=["A", "B"])
    prim_neighbor_list = make_default_prim_neighbor_list(xtal_prim)

The :class:`~libcasm.clexulator.PrimNeighborList` can then be expanded to ensure that unit cells or sites from all relevant clusters relative to the origin unit cell are included. The order in which sites or unit cells are added to the neighbor list does not matter. All intermediary unit cells in the neighbor list will also be added and the order is fixed by the constructor parameters.

.. code-block:: Python

    # site: libcasm.xtal.IntegralSiteCoordinate

    # add site (site)
    prim_neighbor_list.add_site(site)

    # or equivalently (unitcell: array_like of int, shape=(3,))
    prim_neighobr_list.add_unitcell(site.unitcell())

To get the neighbor list index for a particular site relative to the origin unit cell, use the method  :class:`~libcasm.clexulator.PrimNeighborList.neighbor_index` (which will also automatically expand the list if necessary).

.. code-block:: Python

    import numpy as np

    site = xtal.IntegralSiteCoordinate(
        sublattice=1,
        unitcell=np.array([0, 2, 0]),
    )
    neighbor_index = prim_neighbor_list.neighbor_index(site)


To get the neighbor list index for a particular site relative to a different unit cell, subtract the reference unit cell coordinates first so that the unit cell coordinates of the site are releative to the reference unit cell.

.. code-block:: Python

    site = xtal.IntegralSiteCoordinate(
        sublattice=1,
        unitcell=np.array([0, 2, 0]),
    )
    reference_unitcell=np.array([0, 1, 0])
    site -= reference_unitcell
    neighbor_index = prim_neighbor_list.neighbor_index(site)


To efficiently find DoF values on neighboring sites in a particular supercell, the :class:`~libcasm.clexulator.SuperNeighborList` generates (for every unit cell in the supercell) a list of the linear site indices of the neighboring sites which can be used to read DoF values from a :class:`~libcasm.clexulator.ConfigDoFValues` instance.

For a given supercell, the :class:`~libcasm.clexulator.SuperNeighborList` can be constructed from a :class:`~libcasm.clexulator.PrimNeighborList` and the transformation matrix defining the supercell.

.. code-block:: Python

    from libcasm.clexulator import SuperNeighborList

    # transformation_matrix_to_super: numpy.ndarray[numpy.int64[3, 3]
    transformation_matrix_to_super = np.eye(3, dtype=int) * 10

    super_neighbor_list = SuperNeighborList(
        transformation_matrix_to_super, prim_neighbor_list)


Using a SuperNeighborList
-------------------------

The sites in the neighborhood of a particular unit cell can be iterated over using the :class:`~libcasm.clexulator.SuperNeighborList.sites` method of :class:`~libcasm.clexulator.SuperNeighborList`. Conversions between integer coordinate and linear index representations can be performed using :class:`~libcasm.xtal.UnitCellIndexConverter` and :class:`~libcasm.xtal.SiteIndexConverter`.

.. code-block:: Python

    # Get the linear site index of sites neighboring unitcell [0, 3, 1]:

    super_neighbor_list = SuperNeighborList(
        transformation_matrix_to_super, prim_neighbor_list)
    unitcell_index_converter = xtal.UnitCellIndexConverter(
        transformation_matrix_to_super)
    site_index_converter = xtal.SiteIndexConverter(
        transformation_matrix_to_super, prim_neighbor_list.total_n_sublattice())

    unitcell = np.array([0, 3, 1])
    linear_unitcell_index = unitcell_index_converter.linear_unitcell_index(unitcell)

    print("Neighbor sites")
    for i, linear_site_index in enumerate(
        super_neighbor_list.sites(linear_unitcell_index)
    ):
        integral_site_coordinate = \
            site_index_converter.integral_site_coordinate(linear_site_index)
        print(
            f"neighbor {i}: "
            f"linear_site_index: {linear_site_index} "
            f"integral_site_coordinate: {integral_site_coordinate}"
        )

::

    Neighbor sites
    neighbor 0: linear_site_index: 130 integral_site_coordinate: 0, 0 3 1
    neighbor 1: linear_site_index: 1130 integral_site_coordinate: 1, 0 3 1
    neighbor 2: linear_site_index: 129 integral_site_coordinate: 0, 9 2 1
    neighbor 3: linear_site_index: 1129 integral_site_coordinate: 1, 9 2 1
    ...
    neighbor 63: linear_site_index: 1132 integral_site_coordinate: 1, 2 3 1
    neighbor 64: linear_site_index: 152 integral_site_coordinate: 0, 2 5 1
    neighbor 65: linear_site_index: 1152 integral_site_coordinate: 1, 2 5 1


The unit cells in the neighborhood of a particular unit cell can be similarly iterated over using the :class:`~libcasm.clexulator.SuperNeighborList.unitcells` method of :class:`~libcasm.clexulator.SuperNeighborList`.

.. code-block:: Python

    print("Neighbor unit cells")
    for i, nbor_linear_unitcell_index in enumerate(
        super_neighbor_list.unitcells(linear_unitcell_index)
    ):
        nbor_unitcell = unitcell_index_converter.unitcell(nbor_linear_unitcell_index)
        print(
            f"neighbor {i}: "
            f"nbor_linear_unitcell_index: {nbor_linear_unitcell_index} "
            f"nbor_unitcell: {nbor_unitcell}"
        )

::

    Neighbor unit cells
    neighbor 0: nbor_linear_unitcell_index: 130 nbor_unitcell: [0 3 1]
    neighbor 1: nbor_linear_unitcell_index: 129 nbor_unitcell: [9 2 1]
    neighbor 2: nbor_linear_unitcell_index: 139 nbor_unitcell: [9 3 1]
    neighbor 3: nbor_linear_unitcell_index: 120 nbor_unitcell: [0 2 1]
    ...
    neighbor 30: nbor_linear_unitcell_index: 150 nbor_unitcell: [0 5 1]
    neighbor 31: nbor_linear_unitcell_index: 132 nbor_unitcell: [2 3 1]
    neighbor 32: nbor_linear_unitcell_index: 152 nbor_unitcell: [2 5 1]
