.. _example_model:

Create Model
============

Here, the code **blk3cell.exe** and the input file **blk3cell.inp** (:ref:`see format <e3d_blk3cell_input>`) are used to create a conductivity model on the underlying tensor mesh. Then, the code **3DModel2Octree.exe** and the input file **3Dmodel2octree.inp** (:ref:`see format <e3d_3Dmodel2octree_input>`) are used to interpolate the tensor mesh onto the OcTree mesh. For this example, we use the mesh that was created in the example ":ref:`create OcTree mesh<example_octree>`". Files relevant to this part of the example are in the sub-folder *octree_model*. Before running this example, you may want to do the following:

	- `Download and open the zip folder containing the entire E3D version 1 example <https://github.com/ubcgif/E3D/raw/e3dinv/assets/e3d_ver1_example.zip>`__ (if not done already)
	- Learn how to run :ref:`blk3cell<e3d_model_blk3cell>` and :ref:`3DModel2Octree<e3d_model_3DtoOctree>` from command line
	- Learn the format of the input files :ref:`blk3cell.inp<e3d_blk3cell_input>` and :ref:`3Dmodel2octree.inp<e3d_3Dmodel2octree_input>`


Here is the input file for **blk3cell.exe**

.. figure:: ../inputfiles/images/create_blk3cell_input.png
     :align: center
     :width: 700


.. warning:: It is not advisable to image models on the base tensor mesh as they can be prohibitively large (>> 1M cells).


Here is the input file for **3DModel2Octree.exe**

.. figure:: ../inputfiles/images/create_3DtoOctree_input.png
     :align: center
     :width: 700



The resulting Octree model shows a conductive block (:math:`\sigma` = 0.2 S/m) within a more resistive background (:math:`\sigma_b` = 0.01 S/m).


.. figure:: images/octree_model1.png
     :align: center
     :width: 500


