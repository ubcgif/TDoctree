.. _elements:

Elements of the TDoctree version 1 package
==========================================

This section provides a brief description of each program in the TDoctree version 1 package. In addition, we describe the file formats for all input and supporting files used by the coding library.

Program Library
---------------

The main executable programs within the TDoctree version 1 program library are:

    - **create_octree_1mesh_td:** creates an OcTree mesh based on the survey geometry
    - **TDoctreeinv:** used to forward model or invert TEM data

Also included are the following OcTree utility programs:

    - **blk3cellOct:** creates conductivity models on an OcTree mesh
    - **interface_weights:** creates weights on the faces of cells

Main Input Files
----------------

Here, we describe the main input files for executables contained with the TDoctree version 1 coding package.

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
    Model File <files/model>
    Model and Face Weights Files <files/weights>
    Wave File <files/waveFile>












