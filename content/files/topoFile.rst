.. _topoFile:

Topography Data File
====================

The file **topo.txt** contains all topography information. The first line in the topography file gives the number of points of known elevation followed by the list of the elevations (x,y,z). However, this is **not** the file that is used to define active topography in the forward modeling and inversion programs. Forward modeling and inversion executables require active cell models to define topography. 

.. figure:: images/topo.png
     :align: center
     :width: 700

     Example topography file














