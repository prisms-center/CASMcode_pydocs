
.. _occupation-events-basics:

Occupation event basics
=======================

The class :class:`~libcasm.occ_events.OccEvent` is used to represent changes in occupation. For example, this could be:

- the change in occupation due to a diffusive hop
- the change in occupation due to a molecular re-orientation

An occupation event is represented by specifying the trajectories of each of the occupants involved in the event.

Construct an atomic hop OccEvent
--------------------------------

As an example, consider an event representing atom-vacancy exchange. In an :class:`~libcasm.occ_events.OccEvent`, the exchange of an atom and a vacancy is specified by:

- The trajectory of the atom, which consists of:

    - the initial position of the atom
    - the final position of the atom

- The trajectory of the vacancy, which consists of:

    - the initial position of the vacancy
    - the final position of the vacancy

The type and position of the occupants can be specified with:

- the site the occupant is on, represented by :class:`~libcasm.xtal.IntegralSiteCoordinate`
- an occupation index, which is the index of the occupant in the allowed occupant list ( :func:`~libcasm.xtal.Prim.occ_dof`) for the sublattice of the occupied site

The occupant position are represented by :class:`~libcasm.occ_events.OccPosition`, and a trajectory is represented by a two-element ``list[OccPosition]``.

The following example shows how to construct an :class:`~libcasm.occ_events.OccEvent` representing the exchange of an "A" atom and a vacancy on the 1NN sites in an FCC prim with allowed occupants "A", "B", and "Va":

.. code-block:: Python

    import libcasm.xtal as xtal
    from libcasm.xtal.prims import FCC as FCC_prim
    import libcasm.occ_events as occ_events
    from libcasm.occ_events import OccPosition, OccEvent

    xtal_prim = FCC_prim(r=1.0, occ_dof=["A", "B", "Va"])

    site1 = xtal.IntegralSiteCoordinate(sublattice=0, unitcell=[0, 0, 0])
    site2 = xtal.IntegralSiteCoordinate(sublattice=0, unitcell=[1, 0, 0])

    A_occ_index = 0
    Va_occ_index = 2

    A_initial_pos = OccPosition.occupant(site1, A_occ_index)
    A_final_pos = OccPosition.occupant(site2, A_occ_index)
    Va_initial_pos = OccPosition.occupant(site2, Va_occ_index)
    Va_final_pos = OccPosition.occupant(site1, Va_occ_index)

    occ_event = OccEvent([
        [A_initial_pos, A_final_pos],
        [Va_initial_pos, Va_final_pos],
    ])


Transforming OccEvent
---------------------

:class:`~libcasm.occ_events.OccEvent` can be translated by multiples of the lattice vectors:

.. code-block:: Python

    translation = np.array([a, b, c], dtype=int)

    # copy and translate:
    translated_occ_event = occ_event + translation
    assert translated_occ_event is not occ_event

    translated_occ_event = occ_event - translation
    assert translated_occ_event is not occ_event

    # mutate by translatation:
    occ_event += translation
    occ_event -= translation

Symmetry operations can be applied to :class:`~libcasm.occ_events.OccEvent` using the :class:`~libcasm.occ_events.OccEventRep` representation:

.. code-block:: Python

    # construct the prim factor group
    factor_group = xtal.make_factor_group(xtal_prim)

    # construct a representation of the prim factor group (list[OccEventRep])
    # for transforming OccEvent
    occevent_symgroup_rep = occ_events.make_occevent_symgroup_rep(
        factor_group,
        xtal_prim)

    for rep in occevent_symgroup_rep:
        # copy and transform
        transformed_occ_event = rep * occ_event
        assert transformed_occ_event is not occ_event


A copy of an :class:`~libcasm.occ_events.OccEvent` can be constructed using :func:`OccEvent.copy <libcasm.occ_events.OccEvent.copy>` or ``copy.deepcopy``:

.. code-block:: Python

    import copy

    # in Python, assignment is not a copy
    assigned_occ_event = occ_event
    assert assigned_occ_event is occ_event

    # to create a copy, use OccEvent.copy
    copied_occ_event = occ_event.copy()
    assert copied_occ_event is not occ_event

    # or, use copy.deepcopy
    copied_occ_event = copy.deepcopy(occ_event)
    assert copied_occ_event is not occ_event



Comparing OccEvent
------------------

To compare :class:`~libcasm.occ_events.OccEvent`, they can be put in a standardized form which sorts the occupant trajectories, considering both the forward and reverse directions:

.. code-block:: Python

    # mutate, into standardized form
    occ_event_a.standardize()

    # mutate, into standardized form
    occ_event_b.standardize()

    # check for equivalence:
    print("OccEvent are equal?:", occ_event_a == occ_event_b)
    print("OccEvent are not equal?:", occ_event_a != occ_event_b)

    # check ordering (lexicographical ordering of trajectories):
    print("occ_event_a < occ_event_b?:", occ_event_a < occ_event_b)
    print("occ_event_a <= occ_event_b?:", occ_event_a <= occ_event_b)
    print("occ_event_a > occ_event_b?:", occ_event_a > occ_event_b)
    print("occ_event_a >= occ_event_b?:", occ_event_a >= occ_event_b)
    print("OccEvent are not equal?:", occ_event_a != occ_event_b)


.. _orbits-of-occevent:

Orbits of OccEvent
------------------

The orbit of symmetrically equivalent :class:`~libcasm.occ_events.OccEvent` can be constructed using :func:`~libcasm.occ_events.occ_events.make_prim_periodic_orbit`:

.. code-block:: Python

    factor_group = xtal.make_factor_group(xtal_prim)
    occevent_symgroup_rep = occ_events.make_occevent_symgroup_rep(
        factor_group,
        xtal_prim)
    occevent_orbit = occ_events.make_prim_periodic_orbit(
        occ_event,
        occevent_symgroup_rep)

The output, ``occevent_orbit``, is a ``list[OccEvent]``, giving the :class:`~libcasm.occ_events.OccEvent` associated with the origin unit cell from the orbit of all equivalent OccEvent under prim factor group symmetry.


Atomic hops with sublattice restrictions
----------------------------------------

Note that in the previous example the occupation order is the same on every sublattice. In a more complicated :class:`~libcasm.xtal.Prim` with multiple sublattices, the occupation index for the "A" atom or vacancy might change from one sublattice to the other.

In the following example, "A", "B", and vacancies are allowed on FCC corner sites, while "B", "C", and vacancies are allowed on face sites:

.. code-block:: Python

    import libcasm.xtal as xtal
    from libcasm.occ_events import OccPosition, OccEvent

    lattice = xtal.Lattice(np.array([
        [1.0, 0.0, 0.0], # first lattice vector
        [1.0, 0.0, 0.0], # second
        [1.0, 0.0, 0.0], # third
    ]).transpose()) # <--- note transpose

    # Basis sites positions, as columns of a matrix,
    # in fractional coordinates with respect to the lattice vectors
    coordinate_frac = np.array([
        [0., 0., 0.]   # coordinates of basis site, b=0
        ]).transpose() # <--- note transpose

    # Occupation degrees of freedom (DoF)
    occ_dof=[
      ["A", "B", "Va"], # occupants allowed on basis site, b=0
      ["B", "C", "Va"], # occupants allowed on basis site, b=1
      ["B", "C", "Va"], # occupants allowed on basis site, b=2
      ["B", "C", "Va"], # occupants allowed on basis site, b=3
    ])

    xtal_prim = xtal.Prim(
        lattice=lattice,
        coordinate_frac=coordinate_frac,
        occ_dof=occ_dof,
        title="FCC, with sublattice restrictions")

Then an "B"-vacancy exchange event between corner and face sites is constructed using:

.. code-block:: Python

    site1 = xtal.IntegralSiteCoordinate(sublattice=0, unitcell=[0, 0, 0])
    site2 = xtal.IntegralSiteCoordinate(sublattice=1, unitcell=[0, 0, 0])

    B_init_occ_index = 1
    B_final_occ_index = 0
    Va_occ_index = 2

    B_initial_pos = OccPosition.occupant(site1, B_init_occ_index)
    B_final_pos = OccPosition.occupant(site2, B_final_occ_index)
    Va_initial_pos = OccPosition.occupant(site2, Va_occ_index)
    Va_final_pos = OccPosition.occupant(site1, Va_occ_index)

    occ_event = OccEvent([
        [A_initial_pos, A_final_pos],
        [Va_initial_pos, Va_final_pos],
    ])
