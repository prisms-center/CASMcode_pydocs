
Basis function specifications
=============================

The :class:`~casm.bset.cluster_functions.BasisFunctionSpecs` class is used to specify the type and order of basis functions that CASM generates.

Selection of degrees of freedom (DoF)
-------------------------------------

By default, all DoF present in the prim are coupled. The `dofs` parameter allows
specifying that only the given subset of DoFs should be included in the basis
functions.

.. code-block:: Python

    from casm.bset.cluster_functions import BasisFunctionSpecs

    basis_functions_specs = BasisFunctionSpecs(
        dofs=["occ"],
    )


.. _sec-dof-specifications:

DoF specifications
------------------

The occupation and magnetic spin DoF types require specific parameterization using the `dof_specs` parameter, which takes the form of a dictionary with keys corresponding to DoF types. For example:

.. code-block:: Python

    dof_spces = {
        "occ": {
            "site_basis_functions": "occupation"
        }
    }


.. code-block:: Python

    dof_spces = {
        "NCmagspin": {
            "max_poly_order": 5
        }
    }


Occupation site basis functions
-------------------------------

Formulation
^^^^^^^^^^^

For occupation DoF ("occ"), the `dof_specs` parameter is used to specify the value
of the discrete site basis functions. The following is a summary of the formulation
described in detail in `TODO <todo>`_.

The occupation site basis functions are specified in terms of the site occupation index, :math:`s_\nu`, which takes on the values :math:`0, 1, \dots, m-1`, where :math:`m` is the number of allowed occupants on the site.

It is convenient to define the particular choice of occupation site basis functions
using occupation indicator functions, :math:`p^j_\nu`, as a common reference. The
occupation indicator functions take on value :math:`1` when site :math:`\nu` is
occupied by the :math:`j`-th allowed occupant, and :math:`0` otherwise.

For example, site :math:`\nu` in a binary A-B alloy would have two indicator
functions, :math:`p^A_\nu` and :math:`p^B_\nu`, though only one is independent, since
:math:`p^A_\nu + p^B_\nu = 1`. Similarly, a ternary A-B-C alloy would have :math:`p^A_\nu + p^B_\nu + p^C_\nu = 1`.

Generalizing, each site has a vector of indicator functions, :math:`\vec{p}`, and
we consider site basis functions :math:`\vec{\varphi}` that can be linearly combined to give the indicator functions according to

.. math::

    \vec{p} = \pmb{B} \vec{\varphi},

with equivalent matrix representation

.. math::

    \pmb{P} = \pmb{B} \pmb{\varphi},

where :math:`\pmb{P}` and :math:`\pmb{\varphi}` are :math:`m \times m` matrices with
element :math:`ij` being the value of the :math:`i`-th function when the site
occupation index is the :math:`j`-th possible value. By definition, :math:`\pmb{P}` is
the :math:`m \times m` identity matrix, and therefore
:math:`\pmb{B} = \left( \pmb{\varphi} \right)^{-1}`.

Cluster expansion basis functions are then constructed as polynomials with the site basis
functions, :math:`\varphi_i`, at each site in the cluster included as variables.


.. rubric:: Example, binary spin occupation variables

A common choice for occupation variables in a binary alloy cluster expansion are
:math:`\varphi(A)=-1` and :math:`\varphi(B)=1`. Using the above notation, this is

.. math::

    \pmb{P} = \begin{bmatrix}
    1 & 0\\
    0 & 1
    \end{bmatrix},
    \pmb{\varphi} = \begin{bmatrix}
    1 & 1\\
    -1 & 1
    \end{bmatrix},

where the constant function :math:`\varphi_1(s_j)=1` that allows cluster expansion
is explicitly included and :math:`\varphi_2(s_j)` is a site basis function
with values equal to the spin occupation variables (:math:`\varphi_2(A)=-1`, :math:`\varphi_2(B)=1`).

.. rubric:: Example, "occupation" site basis variables

For a binary alloy, the "occupation" site basis variables take the form :math:`\varphi(A)=0` and :math:`\varphi(B)=1`. In general, using the above notation this becomes :math:`\varphi_{1} = 1` and :math:`\varphi_{i \neq 1}(s_j) = \delta_{ij}`. For a ternary alloy cluster expansion this is

.. math::

    \pmb{P} = \begin{bmatrix}
    1 & 0 & 0\\
    0 & 1 & 0\\
    0 & 0 & 1\\
    \end{bmatrix},
    \pmb{\varphi} = \begin{bmatrix}
    1 & 1 & 1\\
    0 & 1 & 0\\
    0 & 0 & 1
    \end{bmatrix}.

Symmetry
^^^^^^^^

The indicator variables transform under symmetry according to

.. math::
    :label: indicator_transformation

    \left(\vec{p}_{\nu}\right)' = \pmb{M}_{b^{\ast}}(\hat{s}) \vec{p}_{\nu^{\ast}},


where:

- :math:`\hat{s}` is a space group operation
- :math:`\vec{p}_{\nu^{\ast}}` is the vector of indicator variables on site :math:`\nu^{\ast}`, which is on sublattice :math:`b^{\ast}`, before transformation by :math:`\hat{s}`,
- :math:`\left(\vec{p}_{\nu}\right)'` is the vector of indicator variables on site :math:`\nu`, which is on sublattice :math:`b`, after transformation by :math:`\hat{s}`,
- :math:`\pmb{M}_{b^{\ast}}(\hat{s})` is the matrix representation of :math:`\hat{s}` used to transform indicator variables occupying sites on sublattice :math:`b^{\ast}`.

The matrix representations :math:`\pmb{M}_{b^{\ast}}(\hat{s})` for transforming indicator variables by factor group operations is determined when constructing a :class:`libcasm.configuration.Prim` by transforming each allowed occupant on :math:`b^{\ast}` and finding the permutation (if it exists) that results in the allowed occupants on :math:`b`. These matrix representations can be accessed using :py:meth:`Prim.local_dof_matrix_rep <libcasm.configuration.Prim.local_dof_matrix_rep>` with

.. code-block:: Python

    M = prim.local_dof_matrix_rep("occ")[i_factor_group][i_sublat]

where `i_factor_group` is the index of the factor group operation that differs from :math:`\hat{s}` by only a translation (i.e. index in ``prim.factor_group.elements``), and `i_sublat` is the index of sublattice :math:`b^{\ast}` (i.e. column index in ``prim.xtal_prim.coordinate_frac()``).

Notes:

- :math:`\pmb{M}_{b^{\ast}}(\hat{s})` is a row permutation matrix. It permutes the rows of the column vector :math:`\vec{p}_{b^{\ast}}` consistent with the order the transformed occupants are listed  as allowed occupants on the final site.
- The transpose of a permutation matrix is equal to its inverse.
- The inverse of a row permutation matrix is the column permutation matrix which permutes columns in the same cycle that the row permutation matrix permutes rows.
- Therefore, :math:`\pmb{M}_{b^{\ast}}(\hat{s})^{\top}` is a column permutation matrix which can permute the columns of :math:`\pmb{\varphi}_{b^{\ast}}` (which correspond to occupation index) to give symmetrically consistent site basis functions on :math:`b` according to :math:`\pmb{\varphi}_{b} = \pmb{\varphi}_{b^{\ast}} \pmb{M}_{b^{\ast}}(\hat{s})^{\top}`.

Matrix representations, :math:`\pmb{\tilde{M}}_{b^{\ast}}(\hat{s})`, for transforming site basis functions, :math:`\vec{\varphi}`, can be determined in terms of :math:`\pmb{M}_{b^{\ast}}(\hat{s})`. First, write :math:`\vec{\varphi}` before and after transformation in terms of :math:`\vec{p}` as

.. math::

    \begin{align}
    \vec{p}_{\nu^{\ast}} &= \pmb{B}_{\nu^{\ast}} \vec{\varphi}_{\nu^{\ast}}, \\
    \left(\vec{p}_{\nu}\right)' &= \pmb{B}_{\nu} \left(\vec{\varphi}_{\nu}\right)'.
    \end{align}

Then substitute into Eq. :eq:`indicator_transformation` to obtain

.. math::

    \pmb{B}_{\nu} \left(\vec{\varphi}_{\nu}\right)' = \pmb{M}_{b^{\ast}}(\hat{s}) \pmb{B}_{\nu^{\ast}} \vec{\varphi}_{\nu^{\ast}}.

Finally, left multiply by :math:`\pmb{B}_{\nu}^{-1}` and use :math:`\pmb{B} = \left( \pmb{\varphi} \right)^{-1}` to obtain

.. math::
    :label: occ_site_func_transformation

    \begin{align}
    \left(\vec{\varphi}_{\nu}\right)' &= \pmb{\tilde{M}}_{b^{\ast}}(\hat{s}) \vec{\varphi}_{\nu^{\ast}}, \\
    \pmb{\tilde{M}}_{b^{\ast}}(\hat{s}) &= \pmb{\varphi}_{\nu} \pmb{M}_{b^{\ast}}(\hat{s}) \pmb{\varphi}_{\nu^{\ast}}^{-1}.
    \end{align}



Specification
^^^^^^^^^^^^^

Chebychev occupation site basis functions
"""""""""""""""""""""""""""""""""""""""""

Chebychev site basis functions give an expansion (with correlation values all equal to `0`) about the idealized random alloy where the probability of any of the allowed occupants on a particular site is the same. This choice of occupation site basis functions can be specified with:

.. code-block:: Python

    basis_function_specs = BasisFunctionSpecs(
        dof_specs={
            "occ": {
                "site_basis_functions": "chebychev"
            }
        }
    )


For a binary alloy, the Chebychev site basis functions used by CASM have the value:

.. math::

    \pmb{\varphi} = \begin{bmatrix}
    1 & 1\\
    -1 & 1
    \end{bmatrix}.

For a ternary alloy, the Chebychev site basis functions used by CASM have the value:

.. math::

    \pmb{\varphi} = \begin{bmatrix}
    1 & 1 & 1\\
    -3/\sqrt{6} & 0 & -3/\sqrt{6} \\
    -\sqrt{2}/2 & \sqrt{2} & -\sqrt{2}/2
    \end{bmatrix}.



Occupation site basis functions
"""""""""""""""""""""""""""""""

The "occupation" site basis functions give an expansion (with correlation values all equal to `0`) about the default configuration where each site is occupied by the first allowed occupant in the :py:meth:`Prim.occ_dof <libcasm.xtal.Prim.occ_dof>` list. This choice of occupation site basis functions can be specified with:

.. code-block:: Python

    basis_function_specs = BasisFunctionSpecs(
        dof_specs={
            "occ": {
                "site_basis_functions": "occupation"
            }
        }
    )


For a binary alloy, the "occupation" site basis functions used by CASM have the value:

.. math::

    \pmb{\varphi} = \begin{bmatrix}
    1 & 1\\
    0 & 1
    \end{bmatrix}.

For a ternary alloy, the "occupation" site basis functions used by CASM have the value:

.. math::

    \pmb{\varphi} = \begin{bmatrix}
    1 & 1 & 1\\
    0 & 1 & 0\\
    0 & 0 & 1
    \end{bmatrix}.


Composition-centered site basis functions
"""""""""""""""""""""""""""""""""""""""""

The composition-centered site basis functions give an expansion (with correlation values all equal to `0`) for the idealized random configuration with a particular composition on each sublattice. The formulation is described in the documentation for the function :func:`~casm.bset.cluster_functions.make_orthonormal_discrete_functions`. The sublattice compositions can be specified using an array of dict, with the attributes:

- "composition": dict[str, float], Species the sublattice composition, using the
  the occupant names as keys (matching the prim occupants dictionary,
  :func:`~libcasm.xtal.Prim.occupants`), and sublattice composition as values. The
  values on a sublattice must sum to 1.0.

- "sublat_indices": list[int], Specifies the sublattices for which the composition
  values apply.

Example:

.. code-block:: Python

    basis_function_specs = BasisFunctionSpecs(
        dof_specs={
            "occ": {
                "site_basis_functions": [
                    {
                        "sublat_indices": [0, 1],
                        "composition": {"A": 0.25, "B": 0.75},
                    },
                    {
                        "sublat_indices": [2, 3],
                        "composition": {"A": 0.75, "B": 0.25},
                    }
                ]
            }
        },
    )


Only one sublattice in each asymmetric unit with >1 allowed occupant is required to be given. The specified composition is used to construct discrete basis functions on one site and then symmetry is used to construct an equivalent basis on other sites in the asymmetric unit.

For anisotropic occupants there may be multiple ways consistent with the prim factor group to construct the site basis functions on other sites (i.e. the choice of which spin state or molecular orientation of an occupant gets site basis function values of -1 or +1 may be arbitrary as long as it is done consistently). The particular choice made is based on the order in which symmetry operations are sorted in the prim factor group and should be consistent for a particular choice of prim.

An exception will be raised if sublattices in different asymmetric units are incorrectly grouped, or if no site is given for an asymmetric unit with >1 allowed occupant.


Directly-set site basis functions
"""""""""""""""""""""""""""""""""

.. warning::

    With this method it is possible to incorrectly use site basis functions
    that are not consistent with the symmetry of the prim. It should be
    considered a feature for developers and advanced users who understand
    how to check the results.

The site basis functions can be directly specified on each sublattice using an array of dict, with the attributes:

- "value": list[list[float], Species the site basis function values, :math:`\varphi_{ms}`,
  where the row index, :math:`m`, corresponds to a function index, and the column, :math:`s`,
  is the site occupation index. One row must be the vector of ones.

- "sublat_indices": list[int], Specifies the sublattices for which the site basis
  function values apply.

Example:

.. code-block:: Python

    basis_function_specs = BasisFunctionSpecs(
        dof_specs={
            "occ": {
                "site_basis_functions": [
                    {
                        "sublat_indices": [0, 1],
                        "value": [
                            [1., 1., 1.],
                            [0., 1., 0.],
                            [0., 0., 1.],
                        ]
                    },
                    {
                        "sublat_indices": [2, 3],
                        "value": [
                            [0., 1., 0.],
                            [0., 0., 1.],
                            [1., 1., 1.],
                        ]
                    }
                ]
            }
        },
    )



Magnetic spin site basis functions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. tip::

    DoF specs as described in this section are planned but not yet implemented.

For magnetic spin DoF ("<flavor>magspin"), the `dof_specs` parameter is used to
specify the maximum order of spherical harmonic site basis functions.

CASM generates spherical harmonic site basis functions of magnetic spin
continuous DoFs. The maximum polynomial order of the site basis functions is given by the
`max_poly_order` parameter.

Example:

.. code-block:: Python

    basis_function_specs = BasisFunctionSpecs(
        dof_specs={
            "NCmagspin": {
                "max_poly_order": 5
            }
        },
    )


Maximum polynomial order
------------------------

By default, for a given cluster orbit, polynomials of order up to the
cluster size are created. For functions of continuous degrees of freedom (DoF),
higher order polynomials are usually necessary and can be requested either on a
per-orbit-branch or global basis, where an "orbit branch" is the set of cluster
orbits with the same number of sites per cluster. The most specific level specified
is used. Orbit branches are specified using the string value of the cluster
size as a key.


Examples
--------

Occupation DoF
^^^^^^^^^^^^^^

Example: Chebychev site basis functions
"""""""""""""""""""""""""""""""""""""""

.. code-block:: Python

    basis_function_specs = BasisFunctionSpecs(
        dof_specs={
            "occ": {
                "site_basis_functions": "chebychev",
            }
        },
    )



Example: Occupation site basis functions
""""""""""""""""""""""""""""""""""""""""

.. code-block:: Python

    basis_function_specs = BasisFunctionSpecs(
        dof_specs={
            "occ": {
                "site_basis_functions": "occupation",
            }
        },
    )


Example: Composition-centered site basis functions
""""""""""""""""""""""""""""""""""""""""""""""""""

.. code-block:: Python

    basis_function_specs = BasisFunctionSpecs(
        dof_specs={
            "occ": {
                "site_basis_functions": [
                    {
                        "sublat_indices": [0, 1],
                        "composition": {"A": 0.25, "B": 0.75},
                    },
                    {
                        "sublat_indices": [2, 3],
                        "composition": {"A": 0.75, "B": 0.25},
                    }
                ]
            }
        },
    )


Example: Directly set site basis function values
""""""""""""""""""""""""""""""""""""""""""""""""

.. warning::

    With this method it is possible to incorrectly use site basis functions
    that are not consistent with the symmetry of the prim. It should be
    considered a feature for developers and advanced users who understand
    how to check the results.

.. code-block:: Python

    basis_function_specs = BasisFunctionSpecs(
        dof_specs={
            "occ": {
                "site_basis_functions": [
                    {
                        "sublat_indices": [0, 1],
                        "value": [
                            [1., 1., 1.],
                            [0., 1., 0.],
                            [0., 0., 1.],
                        ]
                    },
                    {
                        "sublat_indices": [2, 3],
                        "value": [
                            [0., 1., 0.],
                            [0., 0., 1.],
                            [1., 1., 1.],
                        ]
                    }
                ]
            }
        },
    )



Strain DoF
^^^^^^^^^^

Example: Hencky strain DoF
""""""""""""""""""""""""""

With maximum polynomial order 5:

.. code-block:: Python

    basis_function_specs = BasisFunctionSpecs(
        max_poly_order=5,
    )


Strain and occupation DoF
^^^^^^^^^^^^^^^^^^^^^^^^^

Example: Coupled strain and occupation DoF (1)
""""""""""""""""""""""""""""""""""""""""""""""

With:

- "occupation" site basis functions for the discrete occupants
- maximum polynomial order 5, which includes both strain and discrete DoF variables
  for the cluster functions.

.. code-block:: Python

    basis_function_specs = BasisFunctionSpecs(
        dofs=["occ"],
        dof_specs={
            "occ": {
                "site_basis_functions": "occupation",
            }
        },
        global_max_poly_order=5,
    )


Example: Coupled strain and occupation DoF (2)
""""""""""""""""""""""""""""""""""""""""""""""

With:

- "occupation" site basis functions for the discrete occupants
- global maximum polynomial order 5, which includes both strain and discrete DoF variables
  for the cluster functions.
- point- and pair-cluster branch maximum polynomial order 7,
- null-cluster (strain-only) maximum polynomial order 8.

.. code-block:: Python

    basis_function_specs = BasisFunctionSpecs(
        dofs=["occ"],
        dof_specs={
            "occ": {
                "site_basis_functions": "occupation",
            }
        },
        global_max_poly_order=5,
        orbit_branch_max_poly_order={
            "0": 8,
            "1": 7,
            "2": 7,
        }
    )
