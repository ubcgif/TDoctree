.. _obsFile:

Observations File
=================

The observations contains the set of field measurements used in the inversion. This file contains all necessary survey information including: the number of transmitters, transmitter geometry, observation locations, frequencies, observed fields and uncertainties. 

.. note:: Bolded entries are fixed flags recognized by the Fortran codes and blue hyperlinked entries are values/regular expressions specified by the user


The lines the observations file are formatted as follows:


|
| **IGNORE** :ref:`reg_exp<tdoctree_obs_ln0>`
|
| **N_TRX** :math:`\;` :ref:`n_trx<tdoctree_obs_ln1>`
|
|
| :ref:`DEFINE TRANSMITTER<tdoctree_obs_transmitter>`
| 
| **N_RECV** :math:`\;` :ref:`n_recv<tdoctree_obs_ln2>`
| **N_TIME** :math:`\;` :ref:`n_time<tdoctree_obs_ln3>`
| :math:`\;\;` :ref:`Data Array<tdoctree_obs_ln4>`
|
|
| :ref:`DEFINE TRANSMITTER<tdoctree_obs_transmitter>`
|
| **N_RECV** :math:`\;` :ref:`n_recv<tdoctree_obs_ln2>`
| **N_TIME** :math:`\;` :ref:`n_time<tdoctree_obs_ln3>`
| :math:`\;\;` :ref:`Data Array<tdoctree_obs_ln4>`
|
|
| :math:`\;\;\;\;\;\;\;\;\;\; \vdots`
|
|
| :ref:`DEFINE TRANSMITTER<tdoctree_obs_transmitter>`
|
| **N_RECV** :math:`\;` :ref:`n_recv<tdoctree_obs_ln2>`
| **N_TIME** :math:`\;` :ref:`n_time<tdoctree_obs_ln3>`
| :math:`\;\;` :ref:`Data Array<tdoctree_obs_ln4>`
|
| *Repeat for number of unique transmitters*
|
|


.. .. figure:: images/files_locations.png
..      :align: center
..      :width: 400

..      Example survey file with various types of transmitters.



Parameter Descriptions
----------------------

.. _tdoctree_obs_ln0:

    - **reg_exp:** A regular expression denoting which data are ignored during the inversion; examples include *-9999* and *NaN*

.. _tdoctree_obs_ln1:

    - **n_trx:** The total number of unique transmitters. Example: *N_TRX 3*

.. _tdoctree_obs_ln2:

    - **n_recv:** The number of receivers collecting field observations for a particular transmitter.

.. _tdoctree_obs_ln3:

    - **n_time:** The number of time channels for each receiver

.. _tdoctree_obs_ln4:

    - **Data Array:** Contains the X (Easting), Y (Northing), Z (elevation) locations and time channels for all receivers for a particular transmitter. It has has :ref:`n_recv<tdoctree_obs_ln2>` :math:`\times` :ref:`n_time<tdoctree_obs_ln2>` rows and 22 columns. The time-locations array is organized as follows:

.. math::
    \begin{align}
    &| \;\, x_1 \,\; | \;\, y_1 \,\; | \;\, z_1 \,\; | \; t_1 \; | \;\;\; E \; data \;\;\; | \;\;\; H \; data \;\;\; | \;\;\; dB/dt \; data \;\;\; | \\
    &| \;\, x_1 \,\; | \;\, y_1 \,\; | \;\, z_1 \,\; | \; t_2 \; | \;\;\; E \; data \;\;\; | \;\;\; H \; data \;\;\; | \;\;\; dB/dt \; data \;\;\; | \\
    &\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; \vdots \\
    &| \;\, x_1 \,\; | \;\, y_1 \,\; | \;\, z_1 \,\; | \; t_n \; | \;\;\; E \; data \;\;\; | \;\;\; H \; data \;\;\; | \;\;\; dB/dt \; data \;\;\; | \\
    &| \;\, x_2 \,\; | \;\, y_2 \,\; | \;\, z_2 \,\; | \; t_1 \; | \;\;\; E \; data \;\;\; | \;\;\; H \; data \;\;\; | \;\;\; dB/dt \; data \;\;\; | \\
    &| \;\, x_2 \,\; | \;\, y_2 \,\; | \;\, z_2 \,\; | \; t_2 \; | \;\;\; E \; data \;\;\; | \;\;\; H \; data \;\;\; | \;\;\; dB/dt \; data \;\;\; | \\
    &\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; \vdots \\
    &| \;\, x_2 \,\; | \;\, y_2 \,\; | \;\, z_2 \,\; | \; t_n \; | \;\;\; E \; data \;\;\; | \;\;\; H \; data \;\;\; | \;\;\; dB/dt \; data \;\;\; | \\
    &\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; \vdots \\
    &\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; \vdots \\
    &\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; \vdots \\
    &| \; x_m \; | \; y_m \; | \; z_m \; | \; t_1 \; | \;\;\; E \; data \;\;\; | \;\;\; H \; data \;\;\; | \;\;\; dB/dt \; data \;\;\; | \\
    &| \; x_m \; | \; y_m \; | \; z_m \; | \; t_2 \; | \;\;\; E \; data \;\;\; | \;\;\; H \; data \;\;\; | \;\;\; dB/dt \; data \;\;\; | \\
    &\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; \vdots \\
    &| \; x_m \; | \; y_m \; | \; z_m \; | \; t_n \; | \;\;\; E \; data \;\;\; | \;\;\; H \; data \;\;\; | \;\;\; dB/dt \; data \;\;\; |
    \end{align}


|
|

such that :math:`E \; data` is in units V/m and is comprised of 6 columns:

.. math::

    | \; E_x \; | \; E_x \; Unc. \; | \; E_y \; | \; E_y \; Unc. \; | \; E_z \; | \; E_z \; Unc. \; |

:math:`H \; data` is in units A/m and is comprised of 6 columns:

.. math::

    | \; H_x \; | \; H_x \; Unc. \; | \; H_y \; | \; H_y \; Unc. \; | \; H_z \; | \; H_z \; Unc. \; |

and :math:`dB/dt \; data` is in units T/s and is comprised of 6 columns:

.. math::

    | \; dB_x/dt \; | \; dB_x/dt \; Unc. \; | \; dB_y/dt \; | \; dB_y/dt \; Unc. \; | \; -dB_z/dt \; | \; -dB_z/dt \; Unc. \; |



.. important::

    - The data are represented in a left-handed coordinate system where X is Easting, Y is Northing and Z is +ve downward.
    - The vertical component of dB/dt is represented using :math:`\mathbf{-dB_z/dt}` **!!!** This is done due to a common plotting convention. The associated uncertainties are still positive values however!


.. _tdoctree_obs_transmitter:

Defining Transmitters
---------------------

There are two types of transmitters that *TDoctree* survey files can use. These were defined in the :ref:`survey file section <tdoctree_obs_transmitter>`.


