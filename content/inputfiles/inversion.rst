.. _tdoctree_input_inv:

Inversion Input File
====================

The inverse problem is solved using the executable program **tdoctreeinv.exe**. The lines of input file are as follows:

.. tabularcolumns:: |L|C|C|

+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| Line # | Description                                                             | Description                                                       |
+========+=========================================================================+===================================================================+
| 1      | :ref:`OcTree Mesh<tdoctree_input_inv_ln1>`                              | path to octree mesh file                                          |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 2      | :ref:`Observation File<tdoctree_input_inv_ln2>`                         | path to observations/survey file                                  |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 3      | :ref:`Transmitter options<tdoctree_input_inv_ln3>`                      | discretization options for transmitters                           |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 4      | :ref:`Wave file<tdoctree_input_inv_ln4>`                                | sets time steps for the time-dependent problem                    |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 5      | :ref:`Initial/FWD Model<tdoctree_input_inv_ln5>`                        | initial/forward model                                             |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 6      | :ref:`Reference Model<tdoctree_input_inv_ln6>`                          | reference model                                                   |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 7      | :ref:`Susceptibility model<tdoctree_input_inv_ln7>`                     | susceptibility model                                              |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 8      | :ref:`Active Topography Cells<tdoctree_input_inv_ln8>`                  | topography                                                        |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 9      | :ref:`Active Model Cells<tdoctree_input_inv_ln9>`                       | active model cells                                                |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 10     | :ref:`Cell Weights<tdoctree_input_inv_ln10>`                            | additional cell weights                                           |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 11     | :ref:`Face Weights<tdoctree_input_inv_ln11>`                            | additional face weights                                           |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 12     | :ref:`beta_max beta_min beta_factor<tdoctree_input_inv_ln12>`           | cooling schedule for beta parameter                               |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 13     | :ref:`alpha_s alpha_x alpha_y alpha_z<tdoctree_input_inv_ln13>`         | weighting constants for smallness and smoothness constraints      |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 14     | :ref:`Chi Factor<tdoctree_input_inv_ln14>`                              | stopping criteria for inversion                                   |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 15     | :ref:`iter_per_beta nbetas<tdoctree_input_inv_ln15>`                    | set the number of Gauss-Newton iteration for each beta value      |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 16     | :ref:`tol_ipcg max_iter_ipcg<tdoctree_input_inv_ln16>`                  | set the tolerance and number of iterations for Gauss-Newton solve |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 17     | :ref:`Reference Model Update<tdoctree_input_inv_ln17>`                  | reference model                                                   |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 18     | :ref:`Hard Constraints<tdoctree_input_inv_ln18>`                        | use *SMOOTH_MOD* or *SMOOTH_MOD_DIFF*                             |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 19     | :ref:`Bounds<tdoctree_input_inv_ln19>`                                  | upper and lower bounds for recovered model                        |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 20     | :ref:`huber_c<tdoctree_input_inv_ln20>`                                 | Huber constant                                                    |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+
| 21     | :ref:`Time Channel Indecies<tdoctree_input_inv_ln21>`                   | chooses time channels to be inverted                              |
+--------+-------------------------------------------------------------------------+-------------------------------------------------------------------+



.. .. figure:: images/create_inv_input.png
..      :align: center
..      :width: 700

..      Example input file for the inversion program (`Download <https://github.com/ubcgif/tdoctree/raw/tdoctreeinv/assets/input_files1/tdoctreeinv.inp>`__ ). Example input file for forward modeling only (`Download <https://github.com/ubcgif/tdoctree/raw/tdoctreeinv/assets/input_files1/tdoctreefwd.inp>`__ ).


Line Descriptions
^^^^^^^^^^^^^^^^^

.. _tdoctree_input_inv_ln1:

    - **OcTree Mesh:** file path to the OcTree mesh file

.. _tdoctree_input_inv_ln2:

    - **Observation File:** file path to the :ref:`observed data file<obsFile>` or a :ref:`survey file<surveyFile>` (forward modeling only).

.. _tdoctree_input_inv_ln3:
    
    - **Transmitter Options:** On this line, we can change some aspects as to how the transmitter is discretized to the mesh. There are 3 flags that can be entered:

        - *MOVE_TRX_EDGE:* Discretizes the transmitters to the nearest edge. Useful for large loop transmitters
        - *MOVE_TRX_CENTER:* Discretizes the transmitters to the nearest cell center. Useful for dipole transmitters
        - *NOT_MOVE_TRX:* Do no move the location of transmitters. Keep same as in survey file

.. _tdoctree_input_inv_ln4:
    
    - **Wave file:** Set the path to a :ref:`wave file<waveFile>`. This file defines the time-steps for the problem.

.. _tdoctree_input_inv2_ln5:

    - **Initial/FWD Model:** On this line we specify either the starting model for the inversion or the conductivity model for the forward modeling. On this line, there are 3 possible options:

        - If the program is being used to forward model data, the flag 'FWDMODEL' is entered followed by the path to the conductivity model.
        - If the program is being used to invert data, only the path to a conductivity model is required; e.g. inversion is assumed unless otherwise specified.
        - If a homogeneous conductivity value is being used as the starting model for an inversion, the user can enter "VALUE" followed by a space and a numerical value; example "VALUE 0.01".


.. important::

    If data are only being forward modeled, only the :ref:`active topography cells<e3d_input_inv2_ln7>` and :ref:`tol_ipcg max_iter_ipcg<e3d_input_inv2_ln16>` fields are relevant. **However**, the remaining fields must **not** be empty and must have correct syntax for the code to run.

.. _tdoctree_input_inv_ln6:

    - **Reference Model:** The user may supply the file path to a reference conductivity model. If a homogeneous conductivity value is being used for all active cells, the user can enter "VALUE" followed by a space and a numerical value; example "VALUE 0.01".

.. _tdoctree_input_inv_ln7:

    - **Susceptibility Model:** The user may supply the file path to a background susceptibility model. If a homogeneous conductivity value is being used for all active cells, the user can enter "VALUE" followed by a space and a numerical value; example "VALUE 0.01".


.. _tdoctree_input_inv_ln8:

    - **Active Topography Cells:** Here, the user can choose to specify the cells which lie below the surface topography. To do this, the user may supply the file path to an active cells model file or type "ALL_ACTIVE". The active cells model has values 1 for cells lying below the surface topography and values 0 for cells lying above.

.. _tdoctree_input_inv_ln9:

    - **Active Model Cells:** Here, the user can choose to specify the model cells which are active during the inversion. To do this, the user may supply the file path to an active cells model file or type "ALL_ACTIVE". The active cells model has values 1 for cells lying below the surface topography and values 0 for cells lying above. Values for inactive cells are provided by the background conductivity model.

.. _tdoctree_input_inv_ln10:

    - **Cell Weights:** Here, the user specifies whether cell weights are supplied. If so, the user provides the file path to a :ref:`cell weights file <weightsFile>`  If no additional cell weights are supplied, the user enters "NO_WEIGHT".

.. _tdoctree_input_inv_ln11:

    - **Face Weights:** Here, the user specifies whether face weights are supplied. If so, the user provides the file path to a face weights file :ref:`cell weights file <weightsFile>`. If no additional cell weights are supplied, the user enters "NO_FACE_WEIGHT". The user may also enter "EKBLOM" for 1-norm approximation to recover sharper edges.

.. _tdoctree_input_inv_ln12:

    - **beta_max beta_min beta_factor:** Here, the user specifies protocols for the trade-off parameter (beta). *beta_max* is the initial value of beta, *beta_min* is the minimum allowable beta the program can use before quitting and *beta_factor* defines the factor by which beta is decreased at each iteration; example "1E4 10 0.2". The user may also enter "DEFAULT" if they wish to have beta calculated automatically.

.. _tdoctree_input_inv_ln13:

    - **alpha_s alpha_x alpha_y alpha_z:** `Alpha parameters <http://giftoolscookbook.readthedocs.io/en/latest/content/fundamentals/Alphas.html>`__ . Here, the user specifies the relative weighting between the smallness and smoothness component penalties on the recovered models.

.. _tdoctree_input_inv_ln14:

    - **Chi Factor:** The chi factor defines the target misfit for the inversion. A chi factor of 1 means the target misfit is equal to the total number of data observations.

.. _e3d_input_inv2_ln15:

    - **iter_per_beta nBetas:** Here, *iter_per_beta* is the number of Gauss-Newton iterations per beta value. *nBetas* is the number of times the inverse problem is solved for smaller and smaller trade-off parameters until it quits. See theory section for :ref:`cooling schedule <theory_cooling>` and :ref:`Gauss-Newton update <theory_GN>`.

.. _e3d_input_inv2_ln16:

    - **tol_ipcg max_iter_ipcg:** Here, the user specifies solver parameters. *tol_ipcg* defines how well the iterative solver does when solving for :math:`\delta m` and *max_iter_ipcg* is the maximum iterations of incomplete-preconditioned-conjugate gradient. See theory on :ref:`Gauss-Newton solve <theory_IPCG>`

.. _tdoctree_input_inv_ln17:

    - **Reference Model Update:** Here, the user specifies whether the reference model is updated at each inversion step result. If so, enter "CHANGE_MREF". If not, enter "NOT_CHANGE_MREF".

.. _tdoctree_input_inv_ln18:

    - **Hard Constraints:** SMOOTH_MOD runs the inversion without implementing a reference model (essential :math:`m_{ref}=0`). "SMOOTH_MOD_DIF" constrains the inversion in the smallness and smoothness terms using a reference model.

.. _tdoctree_input_inv_ln19:

    - **Bounds:** Bound constraints on the recovered model. Choose "BOUNDS_CONST" and enter the values of the minimum and maximum model conductivity; example "BOUNDS_CONST 1E-6 0.1". Enter "BOUNDS_NONE" if the inversion is unbounded, or if there is no a-prior information about the subsurface model.


.. _tdoctree_input_inv_ln20:

    - **Huber constant:** 

.. _tdoctree_input_inv_ln21:

    - **Time channel indecies:**
