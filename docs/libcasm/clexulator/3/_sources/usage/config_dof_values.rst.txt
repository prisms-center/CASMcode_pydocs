.. _config-dof-values-index:

The ConfigDoFValues data structure
==================================

Overview
--------

:class:`~libcasm.clexulator.ConfigDoFValues` is a data structure used to hold a configuration's degrees of freedom (DoF) values for efficient use in calculations.

The DoF values can be accessed via the following member functions:

- :func:`~libcasm.clexulator.ConfigDoFValues.occupation`: Occupation DoF values, as an integer array with one value for each site in the supercell
- :func:`~libcasm.clexulator.ConfigDoFValues.local_dof_values`: Local continuous DoF values, as two-dimensional floating-point arrays, accessed by DoF type, with one column of values for each site in the supercell.
- :func:`~libcasm.clexulator.ConfigDoFValues.global_dof_values`: Global continuous DoF values, as one-dimensional floating-point arrays, accessed by DoF type.

For CASM standard DoF types, this module provides methods for constructing properly sized :class:`~libcasm.clexulator.ConfigDoFValues` instances, and converting between two useful basis types as described below. See the CASM `Degrees of Freedom (DoF) and Properties`_ documentation for the full list of supported DoF types and their definitions. Some examples include:

- `"disp"`: Atomic displacement
- `"EAstrain"`: Euler-Almansi strain metric
- `"GLstrain"`: Green-Lagrange strain metric
- `"Hstrain"`: Hencky strain metric
- `"Cmagspin"`: Collinear magnetic spin
- `"SOmagspin"`: Non-collinear magnetic spin, with spin-orbit coupling


Data structure layout
---------------------

The size and order of values in :func:`~libcasm.clexulator.ConfigDoFValues` is standardized as follows:

- :func:`~libcasm.clexulator.ConfigDoFValues.occupation`: Occupation DoF values are accesible as a `np.ndarray[numpy.int32[n_sites, 1]]`, where `n_sites` is the number of sites in the supercell.

  - The occupant values, for example `[0, 1, 0, 2, 1, ...]`, specify the occupant on each site in a supercell.
  - The position in the array is called the `linear_site_index`.
  - The values are indices into the list of allowed occupants, as specified by a :class:`~libcasm.xtal.Prim`, on the associated supercell site.
  - Sites in the occupation vector are organized in blocks by sublattice:

    .. code-block::

        # Occupation vector layout:
        # [<-- sublattice 0 -->][<-- sublattice 1 -->]...[<-- sublattice n-1 -->]


  - For a particular supercell, each sublattice block is of size `n_unitcells`, the number of unitcells in the supercell
  - In each sublattice block, the sites are ordered according to the `linear_unitcell_index` of a particular unit cell. Conversions between unit cell indices `(i,j,k)` and `linear_unitcell_index` can be done using :class:`~libcasm.xtal.UnitCellIndexConverter`.
  - Conversions between `linear_site_index` and :class:`~libcasm.xtal.IntegralSiteCoordinate` (unit cell index `(i,j,k)` and sublattice index `b`) can be done using :class:`~libcasm.xtal.SiteIndexConverter`.
  - :class:`~libcasm.xtal.IntegralSiteCoordinate` can be used for conversions to and from Cartesian and fractional coordinates.

- :func:`~libcasm.clexulator.ConfigDoFValues.local_dof_values`: Local continuous DoF values are accesible as a `numpy.ndarray[numpy.float64[matrix_dim, n_sites]]`, where `n_sites` is the number of sites in the supercell, and `matrix_dim` varies by DoF type and basis type. A DoF type string (i.e. `"disp"` for displacements) is used as a key to access the values array.

  - For example, when using the standard basis for displacement DoF, `matrix_dim=3`.
  - Values can be accessed using indices `[dof_component_index, linear_site_index]`:

    - Columns of :func:`~libcasm.clexulator.ConfigDoFValues.local_dof_values` correspond to sites in the supercell using the same indexing conventions as the occupation vector.
    - Rows of :func:`~libcasm.clexulator.ConfigDoFValues.local_dof_values` correspond to components of a local DoF value.

- :func:`~libcasm.clexulator.ConfigDoFValues.global_dof_values`: Global continuous DoF values, accesible by DoF type as a `numpy.ndarray[numpy.float64[vector_dim, 1]]`, where `vector_dim` varies by DoF type and basis type. A DoF type string (i.e. `"GLstrain"` for strain represented using the Green-Lagrange strain metric) is used  as a key to access the values array.

  - For example, when using the standard basis for strain DoF, `vector_dim=6`.
  - Values can be accessed using index `[dof_component_index]`:

    - Elements of :func:`~libcasm.clexulator.ConfigDoFValues.global_dof_values` correspond to components of a global DoF value.


Basis types
-----------

There are two basis types for the continuous DoF values in :class:`~libcasm.clexulator.ConfigDoFValues`:

- The "standard DoF basis", using the standard definition of CASM supported DoF types (i.e. `x, y, z` for displacments)

  - Used for input and output, such as serialization, conversion to :func:`~libcasm.xtal.Structure`, mapping from :func:`~libcasm.xtal.Structure`, etc.

- The "prim DoF basis", which is specified by the :class:`~libcasm.xtal.DoFSetBasis` used to define the space of allowed DoF in a :class:`~libcasm.xtal.Prim`

  - Allows specifiying DoF values, including those that have been constrained to a subspace of the full standard DoF space (i.e. two-dimensional displacements, or only isotropic strains) using independent variables
  - Used as the input to cluster expansion and order parameter calculators


When using the standard DoF basis:

- The `matrix_dim` (row dimension) of local DoF and `vector_dim` of global DoF is the standard DoF dimension (i.e. 3 for displacements, 6 for strain), and DoF components are ordered using the CASM standard definitions. See the CASM `Degrees of Freedom (DoF) and Properties`_ documentation for definitions.

When using the prim DoF basis:

- The `matrix_dim` (row dimension) of local DoF is the maximum dimension of the basis for that DoF type on any sublattice.
- For local DoF, rows of zeros may be present at the bottom of a sublattice block if it is constrained to a smaller subspace than DoF of the same type on other sublattices.
- The `vector_dim` of global DoF is the dimension of the basis for that DoF type.

Construction
------------

There are factory functions to construct properly sized and zero-valued :class:`~libcasm.clexulator.ConfigDoFValues` instances for both basis types:

- :func:`~libcasm.clexulator.make_default_config_dof_values`: Construct :class:`~libcasm.clexulator.ConfigDoFValues` sized for use of the "prim DoF basis" in a supercell of a particular size
- :func:`~libcasm.clexulator.make_default_standard_config_dof_values`: Construct :class:`~libcasm.clexulator.ConfigDoFValues` sized for use of the "standard DoF basis" in a supercell of a particular size


Changing basis
--------------

Conversions between the two basis types can performed using:

- :func:`~libcasm.clexulator.from_standard_values`: Convert to the "prim DoF basis" from the "standard DoF basis"
- :func:`~libcasm.clexulator.to_standard_values`: Convert to the "standard DoF basis" from the "prim DoF basis"


Input and output
----------------

For serialization, :class:`~libcasm.clexulator.ConfigDoFValues` can be converted to and from representations using plain old Python data types (``list``, ``dict``, ``int``, ``float``, ``bool``, ``str``) using:

- :class:`~libcasm.clexulator.ConfigDoFValues.to_dict`
- :class:`~libcasm.clexulator.ConfigDoFValues.from_dict`

After conversion to dict, either ``json`` (for long term storage and portability) or ``pickle`` (for fast local storage) can be used to save :class:`~libcasm.clexulator.ConfigDoFValues`.

For a description of the format, see `ConfigDoF JSON object`_.

.. note::

    There is no change in basis when using :class:`~libcasm.clexulator.ConfigDoFValues.to_dict` or :class:`~libcasm.clexulator.ConfigDoFValues.from_dict`, so serialization can be done for :class:`~libcasm.clexulator.ConfigDoFValues` in either the standard DoF basis or prim DoF basis. However, the recommended convention is that the standard DoF basis is used for input and output.

Conversions to and from other data structures, such as conversion to :class:`~libcasm.xtal.Structure` and mapping from :class:`~libcasm.xtal.Structure` are outside the scope of this module. See `libcasm-configuration` and `libcasm-mapping` for those type of conversions.


.. _`ConfigDoF JSON object`: https://prisms-center.github.io/CASMcode_docs/formats/casm/clex/Configuration/#configdof-json-object

.. _`Degrees of Freedom (DoF) and Properties`: https://prisms-center.github.io/CASMcode_docs/formats/dof_and_properties/
