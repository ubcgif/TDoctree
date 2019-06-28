.. _preFile:

Predicted Data File
===================

predicted data files output from **tdoctree_tiled.exe** contain the locations and predicted data. The order of the data points is in the same order as the :ref:`survey and locations file <surveyFile>`. Each block, separated by a blank line, are the data for a particular transmitter. Thus predicted data files take the format:

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


For each transmitter, a set of field observations are made for a set of receivers. It has 13 columns. The rows of the data array are formatted as follows:

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

such that :math:`E \; data` is in units V/m and is comprised of 3 columns:

.. math::

    | \; E_x \; | \; E_y \; | \; E_z \; |

:math:`H \; data` is in units A/m and is comprised of 3 columns:

.. math::

    | \; H_x \; | \; H_y \; | \; H_z \; |

and :math:`dB/dt \; data` is in units T/s and is comprised of 3 columns:

.. math::

    | \; dB_x/dt \; | \; dB_y/dt \; | \; -dB_z/dt \; |



.. important::

	- The data are represented in a left-handed coordinate system where X is Easting, Y is Northing and Z is +ve downward.
	- The vertical component of dB/dt is represented using :math:`\mathbf{-dB_z/dt}` **!!!** This is done due to a common plotting convention.









