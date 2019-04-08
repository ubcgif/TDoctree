.. _modelFile:

Model File
==========

For a given model, the model file has the following format:


|
| :math:`m_1`
| :math:`m_2`
| :math:`m_3`
| :math:`m_4`
| :math:`m_5`
| :math:`m_6`
| :math:`\;\vdots`
| :math:`m_{N-1}`
| :math:`m_N`
|
|

where :math:`N` is the total number of cells in the corresponding tensor or Octree mesh.

Conductivity Models
^^^^^^^^^^^^^^^^^^^

For conductivity models, conductivity values are entered in units S/m.


Active Cells Model
^^^^^^^^^^^^^^^^^^

Within active cells models, all cells lying below surface topography (or set as active) are given a flag value of 1. All inactive cells are given a flag value of 0.










