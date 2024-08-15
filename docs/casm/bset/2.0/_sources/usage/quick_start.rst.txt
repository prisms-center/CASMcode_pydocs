
Quickstart
==========

.. attention::

    See the section on :ref:`environment variable configuration <casm-bset-configuration>` for compiling and linking the clexulator. Here we assume the environment has been configured before launching Python.


Construct ClexBasisSpecs
------------------------

A :class:`~casm.bset.cluster_functions.ClexBasisSpecs` object is used to define a cluster expansion basis set by specifying:

- :class:`~libcasm.clusterography.ClusterSpecs`: the cluster orbits to include in the basis set, and
- :class:`~casm.bset.cluster_functions.BasisFunctionSpecs`: the type and order of basis functions to construct.

Given a :class:`~libcasm.configuration.Prim`, :class:`~casm.bset.cluster_functions.ClexBasisSpecs` can be constructed directly, or more conveniently by using one of the following methods:

- using the :func:`casm.bset.make_clex_basis_specs` method.
- from a Python dict / JSON using the :func:`ClexBasisSpecs.from_dict <~casm.bset.cluster_functions.ClexBasisSpecs.from_dict>` method, using the format described `here <https://prisms-center.github.io/CASMcode_docs/formats/casm/clex/ClexBasisSpecs/>`_, or

Example 1: Construct a ClexBasisSpecs object using :func:`~casm.bset.make_clex_basis_specs`

.. code-block:: Python

    import libcasm.configuration as casmconfig
    import libcasm.xtal.prims as xtal_prims
    from casm.bset import make_clex_basis_specs

    # Construct a ternary FCC prim
    xtal_prim = xtal_prims.FCC(
        r=0.5,
        occ_dof=["A", "B", "C"],
    )
    prim = casmconfig.Prim(xtal_prim)

    # Construct ClexBasisSpecs
    # max_length: list[float] | None
    #     The maximum site-to-site distance by cluster size
    #     (i.e. null, point, pair, triplet clusters).
    #     For a periodic cluster expansion, the null and point
    #     distances are arbitrary and can be set to 0.0.
    # occ_site_basis_functions_specs: str | list[dict] | None
    #     Function type for occupation DoFs
    clex_basis_specs = make_clex_basis_specs(
        prim=prim,
        max_length=[0.0, 0.0, 3.01, 2.01],
        occ_site_basis_functions_specs="occupation",
    )


Example 2: Construct a ClexBasisSpecs object from a Python dict

.. code-block:: Python

    import libcasm.configuration as casmconfig
    import libcasm.xtal.prims as xtal_prims
    from casm.bset.cluster_functions import ClexBasisSpecs

    # Construct a ternary FCC prim
    xtal_prim = xtal_prims.FCC(
        r=0.5,
        occ_dof=["A", "B", "C"],
    )
    prim = casmconfig.Prim(xtal_prim)

    # Define a cluster expansion basis set using a Python dict
    clex_basis_specs = ClexBasisSpecs.from_dict(
        data={
            "basis_function_specs": {
                "dof_specs": {"occ": {"site_basis_functions": "occupation"}}
            },
            "cluster_specs": {
                "orbit_branch_specs": {
                    "0": {"max_length": 0.0},
                    "1": {"max_length": 0.0},
                    "2": {"max_length": 3.01},
                    "3": {"max_length": 2.01},
                },
            },
        },
        prim=prim,
    )


Write clexulator source code
----------------------------

CASM generates custom code for very efficient calculation of basis functions given a particular :class:`~libcasm.configuration.Prim` and choice of cluster expansion basis functions. This source code is written to a file and then may be compiled, linked, and used with the class :class:`~libcasm.clexulator.Clexulator` (clexulator = CLuster EXpansion calcULATOR). For more details, see `The CASM Clexulator <https://prisms-center.github.io/CASMcode_pydocs/libcasm/clexulator/2.0/usage/cluster_expansion_details.html#the-casm-clexulator>`_.

The method :func:`casm.bset.write_clexulator` takes :class:`~casm.bset.cluster_functions.ClexBasisSpecs` to specify the choice of cluster expansion basis functions, and writes the clexulator source code.

.. code-block:: Python

    import tempfile
    import numpy as np
    import libcasm.clexulator as casmclex
    from casm.bset import write_clexulator

    # Create a temporary directory to write the Clexulator source code
    # For example only - change this to a permanent directory
    tmp_dir = tempfile.TemporaryDirectory()
    bset_dir = tmp_dir.name

    # Write the Clexulator source code to `src_path`
    # bset_dir: pathlib.Path
    #     The directory to write the Clexulator source file
    # src_path: pathlib.Path
    #    The path to the Clexulator source file (or a
    #    prototype Clexulator source file if a local cluster
    #    expansion).
    # local_src_path: Optional[list[pathlib.Path]]
    #    If a local cluster expansion, the paths to the local
    #    Clexulator source files.
    # prim_neighbor_list: libcasm.clexulator.PrimNeighborList
    #    The neighbor list for the prim
    src_path, local_src_path, prim_neighbor_list = write_clexulator(
        prim=prim,
        clex_basis_specs=clex_basis_specs,
        bset_dir=bset_dir,
        project_name="TestProject",
        bset_name="default",
    )


Compiling and constructing a clexulator
---------------------------------------

Once written, the clexulator can be compiled and linked using :func:`make_clexulator` to construct a :class:`~libcasm.clexulator.Clexulator` object.

.. code-block:: Python

    # Compile and construct a clexulator
    clexulator = casmclex.make_clexulator(
        source=str(src_path),
        prim_neighbor_list=prim_neighbor_list,
    )


Evaluating correlations
-----------------------

Once a :class:`~libcasm.clexulator.Clexulator` object is constructed, it can be used to evaluate correlations (per unitcell average values of symmetrically equivalent cluster functions for a particular configuration) using the :class:`~libcasm.clexulator.Correlations` calculator.

.. code-block:: Python

    # Construct a Supercell (conventional FCC cubic cell)
    supercell = casmconfig.Supercell(
        prim=prim,
        transformation_matrix_to_super=np.array(
            [
                [-1, 1, 1],
                [1, -1, 1],
                [1, 1, -1],
            ],
            dtype="int",
        ),
    )

    # Construct a neighbor list for the supercell
    supercell_neighbor_list = casmclex.SuperNeighborList(
        supercell.transformation_matrix_to_super,
        prim_neighbor_list,
    )

    # Construct a default Configuration (with [A, B, B, C] occupation)
    config = casmconfig.Configuration(supercell)
    config.set_occupation([0, 1, 1, 2])

    # Construct a correlations calculator, pointed at `config`'s DoF values
    corr = casmclex.Correlations(
        supercell_neighbor_list=supercell_neighbor_list,
        clexulator=clexulator,
        config_dof_values=config.dof_values,
    )

    # Evaluate the correlations
    # correlation_values: np.ndarray, the correlation values
    corr_per_supercell = corr.per_supercell()
    corr_per_unitcell = corr.per_unitcell(corr_per_supercell)


Evaluating a cluster expansion
------------------------------

Cluster expansion coefficients are obtained by fitting to the calculated energy of configurations in a set of training data (see :cite:t:`CASM`),

.. math::

    \newcommand{\config}{{\mathbb{C}}}
    \begin{pmatrix}
    e(\config_{1}) \\
    .  \\
    . \\
    . \\
    e(\config_{I})  \\
    .  \\
    . \\
    . \\
    e(\config_{M})
    \end{pmatrix}
    =
    \begin{pmatrix}
    \Gamma_{\alpha}^1(\config_{1}) & ... &  \Gamma_{\gamma}^n(\config_{1}) & ... &\\
    .  \\
    . \\
    . \\
    \Gamma_{\alpha}^1(\config_{I}) & ... &  \Gamma_{\gamma}^n(\config_{I}) & ... &\\
    .  \\
    . \\
    . \\
    \Gamma_{\alpha}^1(\config_{M}) & ... & \Gamma_{\gamma}^n(\config_{M}) & ... &
    \end{pmatrix}
    \begin{pmatrix}
    m_{\alpha}^1V_{\alpha}^1 \\
    .  \\
    .  \\
    .  \\
    m_{\gamma}^nV_{\gamma}^n \\
    . \\
    .  \\
    .  \\
    \end{pmatrix},

where:

- :math:`\config_{I}` is the `I`-th configuration in the training data,
- :math:`e(\config_{I})` is its formation energy per unitcell of :math:`\config_{I}`,
- :math:`\Gamma_{\gamma}^n(\config_{I})` is the correlation value for the cluster functions with indices :math:`(\gamma,n)` evaluated for configuration :math:`\config_{I}`,

  - the subscript, :math:`\gamma`, is the "linear orbit index", an index representing symmetrically distinct clusters,
  - the superscript, :math:`n`, is an index representing independent and symmetry allowed cluster functions (i.e. a ternary cluster expansion has multiple independent cluster functions per cluster),
  - CASM uses a "linear function index", to specify :math:`(\gamma,n)` with a single index,

- :math:`m_{\gamma}^n` is the multiplicity (number per unitcell) of cluster functions with indices :math:`(\gamma,n)`, and
- :math:`V_{\gamma}^n` is the coefficient value (per cluster function) for the cluster functions with indices :math:`(\gamma,n)`.

Once cluster expansion coefficients are obtained, the non-zero :math:`m_{\gamma}^n V_{\gamma}^n` values can be stored in a :class:`~libcasm.clexulator.SparseCoefficients` object. Then, the :class:`~libcasm.clexulator.Clexulator` and :class:`~libcasm.clexulator.SparseCoefficients` can be used to evaluate the cluster expansion using the :class:`~libcasm.clexulator.ClusterExpansion` class.

.. code-block:: Python

    # Construct a SparseCoefficients object
    # from basis function indices and coefficients (using the m * V values)
    formation_energy_coefficients = casmclex.SparseCoefficients(
        index=[0, 1, 3],  # linear function indices
        value=[-1.0, -0.1, 0.02],  # coefficients, using the m * V values
    )

    # Construct a cluster expansion calculator,
    # pointed at `config`'s DoF values
    clex = casmclex.ClusterExpansion(
        supercell_neighbor_list=supercell_neighbor_list,
        clexulator=clexulator,
        coefficients=formation_energy_coefficients,
        config_dof_values=config.dof_values,
    )

    # Evaluate the cluster expansion
    # for the configuration with its current occupation
    clex_formation_energy_per_unitcell = clex.per_unitcell()


Unless it is reset, the :class:`~libcasm.clexulator.ClusterExpansion` calculator will continue calculating the cluster expansion for the same configuration. The configuration can be modified and then :func:`~libcasm.clexulator.ClusterExpansion.per_unitcell` called again to evaluate the cluster expansion for the modified configuration.

.. code-block:: Python

    # Change the occupation of the configuration (to [B, A, B, C])
    config.set_occupation([1, 0, 1, 2])

    # Evaluate the cluster expansion
    # for the configuration with its current occupation
    clex_formation_energy_per_unitcell = clex.per_unitcell()


Calculate the effect of changes in DoF values
---------------------------------------------

The :class:`~libcasm.clexulator.Correlations` and :class:`~libcasm.clexulator.ClusterExpansion` calculators also have methods to efficiently evaluate the change in correlation values or cluster expansion values *per supercell* for a proposed change in degree of freedom (DoF) values, as would be needed for a Monte Carlo simulation. There are separate methods for evaluating the effect of changing:

- one occupation DoF value,
- multiple occupation DoF values,
- local continuous DoF values on one site (i.e. displacements), or
- global continuous DoF values (i.e. strain).

.. code-block:: Python

    # Get the change in the cluster expansion *per-supercell* value
    # for a proposed change in the occupation on one site,
    # leaving the occupation unchanged.
    assert config.occ(2) == 1  # B

    delta_clex_formation_energy_per_supercell = clex.occ_delta_value(
        linear_site_index=2,
        new_occ=0,  # A
    )

    assert config.occ(2) == 1  # B


More details about the clexulator, and correlation and cluster expansion calculations,
including evaluating local correlations and local cluster expansions, can be found in the `libcasm-clexulator documentation <https://prisms-center.github.io/CASMcode_pydocs/libcasm/clexulator/2.0/index.html>`_.