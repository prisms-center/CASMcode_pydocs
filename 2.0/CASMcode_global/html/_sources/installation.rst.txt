Installation
============

Install from PyPI
-----------------

The latest version of libcasm-global can be installed with:

    pip install libcasm-global


Install from source
-------------------

Installation of libcasm-global from source requires standard compilers with support for C++17, Python >= 3.8, and CMake. For example:

- On Ubuntu linux:

  ```
  sudo apt-get install build-essential cmake
  ```

- On Mac OSX:

  ```
  xcode-select --install
  brew install cmake
  ```

- In a conda environment:

  ```
  conda create -n casm --override-channels -c conda-forge python=3 cmake
  conda activate casm
  ```

Then `libcasm-global` can be installed with:

    pip install .


To uninstall:

.. code-block::

    pip uninstall libcasm-global
