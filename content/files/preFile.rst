.. _preFile:

Predicted Data File
===================

predicted data files output from **e3dfwd_pardiso.exe** and **e3dinv_pardiso.exe** contain the locations and predicted data. The order of the data points is in the same order as the :ref:`survey and locations file <surveyFile>`. Each block, separated by a blank line, are the data for a particular transmitter and frequency. Thus predicted data files take the format:

|
| **Data Array 1**
|
| **Data Array 2**
|
| :math:`\;\;\;\;\;\;\;\; \vdots`
|
| **Data Array N**
|
|


Data Array
----------

.. important:: The data are represented in a left-handed coordinate system where X is Easting, Y is Northing and Z is +ve downward.


For each transmitter at each frequency, a set of field observations are made for a set of receivers. These field observations include real and imaginary components of the electric and magnetic fields. The rows of the data array are formatted as follows:

.. math::
    | \; x \; | \; y \; | \; z \; | \;\;\; E_x \; data \;\;\; | \;\;\; E_y \; data \;\;\; | \;\;\; E_z \; data \;\;\; | \;\;\; H_x \; data \;\;\; | \;\;\; H_y \; data \;\;\; | \;\;\; H_z \; data \;\;\; |

such that :math:`E_x \; data` is comprised of 2 columns:

.. math::

    | \; E_x^\prime \; | \; E_x^{\prime \prime} \; |

where

    - :math:`E_x^\prime` is the real component of the electric field along the Easting direction
    - :math:`E_x^{\prime\prime}` is the imaginary component of the electric field along the Easting direction


This is done likewise for :math:`E_y`, :math:`E_z`, :math:`H_x`, :math:`E_y`, :math:`H_z`.












