.. _indexFile:

Survey Index File
=================

This file is input when forward modeling data. Using 4 columns, the observation file indexes the transmitters, receivers and time channels used for each measurement. Each row defines a unique field measurement. The general format is as follows:


| :ref:`tx_ind<tdoctree_survey_ln1>` :math:`\;` :ref:`rx_ind<tdoctree_survey_ln2>` :math:`\;` :ref:`t_ind<tdoctree_survey_ln3>` :math:`\;` :ref:`data_opt<tdoctree_survey_ln4>`
| :ref:`tx_ind<tdoctree_survey_ln1>` :math:`\;` :ref:`rx_ind<tdoctree_survey_ln2>` :math:`\;` :ref:`t_ind<tdoctree_survey_ln3>` :math:`\;` :ref:`data_opt<tdoctree_survey_ln4>`
| :ref:`tx_ind<tdoctree_survey_ln1>` :math:`\;` :ref:`rx_ind<tdoctree_survey_ln2>` :math:`\;` :ref:`t_ind<tdoctree_survey_ln3>` :math:`\;` :ref:`data_opt<tdoctree_survey_ln4>`
| :math:`\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; \vdots`
| :ref:`tx_ind<tdoctree_survey_ln1>` :math:`\;` :ref:`rx_ind<tdoctree_survey_ln2>` :math:`\;` :ref:`t_ind<tdoctree_survey_ln3>` :math:`\;` :ref:`data_opt<tdoctree_survey_ln4>`
|
|


.. important:: 
    Due to the way the forward problem is solved, it is imperative that the user sort the observations:
        - First by transmitter
        - Then by receiver
        - Then by time channel


**Parameter Descriptions**


.. _tdoctree_survey_ln1:

    - **tx_ind:** The index corresponding to the desired transmitter within the :ref:`transmitter file<receiverFile>`. 

.. _tdoctree_survey_ln2:

    - **rx_ind:** The index corresponding to the desired receiver within the :ref:`receiver file<receiverFile>`.

.. _tdoctree_survey_ln3:

    - **t_ind:** The index corresponding to the desired time within the :ref:`time channel file<timeFile>`.

.. _tdoctree_survey_ln4:

    - **data_opt:**

        - A flag value of *2* is entered if the datum is the magnetic field *H* in units A/m
        - A flag value of *1* is entered if the datum is the time-derivative *dB/dt* in units T/s




