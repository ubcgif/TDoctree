.. _tdoctree_extract:

Extract Local OcTree Mesh
=========================

The output of using the code **create_octree_mesh_td_tiled.exe** to :ref:`create OcTree meshes<tdoctree_octree>` includes a :ref:`binary file<binaryFile>` **octree_small_mesh.dat**. To extract a particular local OcTree mesh and save as an :ref:`OcTree mesh file<octreeFile>`, we use the program **extract_mesh.exe**. This executable does not require an input file. 

The syntax for extracting local OcTree meshes is the path to the program **extract_mesh.exe**, followed by the path to the binary file containing all local meshes, followed by the index of the local mesh you would like to extract.

.. figure:: images/run_extract_mesh.png
     :align: center
     :width: 500

The program outputs a file named **octree_mesh_xxxx.txt** where 'xxxx' is a placeholder for the index number.
