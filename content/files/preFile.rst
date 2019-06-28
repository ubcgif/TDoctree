.. _preFile:

Predicted Data File
===================

predicted data files output from **tdoctree_v2.exe** contain the transmitter, receiver and time indecies as well as the predicted data. The order of the data points is in the same order as the :ref:`survey index file <indexFile>`. Predicted data files take the format:

|
| :ref:`tx_ind<tdoctree_pre_ln1>` :math:`\;` :ref:`rx_ind<tdoctree_pre_ln2>` :math:`\;` :ref:`t_ind<tdoctree_pre_ln3>` :math:`\;` :ref:`data_opt<tdoctree_pre_ln4>` :math:`\;` :ref:`data<tdoctree_pre_ln5>`
| :ref:`tx_ind<tdoctree_pre_ln1>` :math:`\;` :ref:`rx_ind<tdoctree_pre_ln2>` :math:`\;` :ref:`t_ind<tdoctree_pre_ln3>` :math:`\;` :ref:`data_opt<tdoctree_pre_ln4>` :math:`\;` :ref:`data<tdoctree_pre_ln5>`
| :ref:`tx_ind<tdoctree_pre_ln1>` :math:`\;` :ref:`rx_ind<tdoctree_pre_ln2>` :math:`\;` :ref:`t_ind<tdoctree_pre_ln3>` :math:`\;` :ref:`data_opt<tdoctree_pre_ln4>` :math:`\;` :ref:`data<tdoctree_pre_ln5>`
| :math:`\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; \vdots`
| :ref:`tx_ind<tdoctree_pre_ln1>` :math:`\;` :ref:`rx_ind<tdoctree_pre_ln2>` :math:`\;` :ref:`t_ind<tdoctree_pre_ln3>` :math:`\;` :ref:`data_opt<tdoctree_pre_ln4>` :math:`\;` :ref:`data<tdoctree_pre_ln5>`
|
|

**Parameter Descriptions**


.. _tdoctree_pre_ln1:

    - **tx_ind:** The index corresponding to the desired transmitter within the :ref:`transmitter file<receiverFile>`. 

.. _tdoctree_pre_ln2:

    - **rx_ind:** The index corresponding to the desired receiver within the :ref:`receiver file<receiverFile>`.

.. _tdoctree_pre_ln3:

    - **t_ind:** The index corresponding to the desired time within the :ref:`time channel file<timeFile>`.

.. _tdoctree_pre_ln4:

    - **data_opt:**

        - A flag value of *--------* is entered if the datum is the magnetic field *H* in units A/m
        - A flag value of *--------* is entered if the datum is the time-derivative *dB/dt* in units T/s

.. _tdoctree_pre_ln4:

    - **data:** The datum. Either H or dB/dt

