.. _timeFile:

Time Channels File
==================


The format of the time channels file is as follows:


|
| :math:`k_1 \;\;\;\;\; t_1`
| :math:`k_2 \;\;\;\;\; t_2`
| :math:`k_3 \;\;\;\;\; t_3`
| :math:`k_4 \;\;\;\;\; t_4`
| :math:`k_4 \;\;\;\;\; t_5`
| :math:`k_5 \;\;\;\;\; t_6`
| :math:`\;\;\;\;\;\;\vdots`
| :math:`k_{N-1} \; t_{N-1}`
| :math:`k_N \;\;\;\; t_N`
|
|

where 

    - :math:`N` is the number of unique times within the file
    - :math:`k_i` defines the index value (1,2,....,N) for a particular time channel. Should be ordered 1 to N.
    - :math:`t_i` is the frequency in seconds.

Below we see an example of a time channel file which defines 7 unique times.

.. figure:: images/timefile.png
     :align: center
     :width: 500

.. important:: The times defined within this file **must** lie between the minimum and maximum times defined within the :ref:`wave file<waveFile>`.











