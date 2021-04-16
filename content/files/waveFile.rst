.. _waveFile:

Wave File
=========

The wave file defines the time-dependent current in the transmitter during both the on-time and off-time, as well as the time-stepping that is used to solve the time-dependent Maxwell system. Unlike past generations of tdoctree, the tdoctree_v2 code allows the user to define a different waveform for each source. However, the time-stepping is consistent regardless of the source.

Single Waveform
---------------

In this case, a single waveform will be used to define the time-dependent current in all transmitter. For this the format is:


|
| :math:`t_0 \;\;\; 1 \;\;\;\, I_0`
| :math:`t_1 \;\;\; n_1 \;\;\; I_1`
| :math:`t_2 \;\;\; n_2 \;\;\; I_2`
| :math:`\;\;\;\;\;\;\, \vdots`
| :math:`t_f \;\;\; n_f \;\;\; I_f`
|
|

where

	- :math:`t_i` is a particular time in seconds
	- :math:`n_i` is the number of linear time steps between times :math:`t_{i-1}` and :math:`t_i`. *Note that* :math:`n_0=1` *always*
	- :math:`I_i` is the current in the transmitter at all times within interval :math:`i`; i.e. for :math:`t_{i-1} < t \leq t_i`

.. important::

	- The latest time channel in the survey file *cannot* be later than the last time defining the transmitter waveform
	- The earliest time channel in the survey file *cannot* be earlier than the first time defining the transmitter waveform
	- The first column in the wave file must have increasing values


Step off example
^^^^^^^^^^^^^^^^

For a unit step-off waveform, an example wave file is shown below. This wave file is designed to model data at time channels between t = 0.0001 s and t = 0.01 s. We have a least 20 steps from t = 0 s to t = 1e-4 s in order to accurately model data at the first time channel. And the final time in the wave file is after t = 0.01 s. Notice that because there are 3 distinct time step lengths, the code would store the factorizations of 3 linear systems.


.. figure:: images/wave_stepoff_v2.png
     :align: center
     :width: 500

     Click to `download <https://github.com/ubcgif/tdoctree_v2/raw/tdoctree/assets/wave_examples/stepoff_v2.txt>`__ . 


Square pulse example
^^^^^^^^^^^^^^^^^^^^

Here, we consider the wave file for a square pulse waveform. The on-time begins at t = -20 ms and ends at t = 0 s. This wave file is designed to model data at time channels between t = 0.0001 s and t = 0.01 s. When designing the waveform, several things were considered:

    - The waveform during the on-time was discretized to finer time-steps as we approached the off-time. This was done to more accuately model the early time data
    - We made sure to minimize the number of distinct time-step lengths used to model the data. In this case, we discretized the waveform to use step lengths of 5e-6 s, 5e-5 s and 5e-4 s. Thus, the code only needs to store the factorizations of 3 linear systems.


.. figure:: images/wave_square_v2.png
     :align: center
     :width: 500

     Click to `download <https://github.com/ubcgif/tdoctree/raw/tdoctree_v2/assets/wave_examples/square_v2.txt>`__ .



Arbitrary waveform
^^^^^^^^^^^^^^^^^^

Here, we consider the wave file corresponding to an arbitrary waveform. In this case, it is a half-sine waveform whose on-time is from -20 ms to 0 ms. This wave file is designed to model data at time channels between t = 0.0001 s and t = 0.01 s. At each time during the on-time, we must provide the transmitter current. Once again, notice that:

    - the waveform during the on-time was discretized to finer time-steps as we approached the off-time. This was done to more accuately model the early time data
    - we made sure to minimize the number of distinct time-step lengths used to model the data. In this case, we discretized the waveform to use step lengths of 5e-6 s, 5e-5 s and 5e-4 s. Thus, the code only needs to store the factorizations of 3 linear systems.

Click to `download and see this file <https://github.com/ubcgif/tdoctree_v2/raw/tdoctree/assets/wave_examples/arbitrary_v2.txt>`__ .

..note:: You could define the off-time portion of the wave file in the same way you defined the on-time. Simply provide the actual times and set :math:`n_i=1` .






Waveform for each transmitter
-----------------------------

In this case, a waveform is defined for each transmitter. *This format is a little different than for a single waveform used for all transmitters* . Assume there are :math:`n` transmitters. The format for the waveform file is:


|
|
| :math:`t_0 \;\;\; n \;\;\; I_0^{(1)} \;\;\; I_0^{(2)} \cdots \; I_0^{(n)}`
| :math:`t_1 \;\;\; n \;\;\; I_1^{(1)} \;\;\; I_1^{(2)} \cdots \; I_1^{(n)}`
| :math:`t_2 \;\;\; n \;\;\; I_2^{(1)} \;\;\; I_2^{(2)} \cdots \; I_2^{(n)}`
| :math:`\, \vdots \;\;\;\;\, \vdots \;\;\;\;\; \vdots \;\;\;\;\;\;\;\, \vdots \;\;\;\,\ddots\;\; \vdots`
| :math:`t_f \;\;\; n \;\;\; I_f^{(1)} \;\;\; I_f^{(2)} \cdots \; I_f^{(n)}`
|
|


where

	- :math:`t_i` is a particular time in seconds
	- :math:`n` indicates the total number of transmitters
	- :math:`I_i^{(j)}` is the current in the at time :math:`t_i` for transmitter :math:`j`

.. important::

	- The latest time channel in the survey file *cannot* be later than the last time defining the transmitter waveform
	- The earliest time channel in the survey file *cannot* be earlier than the first time defining the transmitter waveform
	- The first column in the wave file must have increasing values
	- You must define :math:`n` current columns