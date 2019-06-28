.. _binaryFile:

Local Forward Meshes (tiles) in binary File
===========================================

The local Octree meshes used to solve forward problems parallel are stored in a binary file. The ordering of local OcTree meshes in this file is the same as the ordering of transmitters in the :ref:`survey file<surveyFile>` or :ref:`observations file<obsFile>` that was used to make the meshes. The user may extract any local mesh and save it as a :ref:`standard OcTree mesh<octreeFile>` by running the :ref:`extract_mesh<tdoctree_extract>` code.



