.. _receiverFile:

Transmitter and Receiver Files
==============================

The exact dimensions of the transmitters or receivers used to model FEM data are defined within a transmitter or a receiver file, respectively; i.e. transmitters are defined within a transmitter file and receivers are defined within a receivers file. Because transmitters and receivers are both defined as wire segments, the format of the transmitter and receiver files is identical.

.. note::
    - Bolded entries are fixed flags recognized by the Fortran codes and blue hyperlinked entries are values/regular expressions specified by the user


Format
------

The lines of a transmitter/receiver file are formatted as follows:


|
| :ref:`ID<tdoctree_rec_ln1>` :math:`\;` :ref:`N<tdoctree_rec_ln2>` :math:`\;` :ref:`1<tdoctree_rec_ln3>`
| :math:`\;\;\; x_1 \; y_1 \; z_1`
| :math:`\;\;\;\;\;\;\;\; \vdots`
| :math:`\;\; x_N \; y_N \; z_N`
| :ref:`ID<tdoctree_rec_ln1>` :math:`\;` :ref:`N<tdoctree_rec_ln2>` :math:`\;` :ref:`1<tdoctree_rec_ln3>`
| :math:`\;\;\; x_1 \; y_1 \; z_1`
| :math:`\;\;\;\;\;\;\;\; \vdots`
| :math:`\;\; x_N \; y_N \; z_N`
| :ref:`ID<tdoctree_rec_ln1>` :math:`\;` :ref:`N<tdoctree_rec_ln2>` :math:`\;` :ref:`1<tdoctree_rec_ln3>`
| :math:`\;\;\; x_1 \; y_1 \; z_1`
| :math:`\;\;\;\;\;\;\;\; \vdots`
| :math:`\;\; x_N \; y_N \; z_N`
|
|


Parameter Descriptions
----------------------


.. _tdoctree_rec_ln1:

    - **ID:** A unique index number for the transmitter or receiver. The index numbers should be increasing.

.. _tdoctree_rec_ln2:

    - **N:** The number of points defining the transmitter/receiver loop. The first and last node **must** be the same; i.e. you must close the loop.

.. _tdoctree_rec_ln3:

    - **1:** As of May 2018, a flag value of 1 is entered here. In future iterations of the code, this entry may be related to additional functionality.
        
.. _tdoctree_rec_ln4:

    - :math:`\mathbf{x_i \;\; y_i \;\; z_i}`: Denotes the X (Easting), Y (Northing) and Z (elevation) locations for nodes defining transmitter/receiver.

    	- **Loop transmitter/receiver:** Loop transmitters and receivers **must** be defined in a left-handed (clockwise) manner. For example, a horizontal loop must be defined in a clockwise manner for its dipole moment to be in the vertical direction. For *N* loop segments, you will need to define *N+1* nodes; e.g. you must close the loop. If a closed loop is used to define a receiver, the corresponding data are the magnetic field in units A/m.
    	
    	- **Wire transmitter/receiver:** Wire transmitters and receivers are defined using 2 node locations. If a single wire segment is used to define a receiver, the corresponding data are the electric field in units V/m.
















