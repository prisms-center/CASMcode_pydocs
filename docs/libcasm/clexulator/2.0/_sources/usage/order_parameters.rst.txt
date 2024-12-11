.. _order-parameters-index:

Evaluating order parameters
===========================

DoFSpace
--------

A :class:`~libcasm.clexulator.DoFSpace` specifies a subspace of allowed degrees of freedom (DoF) values of a particular DoF type, with subspace basis :math:`Q` (with column basis vectors :math:`q_i`), in terms of the prim DoF basis. This provides a definition for order parameters, :math:`\vec{\eta}`, according to :math:`Q \vec{\eta} = \vec{x}`, where :math:`\vec{x}` is a vector that is determined from the DoF values of a configuration.

.. admonition:: Examples

    - For occupant DoF, :math:`\vec{x}` represents sublattice concentrations in the supercell commensurate with all ordered structures of interest, for example:

      .. math::

          \vec{x} = [n_A^1, n_B^1, n_A^2, n_B^2, \dots],

      where :math:`n_X^j` is the concentration of species :math:`X` on sublattice :math:`j` in the supercell.
    - For displacement DoF using the standard basis, :math:`\vec{x}` represents the average values of each displacement component on a sublattice in the supercell commensurate with all ordered structures of interest, for example:

      .. math::

          \vec{x} = [\bar{dx}^1, \bar{dy}^1, \bar{dz}^1, \bar{dx}^2, \bar{dy}^2, \bar{dz}^2, \dots],

      where :math:`\bar{dx}^j,\bar{dy}^j,\bar{dj}^j` indicates the average of the :math:`x,y,z` displacement component, respectively, on sublattice :math:`j` in the supercell.
    - For strain DoF, :math:`\vec{x}` represents the strain DoF values, for example:

      .. math::

          \vec{x} = [E_{xx}, E_{yy}, E_{zz}, \sqrt{2}E_{yz}, \sqrt{2}E_{xz}, \sqrt{2}E_{xy}].


.. admonition:: Examples, non-standard prim basis

    It is also possible to define order parameters in terms of a non-standard prim basis:

    - For displacement DoF using a non-standard prim basis allowing two-dimensional displacements:

      .. math::

          \vec{x} = [\bar{d\alpha}^1, \bar{d\beta}^1, \bar{d\alpha}^2, \bar{d\beta}^2, \dots],

      where :math:`\bar{d\alpha}^j,\bar{d\beta}^j` indicates the average of the :math:`\alpha-,\beta-` displacement component, respectively, on sublattice :math:`j` in the supercell commensurate with all ordered structures of interest.
    - For strain DoF using a symmetry-adapted prim strain basis, excluding shear:

      .. math::

          \vec{x} = [e_1, e_2, e_3],

      where :math:`e_1, e_2, e_3` are symmetry-adapted strains (see :cite:t:`THOMAS2017a`):

      .. math::

          \vec{e} = \left( \begin{array}{ccc} e_1 \\ e_2 \\ e_3  \end{array} \right) = \left( \begin{array}{ccc} \left( E_{xx} + E_{yy} + E_{zz} \right)/\sqrt{3} \\ \left( E_{xx} - E_{yy} \right)/\sqrt{2} \\ \left( 2E_{zz} - E_{xx} - E_{yy} + \right)/\sqrt{6} \end{array} \right).




Symmetry operations transform the DoF values of a configuration and the effect :math:`\vec{x} \to \vec{x}'` can be represented by a symmetry representation matrix, :math:`M_i`, such that :math:`\vec{x}' = M_i \vec{x}`.

.. note::

    For each DoF type, there is a set of symmetry representation matrices, :math:`M_i`, which describe how the sublattice concentrations, average displacement components, and strain components, respectively, transform under symmetry operations.

Matrix representations, :math:`\tilde{M}_i`, that transform :math:`\vec{\eta}` according to :math:`\vec{\eta}' = \tilde{M}_i \vec{\eta}`, are calculated as :math:`\tilde{M}_i = Q^{-1} M_i Q`.

To calculate symmetry adapted order parameters, a DoFSpace should be constructed with a symmetry adapted basis, :math:`Q`, which block diagonalizes the matrix representations :math:`\tilde{M}_i` with the smallest possible blocks (the `irreducible representations`). A block-diagonal matrix representation can only transform vectors in the subspace corresponding to the block into other vectors in the same subspace. These are `invariant subspaces` under the application of the symmetry, and the order parameters in these subspaces can be used as input variables to symmetry adapted basis functions for properties of the configuration.

Construction of a symmetry adapted basis can be done using methods in `libcasm-configuration`, which provides support for determining and applying symmetry representations. This package provides the underlying methods that calculate :math:`\vec{\eta}` for a configuration (represented using :class:`~libcasm.clexulator.ConfigDoFValues`) given a particular choice of order parameter basis, :math:`Q`.

.. seealso::

    For more details on symmetry adapated order parameters, see :cite:t:`natarajan2017symmetry`. Note that the paper uses the convention :math:`\vec{\eta} = Q \vec{x}`, differing from the convention used here that :math:`Q` is the order parameter basis :math:`Q \vec{\eta} = \vec{x}`.


Global DoF
----------

For global DoF, each row of :math:`Q`, and corresponding element of :math:`\vec{x}`, is associated with one index:

- `dof_component_index`: The index of the relevant global DoF value component on that site

Each element of :math:`\vec{x}` is the `dof_component_index`-th component of the global DoF value.


Local DoF
---------

For local continuous DoF and occupant DoF, :class:`~libcasm.clexulator.DoFSpace` is constructed with a supercell transformation matrix, :math:`T`, defining a DoFSpace supercell which specifies the periodicity of the order parameter. The DoFSpace supercell lattice vectors, as a column vector matrix, are :math:`S = L T`, where :math:`L` are the prim lattice vectors, as a column vector matrix.

Each row of :math:`Q`, and corresponding element in :math:`\vec{x}`, is associated with two indices:

- `linear_site_index`: The index of the associated site in the DoFSpace supercell
- `dof_component_index`: The index of the relevant DoF value component on that site

.. note::

    The DoFSpace member function :func:`~libcasm.clexulator.DoFSpace.axis_info` can be used to lookup the mapping of DoF components to rows of :math:`Q`. For occupant DoF and local DoF, this includes the `linear_site_index` of the site where the DoF component is located.


For local continuous DoF, :math:`\vec{x}` is the average of the `dof_component_index`-th local DoF value component on sites belonging to the `linear_site_index`-th sublattice of the DoFSpace supercell.

For occupant DoF, :math:`\vec{x}` is the fraction sites belonging to the `linear_site_index`-th sublattice of the DoFSpace supercell that are occupied by the `dof_component_index`-th allowed occupant.

.. note::

    For occupant DoF, elements of :math:`\vec{x}` as defined here are not independent. The sum of elements of :math:`\vec{x}` with the same `linear_site_index` must be 1.


The OrderParameter calculator
-----------------------------

The class :class:`~libcasm.clexulator.OrderParameter` is used to calculate:

- the value of an order parameter
- the change in the value of an order parameter given changes in degree of freedom (DoF) values

To calculate the order parameter value, OrderParameter uses:

- :class:`~libcasm.clexulator.DoFSpace`: To specify the order parameter basis
- :class:`~libcasm.xtal.SiteIndexConverter`: To perform index conversions
- :class:`~libcasm.clexulator.ConfigDoFValues`: OrderParameter is given a pointer to a ConfigDoFValues instance and calculates the order parameters using the current state of the DoF values


Construction
------------

A :class:`~libcasm.clexulator.OrderParameter` calculator can be constructed as follows:

.. code-block:: Python

    import numpy as np
    import libcasm.xtal as xtal
    from libcasm.clexulator import (
        DoFSpace, OrderParameter,
    )

    # construct the Prim
    xtal_prim = # xtal.Prim(...)

    # specify the DoFSpace
    dof_space = # DoFSpace(...)

    # construct a OrderParameter calculator
    order_parameter = OrderParameter(
        dof_space=dof_space,
    )


Setup
-----

An OrderParameter instance is set to calculate order parameters in one supercell at a time, using the appropriate :class:`~libcasm.xtal.SiteIndexConverter`. This can be set or changed using the :func:`~libcasm.clexulator.OrderParameter.update` method.

Order parameters are calculated for a :class:`~libcasm.clexulator.ConfigDoFValues` instance that can be specified using the call operator, the :func:`~libcasm.clexulator.OrderParameter.update` method, or the :func:`~libcasm.clexulator.ClusterExpansion.set` method.

Default configuration degree of freedom (DoF) values in a particular supercell can be constructed and the OrderParameter instance updated to evaluate using those values as follows:

.. code-block:: Python

    import libcasm.xtal as xtal
    from libcasm.clexulator import (
        make_default_config_dof_values,
    )

    # specify the supercell:
    l_unitcells = # int(... specify a l * l * l unit cells sized supercell ...)
    transformation_matrix_to_super = np.eye(3) * l_unitcells

    # construct SiteIndexConverter
    site_index_converter = xtal.SiteIndexConverter(
        transformation_matrix_to_super=transformation_matrix_to_super,
        n_sublattice=len(xtal_prim.occ_dof()),
    )

    # construct ConfigDoFValues, in the prim DoF basis
    config_dof_values = make_default_config_dof_values(
        xtal_prim=xtal_prim,
        n_unitcells=round(np.linalg.det(transformation_matrix_to_super)),
    )

    # set the supercell and ConfigDoFValues used for order parameter calculation
    order_parameter.update(
        transformation_matrix_to_super=transformation_matrix_to_super,
        site_index_converter=site_index_converter,
        config_dof_values=config_dof_values,
    )

The `transformation_matrix_to_super` used to construct the :class:`~libcasm.clexulator.ConfigDoFValues` and :class:`~libcasm.xtal.SiteIndexConverter` instances and given as the :func:`~libcasm.clexulator.OrderParameter.update` argument defines the supercell of the configuration being evaluated and therefore must be the same.

However, it does not need to be the same supercell as the :class:`~libcasm.clexulator.DoFSpace` is defined with. When evaluating the order paramers, if the configuration supercell differs from the the :class:`~libcasm.clexulator.DoFSpace` supercell:

- For occupant DoF and local DoF, a commensurate supercell is determined internally and order parameters are evaluated as if the `config_dof_values` are tiled into the commensurate supercell.

- For global DoF, the supercell is irrelevant.


Evaluate the order parameters
-----------------------------

The variable `config_dof_values` returned by :func:`~libcasm.clexulator.make_cluster_expansion` is an instance of :class:`~libcasm.clexulator.ConfigDoFValues` which the :class:`~libcasm.clexulator.OrderParameter` calculator is set to evaluate.

Then, the cluster expansion value can be evaluated with:

.. code-block:: Python

    # evaluate and get current order parameters value:
    eta = order_parameter.value()

    # eta is a const reference of type numpy.ndarray[numpy.float64[d, 1]],
    # where d is the dimension of the DoFSpace basis (number of columns)

Internally, this uses `config_dof_values` to construct a vector, :math:`\vec{x}`, of the DoF values in the DoF space, averaged over the configuration supercell. Then, solving :math:`Q \vec{\eta} = \vec{x}` yields :math:`\vec{\eta}`.


Change DoF values and re-evaluate
---------------------------------

To change DoF values and re-calculate the cluster expansion, just modify the values of the :class:`~libcasm.clexulator.ConfigDoFValues` instance and re-evaluate. For a detailed discussion and some pitfalls, see the discussion :ref:`Change DoF values and re-evaluate <change-and-re-evaluate-clex>` for ClusterExpansion.

