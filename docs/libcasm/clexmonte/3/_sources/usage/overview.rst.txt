Overview
========

The libcasm-clexmonte package provides the CASM cluster expansion Monte Carlo
implementations.

General approach
----------------

At its most basic, Monte Carlo simulations are setup and performed using libcasm-clexmonte by following these steps:

1. Construct a :class:`~libcasm.clexmonte.System`.
2. Construct a :class:`~libcasm.clexmonte.MonteCalculator`.
3. Construct an initial :class:`~libcasm.clexmonte.MonteCarloState`.
4. Construct a single :class:`~libcasm.clexmonte.SamplingFixtureParams`, or a :class:`~libcasm.clexmonte.RunManager` with multiple :class:`~libcasm.clexmonte.SamplingFixtureParams`, to specify what data to sample at periodic intervals.
5. (If kinetic Monte Carlo) Specify any data that should be collected on the selected events i.e. how many of each type of event occurred, histograms of the activation energies of selected events, counts of the local composition around a selected event, etc.
6. Run a Monte Carlo simulation.

The following sections provide an overview of the classes and methods used to setup and run Monte Carlo simulations to provide a high level summary of libcasm-clexmonte. Specific examples and tutorials are currently in development.


System
------

The :class:`~libcasm.clexmonte.System` class holds data defining the crystal system and its properties, independent of a particular Monte Carlo method or particular state of the sytem. This includes:

- the prim (:class:`~libcasm.configuration.Prim`),
- the composition axes (:class:`~libcasm.composition.CompositionConverter`),
- basis sets (:class:`~libcasm.clexulator.Clexulator`),
- correlations calculators (:class:`~libcasm.clexulator.Correlations`),
- cluster expansion calculators (:class:`~libcasm.clexulator.ClusterExpansion`), and
- order parameter calculators (:class:`~libcasm.clexulator.OrderParameter`).

The system data also includes other data such as local basis sets, local cluster expansions, and kinetic Monte Carlo events.

MonteCalculator
---------------

The :class:`~libcasm.clexmonte.MonteCalculator` class provides a standardized interface to Monte Carlo simulation implementations and to the data used during the simulation. This includes:

- system data (:class:`~libcasm.clexmonte.System`),
- state data (:class:`~libcasm.clexmonte.StateData`),
- the potential calculator (:class:`~libcasm.clexmonte.MontePotential`),
- the most recently selected event (:class:`~libcasm.clexmonte.SelectedEvent`),
- selected event data collecting functions (:class:`~libcasm.monte.sampling.SelectedEventFunctions`),
- state sampling functions (:class:`~libcasm.monte.sampling.StateSamplingFunction` and :class:`~libcasm.monte.sampling.jsonStateSamplingFunction`),
- results analysis functions (:class:`~libcasm.clexmonte.ResultsAnalysisFunction`), and
- functions to run the Monte Carlo simulation (:func:`~libcasm.clexmonte.MonteCalculator.run_fixture` and :func:`~libcasm.clexmonte.MonteCalculator.run`).

MonteCarloState
---------------

The :class:`~libcasm.clexmonte.MonteCarloState` data structure combines:

- a configuration (:class:`~libcasm.configuration.Configuration`), and
- thermodynamic conditions (:class:`~libcasm.monte.ValueMap`).

A MonteCarloState can be constructed by:

- explicitly giving the exact configuration and conditions (using the :class:`~libcasm.clexmonte.MonteCarloState` constructor), or
- using the :func:`~libcasm.clexmonte.make_initial_state` or :func:`~libcasm.clexmonte.make_canonical_initial_state` methods to perform standard operations like finding the configuration with minimum potential, or fill a supercell with a certain shape or minimum volume with a motif configuration.

For canonical and kinetic Monte Carlo calculations, it may be useful to:

- use the :func:`~libcasm.clexmonte.enforce_composition` method to perturb an MonteCarloState configuration to match a desired composition, or
- set the conditions of the MonteCarloState to match its configuration.

SamplingFixtureParams
---------------------

A :class:`~libcasm.clexmonte.SamplingFixture` is used to sample data, store results, and check for completion during a Monte Carlo simulation. :class:`~libcasm.clexmonte.SamplingFixtureParams` is a data structure that specifies all the parameters that control a :class:`~libcasm.clexmonte.SamplingFixture`. This includes:

- sampling functions (:class:`~libcasm.monte.sampling.StateSamplingFunction` and :class:`~libcasm.monte.sampling.jsonStateSamplingFunction`), including both standard sampling functions provided by the implementation and user-provided custom sampling functions, which return the quantities (energy, composition, order parameters, etc.) sampled by the fixture,
- sampling parameters (:class:`~libcasm.monte.sampling.SamplingParams`), specifying which sampling functions to evaluate and when the samples should be taken,
- completion check parameters (:class:`~libcasm.monte.sampling.CompletionCheckParams`), which includes which sampled quantities should be converged, the requested absolute or relative precision level, how often to check, and minimum and maximum numbers of samples, steps or passes, computational or simulated time to run for,
- results output parameters, including where to write output files, whether to only write a summary with mean values and estimated precisions, or to also write all observations, or the trajectory of configurations at each sample time, and
- status logging parameters, including whether, where, and how often to write a status log file with the most recent completion check results.


SelectedEventFunctionParams
---------------------------

For kinetic Monte Carlo simulations, data may be collected such as counts how many times each type of event occurred, histograms of the activation energies of selected events, and counts of the local composition around a selected event.

:class:`~libcasm.monte.sampling.SelectedEventFunctions` are used to collect data about the events that occurred during a kinetic Monte Carlo simulation and the :class:`~libcasm.monte.sampling.SelectedEventFunctionParams` data structure specifies the parameters that control the selection of events during a Monte Carlo simulation.


RunManager
----------

In some cases it may be useful to use multiple sampling fixtures for a single Monte Carlo simulation. For instance, a sampling fixture for thermodynamic properties can be re-used and combined with a sampling fixture for kinetic properties during a kinetic Monte Carlo simulation, or sampling fixtures that sample different quantities at different intervals could be combined.

The :class:`~libcasm.clexmonte.RunManager` class is used to hold one or more :class:`SamplingFixture` and enables Monte Carlo methods to do sampling and convergence checking according to each sampling fixture. A `global_cutoff` parameter determines if all sampling fixtures must complete for the Monte Carlo run to finish, or if the run should stop when any one sampling fixture completes.

Additionally, the RunManager controls options for saving initial and final states of each run in order to enable re-starts of a series of runs and perform "dependent runs" where the final configuration of one run is used as the initial configuration of the next run at varying conditions.


Running Monte Carlo simulations
-------------------------------

Simulations can be run at single state using:

- :func:`MonteCalculator.run_fixture <libcasm.clexmonte.MonteCalculator.run_fixture>` when using a single sampling fixture, or
- :func:`MonteCalculator.run <libcasm.clexmonte.MonteCalculator.run>` when using a RunManager.

Main results, the average value of sampled quantities and estimated precision, and the calculation of quantities like the heat capacity and susceptibility from fluctuations of energy and composition, are stored in a results summary file. If additional runs are performed at different thermodynamic conditions, the values calculated from each subsequent run are stored by appending to lists in the results summary file. Often, for ease of thermodynamic integration to calculate free energies, runs are organized along linear paths in thermodynamic conditions space (for instance increasing temperature at constant chemical potential), with one summary file for one linear path.

.. attention::
    Running multiple Monte Carlo simulations at various thermodynamic conditions can be automated by:

    - using :class:`~libcasm.clexmonte.IncrementalConditionsStateGenerator` to run a series of simulations along a path in conditions space,
    - using the `casm-flow <TODO>` package, which helps automate the process of setting up input files, submitting jobs to a cluster, and collecting, analyzing, and plotting results.

