.. _running:

Running the programs
====================

This section provides describes how to run all executables pertaining to the TDoctree package.

.. note::

    All executable files, input files, output filenames and files specified within input files can be specified in the following manner:

    - as just the filename if contained within the current working directory (Example: *filename.txt*)
    - as a file path relative to the current working directory (Example: *sub_dir\\filename.txt*)
    - as the full path (Example: *C:\\Users\\Name\\Tests\\filename.txt*)

    Executable files should **not** be renamed. However, input file names can be specified by the user if desired.

The main executable programs within the TDoctree version 1 program library are:

    - **create_octree_mesh_td:** creates an OcTree mesh based on the survey geometry
    - **tdoctree:** used to forward model or invert TEM data

Also included are the following Octree utility programs:

    - **blk3cellOct:** creates conductivity models on an octree mesh
    - **interface_weights:** creates weights on the faces of cells

Contents
--------

To learn the specifics of running each executable, see the following sections:

  .. toctree::
    :maxdepth: 1

    OcTree Mesh Generation <programs/createOcTree>
    Creating Octree Models <programs/createModel>
    Forward Modeling <programs/forward>
    Additional Cell and Face Weights <programs/weightsFiles>
    Inversion <programs/inversion>

