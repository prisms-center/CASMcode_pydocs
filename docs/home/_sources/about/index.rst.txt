.. include:: ../_shared/logo_and_spacing.rst

About CASM
==========

Acknowledgements
----------------

CASM is developed by the `Van der Ven group <https://labs.materials.ucsb.edu/vanderven/anton/>`_, originally at the University of Michigan and currently at the University of California Santa Barbara.

- **Lead developers**:  John C. Thomas and Brian Puchala
- **Developers**:  John Goiri, Anirudh Natarajan, Sesha Sai Behara, Jonas Kaufman, Jeremiah Thomas
- **Other contributors**: Min-Hua Chen, Jonathon Bechtel, Max Radin, Elizabeth Decolvenaere, Anna Belak, Liang Tian, Naga Sri Harsha Gunda, Julija Vinckeviciute, Sanjeev Kolli, Jonathan Li

The development of CASM was made possible with support from:

- The U.S. Department of Energy, Office of Basic Energy Sciences, Division of Materials Sciences and Engineering under Award #DE-SC0008637 that funds the PRedictive Integrated Structural Materials Science (PRISMS) Center at University of Michigan.
- The National Science Foundation under Awards DMR-1410242, DMR-1105672, DMR-1436154, and OAC-1642433.

License
-------

CASM packages are released under the GNU Lesser General Public License (LGPL).

Dependencies
------------

CASM depends on the C++ and Python open source software communities and we are thankful to all the authors, contributors, and maintainers. A selection of the packages used by or with CASM includes:

Core dependencies
^^^^^^^^^^^^^^^^^

- **Eigen**: "A C++ template library for linear algebra." `[link] <https://eigen.tuxfamily.org/>`_
- **NumPy**: "The fundamental package for scientific computing with Python." `[link] <https://numpy.org/>`_
- **SciPy**: "Fundamental algorithms for scientific computing in Python." `[link] <https://scipy.org/>`_
- **Sparse**: A library for sparse matrices in Python. `[link] <https://sparse.pydata.org/>`_
- **opt_einsum**: A library for optimizing tensor contractions. `[link] <https://optimized-einsum.readthedocs.io/>`_
- **JSON for Modern C++**: "A JSON library for modern C++." `[link] <https://json.nlohmann.me/>`_
- **Gzstream**: "Gzstream is a small C++ library, basically just a wrapper, that provides the functionality of the zlib C-library in a C++ iostream." `[link] <https://www.cs.unc.edu/Research/compgeom/gzstream/>`_
- **Qhull**: A software package for computing the convex hull, Delaunay triangulation, and Voronoi diagrams. `[link] <http://www.qhull.org/>`_

Building and documentation
^^^^^^^^^^^^^^^^^^^^^^^^^^
- **scikit-build**: "A Python build system for CPython C/C++/Fortran/Cython extensions using CMake." `[link] <https://scikit-build.readthedocs.io/>`_
- **pybind11**: "A lightweight header-only library that exposes C++ types in Python and vice versa, mainly to create Python bindings of existing C++ code." `[link] <https://pybind11.readthedocs.io/>`_
- **cibuildwheel**: "Build Python wheels for all the platforms with minimal configuration." `[link] <https://cibuildwheel.pypa.io/>`_
- **Sphinx**: "Create intelligent and beautiful documentation with ease." `[link] <https://www.sphinx-doc.org/>`_
- **PyData Sphinx Theme**: "A clean, Bootstrap-based Sphinx theme by and for the PyData community." `[link] <https://pydata-sphinx-theme.readthedocs.io/>`_

Visualization
^^^^^^^^^^^^^

- **Bokeh**: "A Python library for creating interactive visualizations for modern web browsers." `[link] <https://bokeh.org/>`_


Optional
^^^^^^^^

Optional dependencies, used for the notebook tutorials, fitting coefficients, and integrating with DFT codes:

- **jupyterlab**: "A highly extensible, feature-rich notebook authoring application and editing environment." `[link] <https://jupyterlab.readthedocs.io/en/latest/>`_
- **scikit-learn**: "Machine Learning in Python." `[link] <https://scikit-learn.org/>`_
- **ASE**: "The Atomic Simulation Environment (ASE) is a set of tools and Python modules for setting up, running, and analyzing atomistic simulations." `[link] <https://ase-lib.org/>`_

