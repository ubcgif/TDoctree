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




Below is an example of a transmitters or receivers file where the respective items are closed horizontal loops.

.. figure:: images/receiver_file.png
     :align: center
     :width: 500

Parameter Descriptions
----------------------


.. _tdoctree_rec_ln1:

    - **ID:** A unique index number for the transmitter or receiver. The index numbers should be increasing.

.. _tdoctree_rec_ln2:

    - **N:** The number of points defining the transmitter/receiver.

.. _tdoctree_rec_ln3:

    - **1:** As of May 2018, a flag value of 1 is entered here. In future iterations of the code, this entry may be related to additional functionality.
        
.. _tdoctree_rec_ln4:

    - :math:`\mathbf{x_i \;\; y_i \;\; z_i}`: Denotes the X (Easting), Y (Northing) and Z (elevation) locations for nodes defining transmitter/receiver.

    	- **Loop transmitter/receiver:** When defining a loop transmitter or receiver, you **must** close the loop; e.g. the fist and last nodes must be at the same locations. The transmitters and receivers are defined in a **left-handed (clockwise) manner**. For example, a horizontal loop must be defined in a clockwise manner for its dipole moment to be in the vertical direction.
        
        - **Wire transmitter/receiver:** If the first and last nodes are not in the same place, the user will define a grounded receiver which measures the electric field. The grounded loop can be more than one segment long.


Comments on Data Units
----------------------

**Electric Field Data (e):** For electric field measurements, the code intergrates the electric field of the path of the wire then divides by its length. Thus electric field data are the average electric field measured over the path of the wire in units V/m.

**Time-Derivative of Magnetic Flux Data (db/dt):** For dB/dt measurements, the code integrates the electric field over the path of the wire, multiplies by -1 (giving the EMF), then normalizes by the area of the loop. The data are therefore the average value of dB/dt in the direction of the loop's moment. The units for dB/dt measurements are T/s.

**Magnetic Field Data (h):** Similar to the time-derivative, H-field measurements represent the average value H in the direction of the loop's moment. H-field measurements are in units of A/m. 














