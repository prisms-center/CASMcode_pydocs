.. _eval-local-cluster-expansion:

Evaluating a local cluster expansion
====================================

Construct a LocalClusterExpansion
---------------------------------

By setting the `cluster_expansion_type` parameter to `"local"`, :func:`~libcasm.clexulator.make_cluster_expansion` can also be used to construct a :class:`~libcasm.clexulator.LocalClusterExpansion` calculator:

.. code-block:: Python

    # construct a LocalClusterExpansion calculator
    local_cluster_expansion, info = make_cluster_expansion(
        cluster_expansion_type="local",
        xtal_prim=xtal_prim,
        clexulator_source=clexulator_source,
        coefficients=coefficients,
        transformation_matrix_to_super=transformation_matrix_to_super,
    )

    # get the ConfigDoFValues that cluster_expansion will evaluate
    config_dof_values = info.config_dof_values



For local cluster expansion construction, `clexulator_source` is the path to the :ref:`prototype local clexulator source code file <local-clexulator-files>` (i.e. `/path/to/<primname>_Clexulator_<bsetname>.cc`).

.. note::

    For local cluster expansions, clexulator source code is written for each symmetrically equivalent local basis set, which are stored indexed files relatives to the prototype local basis set. For more details see, :ref:`Cluster Expansion Details <cluster-expansion-details>`.


Evaluate the local cluster expansion
------------------------------------

The local cluster expansion value can be evaluated at a particular choice of:

- `unitcell_index`: Linear unit cell index specifying in which unit cell to evaluate the local correlations, as defined by :class:`~libcasm.xtal.UnitCellIndexConverter`.
- `equivalent_index`: Index of the equivalent local basis set to evaluate. This index specifies a combined choice of (i) a particular `phenomenal` cluster of sites and (ii) a particular orientation of the local basis set at that cluster, when there are multiple symmetrically equivalent local basis sets around the same phenomenal cluster.

For a particular application, there should be a method that helps find the correct `unitcell_index` and `equivalent_index`, which depends on the details of the phenomena being modeled.

For example, :class:`~libcasm.occ_events.OccEvent` is a class which describes a diffusive hop of one or more occupants. A local cluster expansion can be used to predict the kinetically resolved activation barrier around a particular hop. The correct `unitcell_index` and `equivalent_index` to use for evaluating the local cluster expansion around a particular hop in a particular supercell can be found using the method :func:`~libcasm.enumerate.get_occevent_coordinate`, which searches a list of phenomenal :class:`~libcasm.occ_events.OccEvent` to find which one is a strictly translational equivalent:

.. code-block:: Python

    from libcasm.enumerate import get_occevent_coordinate

    # ... calculate local cluster expansion value for the current state
    #     of the ConfigDoFValues that local_cluster_expansion has been set with ...

    # evaluate and return local cluster expansion value:
    unitcell_index, equivalent_index = get_occevent_coordinate(
        occ_event=occ_event, # libcasm.occ_events.OccEvent
        phenomenal_occevent=phenomenal_occevent, # List[libcasm.occ_events.OccEvent]
        supercell=supercell, # libcasm.configuration.Supercell
    )
    value = local_cluster_expansion.value(unitcell_index, equivalent_index)


