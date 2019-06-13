.. _waveFile:

Wave File
=========

The wave file defines the time-stepping that is used to solve the problem. There are two ways to define a wave file that will be accepted by *TDoctreeinv*. When creating the wave file, we must keep several things in mind:

	- The latest time channel in the survey file *cannot* be later than the last time defining the transmitter waveform
	- The earliest time channel in the survey file *cannot* be earlier than the first time defining the transmitter waveform

Piecewise Constant
------------------

To make piecewise constant waveforms, the wave file has the form:

|
|
:math:`t_0 \;\;\; I_0`
:math:`t_1 \;\;\; I_1 \;\;\; n_1`
:math:`t_2 \;\;\; I_2 \;\;\; n_2`
:math:`\;\;\;\;\; \vdots`
:math:`t_f \;\;\; I_f \;\;\; n_f`
|
|

where

	- :math:`t_i` is a particular time in seconds
	- :math:`I_i` is the current in the transmitter between times :math:`t_{i-1}` and :math:`t_i`
	- :math:`n_i` is the number of linear time steps between times :math:`t_{i-1}` and :math:`t_i`

As a result, the step length :math:`\Delta t` being used between times :math:`t_{i-1}` and :math:`t_i` is given by:

.. math::
	\Delta t = \frac{t_i- t_{i-1}}{n_i}


Arbitrary Waveform
------------------

**TODO**
