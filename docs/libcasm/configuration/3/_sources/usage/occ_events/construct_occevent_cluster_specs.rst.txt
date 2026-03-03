.. _constructing-local-cluster-orbits:

Constructing local-cluster orbits
=================================

Construct a prototype OccEvent
------------------------------

To generate local-cluster orbits, and from them local basis functions, one prototype :class:`~libcasm.occ_events.OccEvent` from the orbit of symmetrically equivalent :class:`~libcasm.occ_events.OccEvent` must be provided.

While any element of the orbit may be used, it is conventional to use the :class:`~libcasm.occ_events.OccEvent` in canonical form, which by convention in CASM is the orbit element that compares as greatest.

The canonical :class:`~libcasm.occ_events.OccEvent` can be obtained by :ref:`constucting an orbit<orbits-of-occevent>` and choosing the last element, or directly using :func:`~libcasm.occ_events.make_canonical_occevent`:

.. code-block:: Python

    import libcasm.occ_events as occ_events
    import libcasm.xtal as xtal

    xtal_prim = xtal.Prim(...)
    occ_event = occ_events.OccEvent(...)

    canonical_occ_event = occ_events.make_canonical_occevent(
        xtal_prim, occ_event)


Enumerating OccEvent
--------------------

Symmetrically distinct OccEvent may be enumerated using the function :func:`~libcasm.occ_events.make_canonical_prim_periodic_occevents`. This method works by iterating over all possible :class:`~libcasm.occ_events.OccEvent` and removing invalid, unwanted, or symmetrically equivalent :class:`~libcasm.occ_events.OccEvent`.

The iteration proceeds over:

1) Symmetrically distinct clusters of sites (outer-most loop)
2) Initial occupation values
3) Final occupation values
4) Permutations between the initial and final positions (inner-most loop)

Built-in filters allow for selecting :class:`~libcasm.occ_events.OccEvent` by:

- Cluster size
- Sublattices involved
- Initial or final occupation
- Atom type counts

The default settings skip :class:`~libcasm.occ_events.OccEvent` which:

- Represent a sub-cluster :class:`~libcasm.occ_events.OccEvent` because
  (i) a site is vacant before and after the event or (ii) any molecule
  does not change sites, break up, or re-orient.
- Have two atoms directly exchange sites. (Does not skip atom-vacancy
  exchange.)

Additionally, for :class:`~libcasm.xtal.Prim` with molecular occupants, :class:`~libcasm.occ_events.OccEvent` can be selected by:

- Molecule type counts
- Orientation type counts
- Whether or not molecules break apart

.. warning::

    Enumerated :class:`~libcasm.occ_events.OccEvent` represent symmetrically distinct trajectories of occupants, considering only the initial and final positions, and how the occupants permute amongst them, but not the complete transformation pathway. In real materials, there are some cases in which there may be multiple distinct pathways that are represented by the same :class:`~libcasm.occ_events.OccEvent`. In this case, it is possible to include duplicate :class:`~libcasm.occ_events.OccEvent` with different names in a CASM project for input to kinetic Monte Carlo calculations.


The following example script demonstrates enumerating distinct :class:`~libcasm.occ_events.OccEvent`, in an FCC Prim with "A" and "B" atoms and vacancies, including exchange on triplet sites, using the default filters:

.. code-block:: Python

    import math
    import sys
    import libcasm.clusterography as clust
    import libcasm.occ_events as occ_events
    import libcasm.sym_info as sym_info
    import libcasm.xtal as xtal
    import libcasm.xtal.prims as xtal_prims

    r = 1.0 # ideal atom radius
    a = math.sqrt( ((4*r)**2) /2.) # conventional FCC lattice parameter
    tol = 1e-5
    xtal_prim = xtal_prims.FCC(r=r, occ_dof=["A", "B", "Va"])

    # The OccSystem provides index conversions
    system = occ_events.OccSystem(xtal_prim)

    # The maximum site-to-site distance to allow in clusters,
    # by number of sites in the cluster. The null cluster and
    # point cluster values (elements 0 and 1) are arbitrary
    # for periodic clusters.
    max_length = [
        0.0, # null-cluster orbit
        0.0, # point-cluster orbits
        a + tol, # pair-cluster orbits, including 2NN sites
        a + tol, # triplet-cluster orbits, including 2NN sites
    ]

    # Custom generators is a list[clust.ClusterOrbitGenerator]
    # that allows specifying custom clusters to include,
    # independent of the max_length cutoff,
    # and optionally also including subclusters
    custom_generators = []

    # Construct ClusterSpecs, with generating group equal to
    # the invariant group of prototype_occ_event
        cluster_specs = clust.ClusterSpecs(
        xtal_prim=xtal_prim,
        generating_group=sym_info.make_factor_group(xtal_prim),
        max_length=max_length,
        custom_generators=custom_generators)

    orbits = cluster_specs.make_orbits()
    # null, point, 1NN pair, 2NN pair, 1NN triplet, 2NN triplet
    assert len(orbits) == 6

    # `occevent_counter_params` is a dict that sets filters
    # See the `make_canonical_prim_periodic_occevents` documentation
    # for the list of options (TODO)
    occevent_counter_params = {}

    # `custom_occevents` is a list[occ_events.OccEvent]
    # that allows specifying custom OccEvent to include,
    # independent of the cluster_specs,
    # and not subject to filtering,
    # but still subject to removing duplicates
    custom_occevents = []

    canonical_occevents = occ_events.make_canonical_prim_periodic_occevents(
        system, cluster_specs, occevent_counter_params, custom_occevents)

    # Print enumerated events for inspection
    print_event = occ_events.OccEventPrinter(f=sys.stdout,
                                             system=system,
                                             coordinate_mode='cart')

    for i, x in enumerate(canonical_occevents):
        print(i)
        print_event(x)
        print()

    # pair.1: A-Va, B-Va
    # pair.2: A-Va, B-Va
    # triplet.1NN: A-A-Va, B-B-Va, A-A-A, B-B-B, A-B-Va, A-A-B, B-B-A
    # triplet.2NN: A-A-Va x2, B-B-Va x2, A-A-A x1, B-B-B x1, A-B-Va x3, A-A-B x2, B-B-A x2,
    assert len(canonical_occevents) == 24


The example prints the following description of the enumerated events, using :py:class:`~libcasm.occ_events.OccEventPrinter`, with site locations printed using Cartesian coordinates:

.. code-block::

    0
    Site Occupation:
    [0.0, 0.0, 0.0]:  1 == B  ->  2 == Va
    [0.0, 1.414213562373095, 1.414213562373095]:  2 == Va  ->  1 == B
    Trajectories:
    [[0.0, 0.0, 0.0], 1] == B  ->  [[0.0, 1.414213562373095, 1.414213562373095], 1] == B
    [[0.0, 1.414213562373095, 1.414213562373095], 2] == Va  ->  [[0.0, 0.0, 0.0], 2] == Va

    1
    Site Occupation:
    [0.0, 0.0, 0.0]:  1 == B  ->  2 == Va
    [0.0, 0.0, 2.82842712474619]:  2 == Va  ->  1 == B
    Trajectories:
    [[0.0, 0.0, 0.0], 1] == B  ->  [[0.0, 0.0, 2.82842712474619], 1] == B
    [[0.0, 0.0, 2.82842712474619], 2] == Va  ->  [[0.0, 0.0, 0.0], 2] == Va
    ...

    22
    Site Occupation:
        [0.0, 0.0, 0.0]:  0 == A  ->  0 == A
        [-1.414213562373095, 0.0, 1.414213562373095]:  0 == A  ->  0 == A
        [0.0, 1.414213562373095, 1.414213562373095]:  0 == A  ->  0 == A
    Trajectories:
        [[0.0, 0.0, 0.0], 0] == A  ->  [[-1.414213562373095, 0.0, 1.414213562373095], 0] == A
        [[-1.414213562373095, 0.0, 1.414213562373095], 0] == A  ->  [[0.0, 1.414213562373095, 1.414213562373095], 0] == A
        [[0.0, 1.414213562373095, 1.414213562373095], 0] == A  ->  [[0.0, 0.0, 0.0], 0] == A

    23
    Site Occupation:
        [0.0, 0.0, 0.0]:  0 == A  ->  0 == A
        [0.0, 1.414213562373095, 1.414213562373095]:  0 == A  ->  0 == A
        [0.0, 0.0, 2.82842712474619]:  0 == A  ->  0 == A
    Trajectories:
        [[0.0, 0.0, 0.0], 0] == A  ->  [[0.0, 1.414213562373095, 1.414213562373095], 0] == A
        [[0.0, 1.414213562373095, 1.414213562373095], 0] == A  ->  [[0.0, 0.0, 2.82842712474619], 0] == A
        [[0.0, 0.0, 2.82842712474619], 0] == A  ->  [[0.0, 0.0, 0.0], 0] == A


Save/load OccEvent
------------------

A standard location to save an event for future use is in:

- <CASM project directory> / events / event.<name_of_event> / event.json

The functions :func:`~libcasm.occ_events.save_occevent` and :func:`~libcasm.occ_events.load_occevent` methods can be used to save :class:`~libcasm.occ_events.OccEvent` to the standard location and later load them:

.. code-block:: Python

    # root: pathlib.Path
    # prototype_occ_event: prototype occ_events.OccEvent

    # The OccSystem provides index conversions
    system = occ_events.OccSystem(xtal_prim)

    # Save an OccEvent:
    occ_events.save_occevent(root, "1NN_A_Va", prototype_occ_event, system)

    # Load an OccEvent:
    loaded_occ_event = occ_events.load_occevent(root, "1NN_A_Va", system)

    assert loaded_occ_event == prototype_occ_event


ClusterSpecs for local-cluster orbits
-------------------------------------

The subgroup of the prim factor group that leaves an :class:`~libcasm.occ_events.OccEvent` invariant is the generating group for local-cluster orbits and local basis functions for properties of that event.

The :class:`~libcasm.clusterography.ClusterSpecs` class encapsulates all the parameters needed for constructing cluster orbits. A :class:`~libcasm.clusterography.ClusterSpecs` object with the generating group set to the invariant group of an :class:`~libcasm.occ_events.OccEvent` can be constructed using :func:`~libcasm.occ_events.make_occevent_cluster_specs`:

.. warning::

    When constructing a local cluster expansion basis set, the symmetry of the :class:`~libcasm.occ_events.OccEvent` is very often the same as the symmetry of the actual transformation pathway in the real material, but there are exceptions. The exceptions tend to be cases where multiple pathways exist through intermediate metastable states. In such cases, the user should take care to ensure the symmetry and multiplicity of the events is accurately reproduced by the chosen :class:`~libcasm.occ_events.OccEvent` and the generating group used to construct the local basis set.

.. code-block:: Python

    # xtal_prim: xtal.Prim

    # Construct ClusterSpecs, with generating group equal to
    # the invariant group of prototype_occ_event
    cluster_specs = occ_events.make_occevent_cluster_specs(
        xtal_prim=xtal_prim,
        phenomenal_occ_event=prototype_occ_event,
        max_length=[0.0, 0.0],
        cutoff_radius=[0.0, 2.01])


Local-cluster orbits
--------------------

Once the :class:`~libcasm.clusterography.ClusterSpecs` instance is constructed, local-cluster orbits can be generated using :func:`~libcasm.clusterography.ClusterSpecs.make_orbits`:

.. code-block:: Python

    # Construct local cluster orbits
    local_cluster_orbits = cluster_specs.make_orbits()

Local basis sets
----------------

The :class:`~libcasm.clusterography.ClusterSpecs` instance can be output to JSON for use as input for constructing local basis sets using :func:`~libcasm.clusterography.ClusterSpecs.to_dict`:

.. code-block:: Python

    # Output cluster specs JSON for local basis set construction
    cluster_specs_json = cluster_specs.to_dict()


OccEvent invariant group
------------------------

The subgroup of the prim factor group that leaves an :class:`~libcasm.occ_events.OccEvent` invariant is the generating group for local basis functions of properties of that event. It can be constructed explicitly using :func:`~libcasm.occ_events.make_occevent_group`:

.. code-block:: Python

    import libcasm.occ_events as occ_events
    import libcasm.sym_info as sym_info

    # xtal_prim: xtal.Prim
    # occ_event: occ_events.OccEvent

    # Note the use of sym_info.make_factor_group:
    prim_factor_group = sym_info.make_factor_group(xtal_prim)
    occevent_symgroup_rep = occ_events.make_occevent_symgroup_rep(
        prim_factor_group.elements(), xtal_prim)

    # Construct the group which leaves the phenomenal OccEvent invariant
    invariant_group = occ_events.make_occevent_group(
        occ_event=prototype_occ_event,
        group=prim_factor_group,
        lattice=xtal_prim.lattice(),
        occevent_symgroup_rep=occevent_symgroup_rep)

The objects ``prim_factor_group`` and ``invariant_group`` are instances of :class:`~libcasm.sym_info.SymGroup`, with the relationship that ``invariant_group`` is a subgroup of ``prim_factor_group``, which is called the "head group". The class :class:`~libcasm.sym_info.SymGroup` provides more information than a simple ``list[libcasm.xtal.SymOp]``, including the multiplication table and the head group indices of the subgroup operations.
