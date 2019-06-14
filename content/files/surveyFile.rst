.. _surveyFile:

Survey and Locations File
=========================

The survey and locations file is used to predict synthetic field data (forward modeling). This file contains all necessary survey information including: the number of transmitters, transmitter geometry, observation locations and frequencies. 

.. note:: Bolded entries are fixed flags recognized by the Fortran codes and blue hyperlinked entries are values/regular expressions specified by the user


The lines of the survey file are formatted as follows:

|
| **N_TRX** :math:`\;` :ref:`n_trx<tdoctree_survey_ln1>`
|
|
| :ref:`DEFINE TRANSMITTER<tdoctree_survey_transmitter>`
| 
| **N_RECV** :math:`\;` :ref:`n_recv<tdoctree_survey_ln2>`
| **N_TIME** :math:`\;` :ref:`n_time<tdoctree_survey_ln3>`
| :math:`\;\;` :ref:`Loc-Time Array<tdoctree_survey_ln4>`
|
|
| :ref:`DEFINE TRANSMITTER<tdoctree_survey_transmitter>`
|
| **N_RECV** :math:`\;` :ref:`n_recv<tdoctree_survey_ln2>`
| **N_TIME** :math:`\;` :ref:`n_time<tdoctree_survey_ln3>`
| :math:`\;\;` :ref:`Loc-Time Array<tdoctree_survey_ln4>`
|
|
| :math:`\;\;\;\;\;\; \vdots`
|
|
| :ref:`DEFINE TRANSMITTER<tdoctree_survey_transmitter>`
|
| **N_RECV** :math:`\;` :ref:`n_recv<tdoctree_survey_ln2>`
| **N_TIME** :math:`\;` :ref:`n_time<tdoctree_survey_ln3>`
| :math:`\;\;` :ref:`Loc-Time Array<tdoctree_survey_ln4>`
|
| *Repeat for number of unique transmitters*
|
|


.. figure:: images/files_locations.png
     :align: center
     :width: 400

     Example survey file with various types of transmitters.



Parameter Descriptions
----------------------


.. _tdoctree_survey_ln1:

    - **n_trx:** The total number of unique transmitters. Example: *N_TRX 3*

.. _tdoctree_survey_ln2:

    - **n_recv:** The number of receivers collecting field observations for a particular transmitter.

.. _tdoctree_survey_ln3:

    - **n_time:** The number of time channels for each receiver

.. _tdoctree_survey_ln4:

    - **Loc-Time Array:** Contains the X (Easting), Y (Northing), Z (elevation) locations and time channels for all receivers for a particular transmitter. It has has :ref:`n_recv<tdoctree_survey_ln2>` :math:`\times` :ref:`n_time<tdoctree_survey_ln2>` rows and 4 columns. The time-locations array is organized as follows:

|
|  :math:`x_1 \;\; y_1 \;\; z_1 \;\; t_1`
|  :math:`x_1 \;\; y_1 \;\; z_1 \;\; t_2`
|  :math:`\; \vdots \;\;\;\;\, \vdots \;\;\;\;\, \vdots \;\;\;\;\, \vdots`
|  :math:`x_1 \;\; y_1 \;\; z_1 \;\; t_{n}`
|  :math:`x_2 \;\; y_2 \;\; z_2 \;\; t_1`
|  :math:`x_2 \;\; y_2 \;\; z_2 \;\; t_2`
|  :math:`\; \vdots \;\;\;\;\, \vdots \;\;\;\;\, \vdots \;\;\;\;\, \vdots`
|  :math:`x_2 \;\; y_2 \;\; z_2 \;\; t_{n}`
|  :math:`\; \vdots \;\;\;\;\, \vdots \;\;\;\;\, \vdots \;\;\;\;\, \vdots`
|  :math:`\; \vdots \;\;\;\;\, \vdots \;\;\;\;\, \vdots \;\;\;\;\, \vdots`
|  :math:`\; \vdots \;\;\;\;\, \vdots \;\;\;\;\, \vdots \;\;\;\;\, \vdots`
|  :math:`x_m \; y_m \; z_m \; t_1`
|  :math:`x_m \; y_m \; z_m \; t_2`
|  :math:`\; \vdots \;\;\;\;\, \vdots \;\;\;\;\, \vdots \;\;\;\;\, \vdots`
|  :math:`x_m \; y_m \; z_m \; t_{n}`
|
|


.. _tdoctree_survey_transmitter:

Defining Transmitters
---------------------

There are two types of transmitters that *TDoctree* survey files can use

Circular loop transmitter
~~~~~~~~~~~~~~~~~~~~~~~~~

This is an inductive source. The circular loop transmitter is defined using two lines:

|
| *TRX_LOOP*
| :math:`x \;\; y \;\; z \;\; R \;\; \theta \;\; \alpha`
|
|

where

    - *TRX_LOOP* is a flag that must be entered
    - :math:`x` is the Easting, :math:`y` is the Northing and :math:`z` is the elevation location of the center of the loop
    - :math:`R` is the radius of the loop
    - :math:`\theta` is the azimuthal angle in degrees. A horizontal loop is defined by :math:`\theta = 0`
    - :math:`\alpha` is the clockwise angle from northing in degrees


Large inductive source
~~~~~~~~~~~~~~~~~~~~~~

Here, we define the inductive source using a set of wire segments. When defining this type of transmitter, you **must** close the loop. The block defining this transmitter is given by:

|
| *TRX_LINES*
| :math:`N`
| :math:`x_1 \;\;\; y_1 \;\;\; z_1`
| :math:`x_2 \;\;\; y_2 \;\;\; z_2`
| :math:`\; \vdots \;\;\;\;\;\, \vdots \;\;\;\;\;\, \vdots`
| :math:`x_N \;\; y_N \;\; z_N`
| 
|

where

    - *TRX_LINES* is a flag that must be entered
    - :math:`N` is the number of nodes (# segments - 1)
    - :math:`x_i, \; y_i \; z_i` are Easting, Northing and elevation locations for the nodes















