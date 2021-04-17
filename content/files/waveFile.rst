.. _waveFile:

Wave File
=========

The wave file defines the time-dependent current in the transmitter during both the on-time and off-time, as well as the time-stepping that is used to solve the time-dependent Maxwell system.


.. important::

    - You want to discretize in time appropriately while keeping the number of unique step lengths as small as possible. Every different step length used will result in the code needing store the factorization of another linear system.
    - The latest time defining the transmitter waveform (:math:`t_f`) **must** be after the latest time channel in the survey file 
    - The earliest time defining the transmitter waveform (:math:`t_0`) **must** be before the earliest time channel in the survey file
    - The first column in the wave file **must** have increasing values


Basic Format
------------

The basic format for a wave file is a two column format: 

|
| :math:`t_0 \;\;\; I_0`
| :math:`t_1 \;\;\; I_1`
| :math:`t_2 \;\;\; I_2`
| :math:`\;\;\;\, \vdots`
| :math:`t_f \;\;\; I_f`
|
|

where

    - :math:`t_i` is the time in seconds
    - :math:`I_i` is the current in the transmitter time :math:`t_i`


Please be aware that you are defining the transmitter current during both the on-times and off times. The code will assume a steady-state transmitter current of :math:`I_0` for :math:`t \leq t_0`.

**Step-Off Example:**

Here, we have provided an `example file for a stepoff waveform <https://github.com/ubcgif/tdoctree/raw/tdoctree/assets/wave_examples/stepoff_basic.txt>`__ . This wave file is designed to model data at logarithmically spaced time channels between t = 0.0001 s and t = 0.01 s. We have a least 20 steps from t = 0 s to t = 1e-4 s in order to accurately model data at the first time channel. And the final time in the wave file is after t = 0.01 s. Notice that because there are only 3 distinct time step lengths (5e-6 s, 5e-5 s and 5e-4 s), the code only needs to store the factorizations of 3 linear systems.

**Arbitrary Waveform Example:**

We have also provided an `example file for an arbitrary waveform <https://github.com/ubcgif/tdoctree/raw/tdoctree/assets/wave_examples/arbitrary_basic.txt>`__ . The waveform is a half-sine function whose on-time is from -20 ms to 0 ms. This wave file is designed to model data at logarithmically spaced time channels between t = 0.0001 s and t = 0.01 s. In this case, we must define the waveform at :math:`t \; < \; 0` s. Notice that once againg there are only 3 distinct time step lengths (5e-6 s, 5e-5 s and 5e-4 s) and the code only needs to store the factorizations of 3 linear systems.



Compact Format
--------------

The compact format for the wave file better demonstrates groups of time invervals that use the same step length. This format is as follows:

|
| :math:`t_0 \;\;\; I_0`
| :math:`t_1 \;\;\; I_1 \;\;\; n_1`
| :math:`t_2 \;\;\; I_2 \;\;\; n_2`
| :math:`\;\;\;\;\;\;\, \vdots`
| :math:`t_f \;\;\; I_f \;\;\; n_f`
|
|

where

    - :math:`t_i` is a particular time in seconds
    - :math:`I_i` is the current in the transmitter at all times within interval :math:`i`; i.e. for :math:`t_{i-1} < t \leq t_i`
    - :math:`n_i` is the number of linear time steps between times :math:`t_{i-1}` and :math:`t_i`

The first line of the wave file defines the initial time :math:`t_0` and current :math:`I_0`. The time-step length :math:`\Delta t_i` used within interval :math:`i` is given by:

.. math::
    \Delta t_i = \frac{t_i- t_{i-1}}{n_i}


**Step-Off Example:**

For a unit step-off waveform, we have provided an example wave file is shown below. This waveform is IDENTICAL to the stepoff waveform shown in the *basic format section* . We simply used the compact format.


.. figure:: images/wave_stepoff.png
     :align: center
     :width: 500

     Click to `download <https://github.com/ubcgif/tdoctree/raw/tdoctree/assets/wave_examples/stepoff.txt>`__ . 


**Square Pulse Example:**

Here, we consider the wave file for a square pulse waveform. The on-time begins at t = -20 ms and ends at t = 0 s. This wave file is designed to model data at time channels between t = 0.0001 s and t = 0.01 s. When designing the waveform, several things were considered:

    - The waveform during the on-time was discretized to finer time-steps as we approached the off-time. This was done to more accuately model the early time data
    - We made sure to minimize the number of distinct time-step lengths used to model the data. In this case, we discretized the waveform to use step lengths of 5e-6 s, 5e-5 s and 5e-4 s. Thus, the code only needs to store the factorizations of 3 linear systems.


.. figure:: images/wave_square.png
     :align: center
     :width: 500

     Click to `download <https://github.com/ubcgif/tdoctree/raw/tdoctree/assets/wave_examples/square.txt>`__ .



**Arbitrary Waveform Example:**

Here, we consider the wave file corresponding to the same arbitrary waveform that was shown in the *basic format section*. Click to `see this file <https://github.com/ubcgif/tdoctree/raw/tdoctree/assets/wave_examples/arbitrary.txt>`__ . Notice that we must define the transmitter current at every distinct on-time. However, we can compact the time-stepping during the off-time because the transmitter current doesn't change.


