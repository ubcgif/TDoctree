.. _elements:

Elements of the TDoctree version 1 package
==========================================

This section provides a brief description of each program in the TDoctree version 1 tiled package. In addition, we describe the file formats for all input and supporting files used by the coding library.

Program Library
---------------

The main executable programs within the TDoctree version 1 tiled program library are:

    - **create_octree_td_tiled:** creates a global OcTree mesh for the inversion based on the survey geometry and a set of local OcTree meshes about each transmitter and its receivers
    - **tdoctree_tiled:** used to forward model and inverted TEM data

Also included are the following Octree utility programs:

      - **extract_mesh:** extracts a specified local OcTree mesh from a hexidecimal file containing all local forward meshes
      - **blk3cellOct:** creates block models directly on OcTree meshes
      - **interface_weights:** generates interface weights

Main Input Files
----------------

Here, we describe the main input files for executables contained with the TDoctree version 1 tiled coding package.

.. tOcTree::
    :maxdepth: 2

    Create OcTree mesh <inputfiles/createOcTree>
    Create OcTree model <inputfiles/createModel>
    Forward modeling <inputfiles/forward>
    Create face weights <inputfiles/weightsFiles>
    Inversion <inputfiles/inversion>


Supporting Files
----------------

Here, we describe the formats of supporting files used to run TDoctree executable files. The input files for each executable program are described in the :ref:`running the programs<running>` section.

.. toctree::
    :maxdepth: 1

    Survey and Locations File <files/surveyFile>
    Predicted Data File <files/preFile>
    Observations File <files/obsFile>
    Topography File <files/topoFile>
    OcTree Mesh File <files/octree_mesh>
    Local Forward Meshes File <files/local_meshes> 
    Model File <files/model>
    Model and Face Weights Files <files/weights>
    Wave File <files/waveFile>
    Time Indecies File <files/time_indecies>
    Active Tiles File <files/active_tiles>












