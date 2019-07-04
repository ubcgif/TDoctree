.. _activeTilesFile:

Active Tiles File
=================

The active tiles file contains a vector of 1s and 0s, where 1 is used to denote a tile (local forward mesh) that is used in the inversion and 0 is used to denote a tile that is not. For example:


|
| 1
| 1
| 1
| 1
| 1
| 0
| 0
| 1
| 1
|
|

Note that the 6 :math:`\!^{th}` and 7 :math:`\!^{th}` tiles specified in the :ref:`forward mesh file <binaryFile>` (and their associated data) will not be used in the inversion.








