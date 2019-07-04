.. _tdoctree_input_inv:

Inversion Input File
====================

The inverse problem is solved using the executable program **tdoctree_tiled.exe**. The lines of input file are as follows:

.. tabularcolumns:: |L|C|C|

+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| Line # | Description                                                             | Description                                                       |
+========+=========================================================================+===================================================================+
| 1      | :ref:`Inversion Mesh<tdoctree_input_inv_ln1>`                           | path to inversion mesh file                                       |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 2      | :ref:`Forward Mesh<tdoctree_input_inv_ln2>`                             | path to forward mesh file                                         |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 3      | :ref:`nTiles<tdoctree_input_inv_ln3>`                                   | total number of tile meshes                                       |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 4      | :ref:`Observation File<tdoctree_input_inv_ln4>`                         | path to observations/survey file                                  |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 5      | :ref:`Transmitter options<tdoctree_input_inv_ln5>`                      | discretization options for transmitters                           |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 6      | :ref:`Wave file<tdoctree_input_inv_ln6>`                                | sets time steps for the time-dependent problem                    |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 7      | :ref:`Initial/FWD Model<tdoctree_input_inv_ln7>`                        | initial/forward model                                             |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 8      | :ref:`Reference Model<tdoctree_input_inv_ln8>`                          | reference model                                                   |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 9      | :ref:`Active Topography Cells<tdoctree_input_inv_ln9>`                  | topography                                                        |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 10     | :ref:`Active Model Cells<tdoctree_input_inv_ln10>`                      | active model cells                                                |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 11     | :ref:`Cell Weights<tdoctree_input_inv_ln11>`                            | additional cell weights                                           |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 12     | :ref:`Face Weights<tdoctree_input_inv_ln12>`                            | additional face weights                                           |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 13     | :ref:`Sensitivity Weighting<tdoctree_input_inv_ln13>`                   | sensitivity weighting                                             |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 14     | :ref:`beta_max beta_min beta_factor<tdoctree_input_inv_ln14>`           | cooling schedule for beta parameter                               |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 15     | :ref:`alpha_s alpha_x alpha_y alpha_z<tdoctree_input_inv_ln15>`         | weighting constants for smallness and smoothness constraints      |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 16     | :ref:`Chi Factor<tdoctree_input_inv_ln16>`                              | stopping criteria for inversion                                   |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 17     | :ref:`iter_per_beta nbetas<tdoctree_input_inv_ln17>`                    | set the number of Gauss-Newton iteration for each beta value      |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 18     | :ref:`tol_ipcg max_iter_ipcg<tdoctree_input_inv_ln18>`                  | set the tolerance and number of iterations for Gauss-Newton solve |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 19     | :ref:`Reference Model Update<tdoctree_input_inv_ln19>`                  | reference model                                                   |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 20     | :ref:`Hard Constraints<tdoctree_input_inv_ln20>`                        | use *SMOOTH_MOD* or *SMOOTH_MOD_DIFF*                             |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 21     | :ref:`Bounds<tdoctree_input_inv_ln21>`                                  | upper and lower bounds for recovered model                        |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 22     | :ref:`huber_c ntilesens<tdoctree_input_inv_ln22>`                       | Huber constant (for sparse model recovery)                        |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 23     | :ref:`Time Channel Indecies<tdoctree_input_inv_ln23>`                   | chooses time channels to be inverted                              |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 24     | :ref:`Active tiles file<tdoctree_input_inv_ln24>`                       | chooses tiles that are used in the sensitivity approximation      |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+



.. .. figure:: images/create_inv_input.png
..      :align: center
..      :width: 700

..      Example input file for the inversion program (`Download <https://github.com/ubcgif/tdoctree/raw/tdoctreeinv/assets/input_files1/tdoctreeinv.inp>`__ ). Example input file for forward modeling only (`Download <https://github.com/ubcgif/tdoctree/raw/tdoctreeinv/assets/input_files1/tdoctreefwd.inp>`__ ).


Line Descriptions
^^^^^^^^^^^^^^^^^

.. _tdoctree_input_inv_ln1:

    - **Inversion Mesh:** file path to the inversion (OcTree) mesh file (**link needed**)

.. _tdoctree_input_inv_ln2:

    - **Forward Mesh:** file path to the forward tile (OcTree) meshes file (**link needed**)

.. _tdoctree_input_inv_ln3:

    - **nTiles:** the total number of OcTree mesh tiles in the forward tile mesh file. This number can be found in the *log* file for OcTree mesh creation (**link**)

.. _tdoctree_input_inv_ln4:

    - **Observation File:** file path to the :ref:`observed data file<obsFile>` or a :ref:`survey file<surveyFile>` (forward modeling only).

.. _tdoctree_input_inv_ln5:
    
    - **Transmitter Options:** On this line, we can change some aspects as to how the transmitter is discretized to the mesh. There are 3 flags that can be entered:

        - *MOVE_TRX_EDGE:* Discretizes the transmitters to the nearest edge. Useful for large loop transmitters
        - *MOVE_TRX_CENTER:* Discretizes the transmitters to the nearest cell center. Useful for dipole transmitters
        - *NOT_MOVE_TRX:* Do no move the location of transmitters. Keep same as in survey file

.. _tdoctree_input_inv_ln6:
    
    - **Wave file:** Set the path to a :ref:`wave file<waveFile>`. This file defines the time-steps for the problem.

.. _tdoctree_input_inv_ln7:

    - **Initial/FWD Model:** On this line we specify either the starting model for the inversion or the conductivity model for the forward modeling. On this line, there are 3 possible options:

        - If the program is being used to forward model data, the flag 'FWDMODEL' is entered followed by the path to the conductivity model.
        - If the program is being used to invert data, only the path to a conductivity model is required; e.g. inversion is assumed unless otherwise specified.
        - If a homogeneous conductivity value is being used as the starting model for an inversion, the user can enter "VALUE" followed by a space and a numerical value; example "VALUE 0.01".


.. important::

    If data are only being forward modeled, only the :ref:`active topography cells<tdoctree_input_inv_ln7>` and :ref:`tol_ipcg max_iter_ipcg<tdoctree_input_inv_ln16>` fields are relevant. **However**, the remaining fields must **not** be empty and must have correct syntax for the code to run.

.. _tdoctree_input_inv_ln8:

    - **Reference Model:** The user may supply the file path to a reference conductivity model. If a homogeneous conductivity value is being used for all active cells, the user can enter "VALUE" followed by a space and a numerical value; example "VALUE 0.01".


.. _tdoctree_input_inv_ln9:

    - **Active Topography Cells:** Here, the user can choose to specify the cells which lie below the surface topography. To do this, the user may supply the file path to an active cells model file or type "ALL_ACTIVE". The active cells model has values 1 for cells lying below the surface topography and values 0 for cells lying above.

.. _tdoctree_input_inv_ln10:

    - **Active Model Cells:** Here, the user can choose to specify the model cells which are active during the inversion. To do this, the user may supply the file path to an active cells model file or type "ALL_ACTIVE". The active cells model has values 1 for cells lying below the surface topography and values 0 for cells lying above. Values for inactive cells are provided by the background conductivity model.

.. _tdoctree_input_inv_ln11:

    - **Cell Weights:** Here, the user specifies whether cell weights are supplied. If so, the user provides the file path to a :ref:`cell weights file <weightsFile>`  If no additional cell weights are supplied, the user enters "NO_WEIGHT".

.. _tdoctree_input_inv_ln12:

    - **Face Weights:** Here, the user specifies whether face weights are supplied. If so, the user provides the file path to a face weights file :ref:`cell weights file <weightsFile>`. If no additional cell weights are supplied, the user enters "NO_FACE_WEIGHT". The user may also enter "EKBLOM" for 1-norm approximation to recover sharper edges.

.. _tdoctree_input_inv_ln13:
    
    - **Sensitivity Weighting:**

.. _tdoctree_input_inv_ln14:

    - **beta_max beta_min beta_factor:** Here, the user specifies protocols for the trade-off parameter (beta). *beta_max* is the initial value of beta, *beta_min* is the minimum allowable beta the program can use before quitting and *beta_factor* defines the factor by which beta is decreased at each iteration; example "1E4 10 0.2". The user may also enter "DEFAULT" if they wish to have beta calculated automatically.

.. _tdoctree_input_inv_ln15:

    - **alpha_s alpha_x alpha_y alpha_z:** `Alpha parameters <http://giftoolscookbook.readthedocs.io/en/latest/content/fundamentals/Alphas.html>`__ . Here, the user specifies the relative weighting between the smallness and smoothness component penalties on the recovered models.

.. _tdoctree_input_inv_ln16:

    - **Chi Factor:** The chi factor defines the target misfit for the inversion. A chi factor of 1 means the target misfit is equal to the total number of data observations.

.. _tdoctree_input_inv_ln17:

    - **iter_per_beta nBetas:** Here, *iter_per_beta* is the number of Gauss-Newton iterations per beta value. *nBetas* is the number of times the inverse problem is solved for smaller and smaller trade-off parameters until it quits. See theory section for :ref:`cooling schedule <theory_cooling>` and :ref:`Gauss-Newton update <theory_GN>`.

.. _tdoctree_input_inv_ln18:

    - **tol_ipcg max_iter_ipcg:** Here, the user specifies solver parameters. *tol_ipcg* defines how well the iterative solver does when solving for :math:`\delta m` and *max_iter_ipcg* is the maximum iterations of incomplete-preconditioned-conjugate gradient. See theory on :ref:`Gauss-Newton solve <theory_IPCG>`

.. _tdoctree_input_inv_ln19:

    - **Reference Model Update:** Here, the user specifies whether the reference model is updated at each inversion step result. If so, enter "CHANGE_MREF". If not, enter "NOT_CHANGE_MREF".

.. _tdoctree_input_inv_ln20:

    - **Hard Constraints:** SMOOTH_MOD runs the inversion without implementing a reference model (essential :math:`m_{ref}=0`). "SMOOTH_MOD_DIF" constrains the inversion in the smallness and smoothness terms using a reference model.

.. _tdoctree_input_inv_ln21:

    - **Bounds:** Bound constraints on the recovered model. Choose "BOUNDS_CONST" and enter the values of the minimum and maximum model conductivity; example "BOUNDS_CONST 1E-6 0.1". Enter "BOUNDS_NONE" if the inversion is unbounded, or if there is no a-prior information about the subsurface model.


.. _tdoctree_input_inv_ln22:

    - **huber_c:** Here, the user may control the sparseness of the recovered model by specifying the Huber constant (:math:`\epsilon`) within the Huber norm. The TDoctree code uses the Huber norm to define the smallness term (link) in the inversion. If a large value is used (*default = 10000*), the inversion will use an L2 norm for the smallness. If a sufficiently small value is used, the smallness will be similar to an L1 norm. The Huber norm is given by:

.. math::
    \sum_{i=1}^M x_i^2 \;\;\;\; \textrm{where} \;\;\;\; x_i = \begin{cases} \sigma_i^2 \;\; \textrm{for} \;\; \sigma_i \leq \epsilon \\ \epsilon \big ( 2 |\sigma_i | - \epsilon \big ) \;\; \textrm{for} \;\; \sigma_i > \epsilon    \end{cases}

    - **ntilesens:**

.. _tdoctree_input_inv_ln23:

    **Time channel indecies:** If the user would like to invert the data at all time channels, the flag "ALL_TIME_CHANNELS" is entered. At times the user may want to invert early time channels, then use the corresponding recovered model as a starting model for an inversion that includes data at later time channels. In the latter case, the user provides the *filepath* to a :ref:`time indecies file <timeindeciesFile>`

.. _tdoctree_input_inv_ln24:

    - **Active tiles file:** This line allows the user to invert only a subset of the data by specifying the tiles (local forward meshes) they wish to be used in the inversion. If the flag *USE_ALL_TILES* is used, then all the data are inverted; e.g. all the tiles are used. If the path to an :ref:`active tiles file<activeTilesFile>` is used, then only the 'active tiles' are inverted. The active tiles file is a vector of 1s and 0s, where a 1 denotes a local forward mesh that is used in the inversion, and a zero denotes a local forward mesh that is not. The number of values in the active tiles file must equal the number of local forward meshes.
