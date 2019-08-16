.. _waveFile:

Wave File
=========

The wave file defines the time-stepping that is used to solve the problem and the time-dependent current in the transmitter. Unlike past generation of tdoctree, the tdoctree_v2 code allows the user to define a different waveform for each source. However, the time-stepping is consistent regardless of the source.

Single Waveform
---------------

In this case, a single waveform will be used to define the time-dependent current in all transmitter. For this the format is:


|
|
| :math:`t_0 \;\;\; 1 \;\;\; I_0`
| :math:`t_1 \;\;\; 1 \;\;\; I_1`
| :math:`t_2 \;\;\; 1 \;\;\; I_2`
| :math:`\;\;\;\;\;\;\, \vdots`
| :math:`t_f \;\;\; 1 \;\;\; I_f`
|
|

where

	- :math:`t_i` is a particular time in seconds
	- :math:`1` indicates there is only one waveform being defined
	- :math:`I_i` is the current in the at time :math:`t_i`

.. important::

	- The latest time channel in the survey file *cannot* be later than the last time defining the transmitter waveform
	- The earliest time channel in the survey file *cannot* be earlier than the first time defining the transmitter waveform
	- The first column in the wave file must have increasing values


Waveform for each transmitter
-----------------------------

In this case, a waveform is defined for each transmitter. Assume there are :math:`n` transmitters. The format for the waveform file is:


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