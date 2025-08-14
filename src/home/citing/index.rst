.. include:: ../_shared/logo_and_spacing.rst

.. _citing_casm:

Citing CASM
===========

Citing the CASM software suite
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

CASM can be cited using the following reference:

.. _casm_ref:


    B.\  Puchala, J. C. Thomas, A. R. Natarajan, J. G. Goiri, S. S. Behara, J. L. Kaufman, A. Van der Ven, "CASM — A software package for first-principles based study of multicomponent crystalline solids, *Computational Materials Science*, **217**, 111897 (2023). DOI: `10.1016/j.commatsci.2022.111897 <https://doi.org/10.1016/j.commatsci.2022.111897>`_. :download:`[bibtex] <../_static/bib/WOS_000910852300005.bib>`

As an example, CASM can be acknowledged in a publication with:

“We used the CASM software suite :ref:`[1] <casm_ref>`, which automates the construction and parameterization of effective Hamiltonians and implements these Hamiltonians in Monte Carlo simulations.”

.. _citing_algorithms:

Citing algorithms
^^^^^^^^^^^^^^^^^

CASM utilizes a wide variety of algorithms, many of which were developed by the CASM development team, and some of which have yet to be published. Please cite :ref:`CASM <casm_ref>` if you implement a particular algorithm from CASM in other software.

CASM also relies on algorithms and methods that have been published in the literature.

The algorithms in CASM that enumerate symmetrically distinct configurations rely on algebraic properties of principal ideal domains, which were brought to bear on the problem by Hart and Forcade:

    G.\  L. W. Hart and R. W. Forcade, *Phys. Rev. B* **77**, 224115 (2008).


If you use CASM to map lattices or crystal structures, please cite the following reference:

.. _mapping_ref:

    J.\  C. Thomas, A. R. Natarajan, and A. Van der Ven, "Comparing crystal structures with symmetry and geometry," *npj Computational Materials*, **7**, 164 (2021). DOI: `10.1038/s41524-021-00627-0 <https://doi.org/10.1038/s41524-021-00627-0>`_. :download:`[bibtex] <../_static/bib/WOS_000705851600001.bib>`

If you use CASM to generate and calculate symmetry-adapted order parameters (using either the :func:`~libcasm.configuration.dof_space_analysis` or :func:`~libcasm.configuration.config_space_analysis` methods), please cite the following reference:

.. _orderparameter_ref:

    A.\  R. Natarajan, J. C. Thomas, B. Puchala, and A. Van der Ven, "Symmetry-adapted order parameters and free energies for solids undergoing order-disorder phase transitions," *Phys. Rev. B*, **96**, 134204 (2017). DOI: `10.1103/PhysRevB.96.134204 <https://doi.org/10.1103/PhysRevB.96.134204>`_. :download:`[bibtex] <../_static/bib/WOS_000413442800001.bib>`

If you use CASM to generate symmetry-adpated order parameters using the :func:`~libcasm.configuration.config_space_analysis` method, please cite the following reference:

.. _config_space_ref:

    F.\  Walsh, A. R. Natarajan, and A. Van der Ven, "Order parameters for antiferromagnetic structures: A first-principles study of iridium manganese," *Phys. Rev. Materials*, **6**, 044402 (2022). DOI: `10.1103/PhysRevMaterials.6.044402 <https://doi.org/10.1103/PhysRevMaterials.6.044402>`_. :download:`[bibtex] <../_static/bib/WOS_000783790800003.bib>`

The cluster expansion for configurational degrees of freedom was rigorously formalized by Sanchez *et al.*:

    J.\  Sanchez, F. Ducastelle, and D. Gratias, "Generalized cluster description of multicomponent systems," *Physica A*, **128**, 334–350 (1984). DOI: `10.1016/0378-4371(84)90096-7 <https://doi.org/10.1016/0378-4371(84)90096-7>`_. :download:`[bibtex] <../_static/bib/WOS_A1984TV90300018.bib>`

    D.\  deFontaine, in *Solid State. Phys.*, ed. H. Ehrenreich and D. Turnbull, Academic Press, vol. 47, pp. 33–176 (1994). DOI: `10.1016/S0081-1947(08)60639-6 <https://doi.org/10.1016/S0081-1947(08)60639-6>`_. :download:`[bibtex] <../_static/bib/WOS_A1994BE38H00002.bib>`


If you use CASM to find independent composition axes or perform Monte Carlo calculations, please cite the following reference:

.. _mc_ref:

    B.\  Puchala, J. C. Thomas, and A. Van der Ven, "CASM Monte Carlo: Calculations of the thermodynamic and kinetic properties of complex multicomponent crystals." arXiv preprint arXiv:2309.11761. DOI: `10.48550/arXiv.2309.11761 <https://doi.org/10.48550/arXiv.2309.11761>`_. :download:`[bibtex] <../_static/bib/puchala2023casmmontecarlocalculations.bib>`

The local cluster expansion for diffusion barriers was introduced by Van der Ven *et al.*:

    A.\  Van der Ven, G. Ceder, M. Asta, and P. D. Tepesch, *Phys. Rev. B* **64**, 184307 (2001).

The anharmonic potential cluster expansion as implemented in CASM was developed by Thomas *et al.*:

    J.\  C. Thomas, A. Van der Ven, "Finite-temperature properties of strongly anharmonic and mechanically unstable crystal phases from first principles", *Physical Review B*, **88**, 214111 (2013).


The fitting of the interaction coefficients of a cluster expansion to first-principles data relies on a minimization of the cross-validation (CV) score, an approach introduced to cluster expansions by van de Walle *et al.*:

    A.\  van de Walle and G. Ceder, *J. Phase Equilib.* **23**, 348 (2002).

The approach of using a genetic algorithm to pick interaction coefficients that minimize the CV score was introduced by Hart *et al.*, while the depth first search approach is due to Puchala *et al.*:

    G.\  L. W. Hart, V. Blum, M. J. Walorski, and A. Zunger, *Nat. Mater.* **4**, 391 (2005).

    B.\  Puchala, A. Van der Ven, "Thermodynamics of the Zr-O system from first-principles calculations", *Physical Review B*, **88**, 094108 (2013).

The use of compressive sensing methods to parameterize a cluster expansion was introduced by Nelson *et al.*:

    L.\  J. Nelson, G. L. W. Hart, F. Zhou, and V. Ozoliņš, *Phys. Rev. B* **87**, 035125 (2013).

Convergence criteria for Monte Carlo sampling are due to van de Walle *et al.*:

    A.\  van de Walle, M. Asta, *Modell. Simul. Mater. Sci. Eng.* **10**, 521 (2002).


.. _citation_submission:

Citation submission
^^^^^^^^^^^^^^^^^^^

If you use CASM for published work, please submit the publication information using our `citation submission form <https://docs.google.com/forms/d/e/1FAIpQLScWMUcKNeix1HrMMkwnYqEsyjKG5o2oK8H7S05B4S2NOOQ-vw/viewform?usp=header>`_ or send an email to :email:`casm-developers@lists.engr.ucsb.edu` so that we can include your citation on our website and demonstrate our impact to our funding agency.

