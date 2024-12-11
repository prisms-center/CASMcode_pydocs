.. _cluster-expansion-index:
.. _eval-cluster-expansion:

Evaluating a cluster expansion
==============================

The :class:`~libcasm.clexulator.ClusterExpansion` class is used to calculate:

- the value of a cluster expansion
- the change in the value of a cluster expansion given changes in degree of freedom (DoF) values

Evaluating a cluster expansion requires:

- Cluster expansion basis functions, as a CASM :class:`~libcasm.clexulator.Clexulator` source code file
- Cluster expansion coefficients, as a :class:`~libcasm.clexulator.SparseCoefficients` object
- Configuration degree of freedom (DoF) values, as a :class:`~libcasm.clexulator.ConfigDoFValues` object
- A neighbor list for the supercell of the configuration, which is used to find the correct degree of freedom (DoF) values for each basis function, as a :class:`~libcasm.clexulator.SuperNeighborList`.


Construct a ClusterExpansion
----------------------------

The :func:`~libcasm.clexulator.make_cluster_expansion` method can be used to construct a :class:`~libcasm.clexulator.ClusterExpansion` calculator along with consistent :class:`~libcasm.clexulator.ConfigDoFValues` and :class:`~libcasm.clexulator.SuperNeighborList` objects, given a :class:`~libcasm.xtal.Prim` and the integer transformation matrix for the supercell containing the configuration:

.. note::

    Before running, set the environment variable ``CASM_PREFIX`` to the location where CASM is installed in order to enable proper compilation and linking of the CASM clexulator using the CASM libraries.

    .. code-block:: bash

        export CASM_PREFIX=$(python -m libcasm.casmglobal --prefix)


    In some cases, finer control of compilation and linking options may be necessary, which can be done as described in the :class:`~libcasm.clexulator.make_clexulator` documentation. For example, compiling and linking on some Linux distros may require one or both of the "-D_GLIBCXX_USE_CXX11_ABI=0" and "--no-as-needed" flags. These can be set in the environment variables ``CASM_CXXFLAGS`` and ``CASM_SOFLAGS`` respectively:

    .. code-block:: bash

        export CASM_CXXFLAGS="-O3 -Wall -fPIC --std=c++17 -D_GLIBCXX_USE_CXX11_ABI=0 "

    .. code-block:: bash

        export CASM_SOFLAGS="-shared -Wl,--no-as-needed"


.. code-block:: Python

    import numpy as np
    import libcasm.xtal as xtal
    from libcasm.clexulator import (
        make_cluster_expansion,
        SparseCoefficients,
    )

    # construct the Prim
    xtal_prim = # xtal.Prim(...)

    # specify the clexulator source file
    clexulator_source = # str( ... path to clexulator source code file ... )

    # specify the cluster expansion coefficients
    coefficients = # SparseCoefficients(...)

    # specify the supercell:
    l_unitcells = # int(... specify a l * l * l unit cells sized supercell ...)
    transformation_matrix_to_super = np.eye(3) * l_unitcells

    # construct a ClusterExpansion calculator
    cluster_expansion, info = make_cluster_expansion(
        xtal_prim=xtal_prim,
        clexulator_source=clexulator_source,
        coefficients=coefficients,
        transformation_matrix_to_super=transformation_matrix_to_super,
    )

    # get the ConfigDoFValues that cluster_expansion will evaluate
    config_dof_values = info.config_dof_values


In this example, the :func:`~libcasm.clexulator.make_cluster_expansion` method returns two objects:

- `cluster_expansion`: The constructed :class:`~libcasm.clexulator.ClusterExpansion` calculator
- `info`: A :class:`~libcasm.clexulator.ClusterExpansionInfo` instance which provides the supercell neighbor list and a default initialized :class:`~libcasm.clexulator.ConfigDoFValues` instance for the specified supercell


Evaluate the cluster expansion
------------------------------

To evaluate the cluster expansion, the :class:`~libcasm.clexulator.ClusterExpansion` calculator must be set to point at a :class:`~libcasm.clexulator.ConfigDoFValues` instance. This is done automatically for the objects returned by :func:`~libcasm.clexulator.make_cluster_expansion`.

Then, the cluster expansion value can be evaluated with:

.. code-block:: Python

    # ... calculate cluster expansion value for the current state
    #     of the ConfigDoFValues that cluster_expansion has been set with ...

    # evaluate and return per_supercell value:
    value_per_supercell = cluster_expansion.per_supercell()

    # or evaluate and return the per_unitcell value:
    value_per_unitcell = cluster_expansion.per_unitcell()


.. _change-and-re-evaluate-clex:

Change DoF values and re-calculate
----------------------------------

To change DoF values and re-calculate the cluster expansion, just modify the values of the :class:`~libcasm.clexulator.ConfigDoFValues` instance and re-evaluate:

.. code-block:: Python

    # Correct usage -->
    # The following all assign new DoF values to
    # `config_dof_values`'s data using a copy:

    # ... set ConfigDoFValues occupant values, using a copy...
    occupation = np.array(...)
    config_dof_values.set_occupation(occupation)

    # ... or set a single ConfigDoFValues occupant value, using a copy...
    linear_site_index = int(...)
    new_occ = int(...)
    config_dof_values.set_occ(linear_site_index, new_occ)

    # ... or set all ConfigDoFValues values from other
    #     ConfigDoFValues instance, using copies ...
    other = ConfigDoFValues(...)
    config_dof_values.set(other)

    # ... calculate cluster expansion value for the current state
    #     of the ConfigDoFValues that cluster_expansion has been set with ...

    # evaluate and return the per_unitcell value:
    value_per_unitcell = cluster_expansion.per_unitcell()

.. warning::

    Wrong usage, trying to change DoF values, but actually changing what a Python variable points to:

    .. code-block:: Python

        # !Wrong usage! -->
        # In Python, the following will change what `config_dof_values`
        # points at, not assign values to `config_dof_values`'s data
        # using a copy.
        # Therefore, cluster_expansion, which still has a pointer to the
        # original data structure, will not use the DoF values from `other`.
        other = ConfigDoFValues(...)
        config_dof_values = other

        # ... this will try to calculate cluster expansion value
        #     but still points at the original `config_dof_values` data ...

        # ... so it will evaluate and return the wrong per_unitcell value ...
        wrong_value_per_unitcell = cluster_expansion.per_unitcell()

.. warning::

    Wrong usage, trying to change DoF values using an external data structure which was copied:

    .. code-block:: Python

        # !Wrong usage! -->
        # ConfigDoFValues.set_occuption copies values of an occupation array
        # into the  `config_dof_values`'s data, so subsequently changing the
        # values in the array has no effect on `config_dof_values`

        occupation = np.array(...)

        # ... this copies `occupation` values into `config_dof_values`'s data ...
        config_dof_values.set_occupation(occupation)

        # ... so this has no effect on `config_dof_values`'s data ...
        occupation[linear_site_index] = new_occ

        # ... and it will evaluate and return the wrong per_unitcell value ...
        wrong_value_per_unitcell = cluster_expansion.per_unitcell()


Set a ClusterExpansion to use a different ConfigDoFValues instance
------------------------------------------------------------------

The function :func:`~libcasm.clexulator.ClusterExpansion.set` may be used to point :class:`~libcasm.clexulator.ClusterExpansion` at the data of a different :class:`~libcasm.clexulator.ConfigDoFValues` instance *which must have the same supercell*:

.. code-block:: Python

    # Correct usage -->
    # Reset cluster_expansion to point at the data in `other`,
    # which must have the same supercell.
    other = ConfigDoFValues(...)
    cluster_expansion.set(other)

    # ... calculate cluster expansion value using `other`'s data ...
    value_per_unitcell = cluster_expansion.per_unitcell()


.. warning::

    To calculate the cluster expansion in a different supercell, a new :func:`~libcasm.clexulator.ClusterExpansion` instance must be constructed.


Calculate the effect of changes in DoF values
---------------------------------------------

:class:`~libcasm.clexulator.ClusterExpansion` includes methods to efficiently calculate the change in the value of the per_supercell cluster expansion value due to changes in particular DoF values:

- :func:`~libcasm.clexulator.ClusterExpansion.occ_delta_value`: For the change in the per_supercell cluster expansion value due to the change in a single occupant value
- :func:`~libcasm.clexulator.ClusterExpansion.multi_occ_delta_value`: For the change in the per_supercell cluster expansion value due to changes in multiple occupant values
- :func:`~libcasm.clexulator.ClusterExpansion.local_delta_value`: For the change in the per_supercell cluster expansion value due to the change in a single local continuous DoF value
- :func:`~libcasm.clexulator.ClusterExpansion.global_delta_value`: For the change in the per_supercell cluster expansion value due to the change in a single global continuous DoF value

As an example, the following is pseudo-code that uses :func:`~libcasm.clexulator.ClusterExpansion.occ_delta_value` in the inner loop of a semi-grand canonical Monte Carlo simulation:

.. code-block:: Python

    # ... propose the change of a single occupation value ...
    linear_site_index = ... random site index ...
    new_occ = ... random occupant index, excluding current value ...

    # ... calculate the change in formation energy relative the current
    #     state of the ConfigDoFValues that cluster_expansion has been set with ...
    delta_formation_energy = cluster_expansion.occ_delta_value(
        linear_site_index, new_occ)

    delta_generalized_enthalpy = ... calculate change in potential ...

    # ... accept or reject ...
    if is_accepted(delta_generalized_enthalpy):
        # ... if accepted, update config_dof_values ...
        config_dof_values.set_occ(linear_site_index, new_occ)



