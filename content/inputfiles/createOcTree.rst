.. _tdoctree_input_octree:

Create OcTree Mesh Input File
=============================

The :ref:`OcTree mesh<octreeFile>` used in the TDoctree code are created using the program **create_octree_1mesh_td.exe**. Parameters necessary for defining the OcTree mesh are set in the input file. The lines within the input file are as follows:


.. tabularcolumns:: |C|C|C|

+--------+---------------------------------------------------------------+-----------------------------------------------------------------+
| Line # | Parameter                                                     | Descriptions                                                    |
+========+===============================================================+=================================================================+
| 1      |:ref:`dx dy dz<tdoctree_input_octreeln1>`                      | min. cell widths in x, y and z for base mesh                    |
+--------+---------------------------------------------------------------+-----------------------------------------------------------------+
| 2      |:ref:`x_pad y_pad down_pad up_pad<tdoctree_input_octreeln2>`   | sets the thickness of padding in x, y, down and up directions   |
+--------+---------------------------------------------------------------+-----------------------------------------------------------------+
| 3      |:ref:`h1 h2 h3<tdoctree_input_octreeln3>`                      | sets cell sizes within core mesh region                         |
+--------+---------------------------------------------------------------+-----------------------------------------------------------------+
| 4      |:ref:`n1 n2 n3<tdoctree_input_octreeln4>`                      | sets thickness of cells of finest discretization near receivers |
+--------+---------------------------------------------------------------+-----------------------------------------------------------------+
| 5      |:ref:`locFile<tdoctree_input_octreeln5>`                       | the file containing transmitters and observation locations      |
+--------+---------------------------------------------------------------+-----------------------------------------------------------------+
| 6      |:ref:`topoFile<tdoctree_input_octreeln6>`                      | sets topography                                                 |
+--------+---------------------------------------------------------------+-----------------------------------------------------------------+
| 7      |:ref:`polygon options<tdoctree_input_octreeln7>`               | Sets lateral extent of core region                              |
+--------+---------------------------------------------------------------+-----------------------------------------------------------------+



.. .. figure:: images/create_octree_input.png
..      :align: center
..      :width: 700

..      Example input file for creating octree mesh (`Download <https://github.com/ubcgif/tdoctree/raw/tdoctreeinv/assets/input_files1/tdoctree_mesh.inp>`__ )


Line Descriptions
^^^^^^^^^^^^^^^^^


.. _tdoctree_input_octreeln1:

    - **dx dy dz:** Minimum cell widths in x, y and z for the base mesh.

.. _tdoctree_input_octreeln2:

    - **x_pad y_pad down_pad up_pad:** Distance from the survey area in the x, y, downward and upward directions, respectively, that the mesh extends.

.. _tdoctree_input_octreeln3:

    - **h1 h2 h3:** Sets cell sizes within the core mesh region. Up to a depth of *h1* from surface topography, the smallest cell size is used (set by *dx, dy, dz*). For the following *h2* metres, the cell widths are doubled. For the following *h3* metres, the cell widths are doubled again. Outside a depth and horizontal distance of *h1+h2+h3*, the cells widths increase by a factor of 2 for every additional layer (see the figure below).

.. _tdoctree_input_octreeln4:

    - **n1 n2 n3:** This sets the thicknesses of layers of finest discretization near the receivers. **n1 = 4** means that around each receiver, there is a layer 4 cells thick that uses the finest discretization. This is followed by a layer which is **n2** cells thick, where the cell dimensions are increased by a factor of 2. Likewise for the 3rd layer.

.. _tdoctree_input_octreeln5:

    - **locFile:** Contains the locations of the receivers. The user may either enter the file path to an :ref:`observed data<obsFile>` file, or the flag "ONLY_LOC" followed by the path to a :ref:`data points<surveyFile>` file. 

.. _tdoctree_input_octreeln6:

    - **topoFile:** If a topography file is available, the file path to the topography file is entered; see :ref:`topography file<topoFile>` for format. In the case of flat topography, the user instead enter "TOPO_CONST", followed by a space, then the elevation of the surface topography; for example "TOPO_CONST 125.5".

.. _tdoctree_input_octreeln7:

    - **polygon options:** This sets the lateral extent of the core mesh region. Here, there are two options

        1. The flag *MAKE_POLYGON* is entered followed by a positive value (*val*). Up to a lateral distance *val* from all transmitters, the finest mesh discretization is used.
        2. Enter the file path to a :ref:`polygon file<topoFile>`. The polygon denotes the points of a convex hull that is used to define the lateral extent of the core mesh region.












