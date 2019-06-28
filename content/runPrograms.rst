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

The main executable programs within the TDoctree version 1 tiled program library are:

    - **create_octree_td_tiled:** creates a global OcTree mesh for the inversion based on the survey geometry and a set of local OcTree meshes about each transmitter and its receivers
    - **tdoctree_tiled:** used to forward model and inverted TEM data

Also included are the following Octree utility programs:

      - **extract_mesh:** extracts a specified local OcTree mesh from a hexidecimal file containing all local forward meshes
      - **blk3cellOct:** creates block models directly on OcTree meshes
      - **interface_weights:** generates interface weights

Contents
--------

To learn the specifics of running each executable, see the following sections:

  .. toctree::
    :maxdepth: 1

    OcTree Mesh Generation <programs/createOcTree>
    Extract Tile <programs/extractOcTree>
    Creating Octree Models <programs/createModel>
    Forward Modeling <programs/forward>
    Additional Cell and Face Weights <programs/weightsFiles>
    Inversion <programs/inversion>

