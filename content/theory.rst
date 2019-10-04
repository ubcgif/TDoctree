.. _theory:

Background Theory
=================

This section aims to provide the user with a basic review of the physics, discretization, and optimization
techniques used to solve the time domain electromagnetics problem. It is assumed
that the user has some background in these areas. For further reading see (Nabighian, 1991).

.. _theory_fundamentals:

Fundamental Physics
-------------------

Maxwell's equations provide the starting point from which an understanding of how electromagnetic
fields can be used to uncover the substructure of the Earth. The time domain Maxwell's
equations are:

.. math::
    \nabla \times & \vec{e} = - \partial_t \vec{b} \\
    \nabla \times & \vec{h} - \vec{j} = \vec{s} \, f(t) \\
    \rho \vec{j} &= \vec{e} \\
    \vec{b} &= \mu \vec{h}
    :label: maxwells_eq


where :math:`\vec{e}`, :math:`\vec{h}`, :math:`\vec{j}` and :math:`\vec{b}` are the electric field, magnetic field, current density and magnetic flux density, respectively. :math:`\vec{s}` contains the geometry of the source term and :math:`f(t)` is a time-dependent waveform. Symbols :math:`\mu` and :math:`\rho` represent the magnetic permeability and electrical resistivity, respectively. This formulation assumes a quasi-static mode so that the system can be viewed as a diffusion equation (Weaver, 1994; Ward and Hohmann, 1988 in Nabighian, 1991). By doing so, some difficulties arise when
solving the system;

    - the resistivity :math:`\rho` varies over several orders of magnitude
    - the fields can vary significantly near the sources, but smooth out at distance thus high resolution is required near sources

Octree Mesh
-----------

By using an Octree discretization of the earth domain, the areas near sources and likely model
location can be give a higher resolution while cells grow large at distance. In this manner, the
necessary refinement can be obtained without added computational expense. 
The figure below shows an example of an Octree mesh, with nine cells, eight of which are the base mesh minimum size.


.. figure:: images/OcTree.png
     :align: center
     :width: 600


When working with Octree meshes, the underlying mesh is defined as a regular 3D orthogonal grid where
the number of cells in each dimension are :math:`2^{n_1} \times 2^{n_2} \times 2^{n_3}`. The cell widths for the underlying mesh
are :math:`h_1, \; h_2, \; h_3`, respectively. This underlying mesh
is the finest possible, so that larger cells have side lengths which increase by powers of 2.
The idea is that if the recovered model properties change slowly over a certain volume, the cells
bounded by this volume can be merged into one without losing the accuracy in modeling, and are
only refined when the model begins to change rapidly.



Discretization of Operators
---------------------------

The operators div, grad, and curl are discretized using a finite volume formulation. Although div and grad do not appear in :eq:`maxwells_eq` , they are required for the solution of the system. The divergence operator is discretized in the usual flux-balance approach, which by Gauss' theorem considers the current flux through each face of a cell. The nodal gradient (operates on a function with values on the nodes) is obtained by differencing adjacent nodes and dividing by edge length. The discretization of the curl operator is computed similarly to the divergence operator by utilizing Stokes theorem by summing the magnetic field components around the edge of each face. Please see Haber (2012) for a detailed description of the discretization process.

.. _theory_fwd:

Forward Problem
---------------

To solve the forward problem :eq:`maxwells_eq` , we must first discretize in space and then in time. Using finite volume approach, the magnetic fields on cell edges (:math:`\mathbf{u}`) discretized in space are described by:

.. math::
    \mathbf{C^T \, M_\rho \, C \, u} + \mathbf{M_\mu} \, \partial_t \mathbf{u} = f(t) \, \mathbf{q}
    :label: discrete_h_sys


where :math:`\mathbf{C}` is the curl operator, :math:`f(t)` is a time-dependent waveform, :math:`\mathbf{q}` defines the time-independent portion of the right-hand side (:ref:`explained here <theory_rhs>` ) and

.. math::
    \mathbf{M_\rho} &= diag \big ( \mathbf{A^T_{f2c} V} \, \boldsymbol{\rho} \big ) \\
    \mathbf{M_\mu} &= diag \big ( \mathbf{A^T_{f2c} V} \, \boldsymbol{\mu} \big )
    :label: mass_matrix


:math:`\mathbf{V}` is a diagonal matrix that contains the volume for each cell. Vectors :math:`\boldsymbol{\rho}` and :math:`\boldsymbol{\mu}` are vectors containing the electrical resistivity and magnetic permeability of each cell, respectively. :math:`\mathbf{A_{f2c}}` averages from faces to cell centres and :math:`\mathbf{A_{e2c}}` averages from edges to cell centres.

Discretization in time is performed using backward Euler. Thus for a single transmitter, we must solve the following for every time step :math:`\Delta t_i`:

.. math::
    \mathbf{A_i \, u_{i}} = \mathbf{-B_i \, u_{i-1}} + \mathbf{q_i}
    :label: back_euler


where

.. math::
    \mathbf{A_i} &= \mathbf{C^T \, M_\rho \, C } + \Delta t_i^{-1} \mathbf{M_\mu} \\
    \mathbf{B_i} &= - \Delta t_i^{-1} \mathbf{M_\mu} \\
    \mathbf{q_i} &= f_i \, \mathbf{q}
    :label: a_operator 


If we organize all time-steps into a single system, and by letting :math:`\mathbf{K} = \mathbf{C^T \; M_\rho \, C}`, the forward problem can be expressed as :math:`\mathbf{A \, u} = \mathbf{\tilde q}`:

.. math::
    \begin{bmatrix}
        \mathbf{K} & & & & & \\
        \mathbf{B_1} & \mathbf{A_1} & & & & \\
        & \mathbf{B_2} & \mathbf{A_2} & & & \\
        & & & \ddots & & \\
        & & & & \mathbf{B_n} & \mathbf{A_n}
    \end{bmatrix}
    \begin{bmatrix}
        \mathbf{u_0} \\ \mathbf{u_1} \\ \mathbf{u_2} \\ \vdots \\ \mathbf{u_n}
    \end{bmatrix} =
    \begin{bmatrix}
        \mathbf{q_0} \\ \mathbf{q_1} \\ \mathbf{q_2} \\ \vdots \\ \mathbf{q_n}
    \end{bmatrix}
    :label: sys_forward


.. note:: This problem must be solved for each source. However, LU factorization for each unique time step length is used to make solving for many right-hand sides more efficient.

.. _theory_rhs:

Defining the Vector q
^^^^^^^^^^^^^^^^^^^^^

The TDoctree version 1 package models EM responses with inductive sources (e.g. a closed loop). For these types of sources, analytic solutions exist for the magnetostatic solution. We assume this is the case for :math:`t \leq t_0`. Let :math:`\mathbf{u_0}` define the static magnetic field within the domain discretized to cell edges. From :eq:`discrete_h_sys` , the time-derivative at :math:`t \leq t_0` is zero, thus:

.. math::
    \mathbf{C^T \, M_\rho \, C \, u_0} = f_0 \, \mathbf{q}


For each :math:`\mathbf{q_i}` defined in :eq:`a_operator` we can use vector :math:`\mathbf{q}` obtained here.

.. _theory_data:

Data
----

We have the magnetic field on cell edges at all time steps. Let :math:`Q` be a linear operator that averages the magnetic fields from cell edges to cell centers then interpolates each Cartesian component to the locations of the receivers. Where

.. math::
    t_i = t_0 + \sum_{k=0}^i \Delta t_k


the Cartesian components of the magnetic field at the receivers at all time steps is:

.. math::
    \mathbf{h_i} = Q \, \mathbf{ u_i}
    :label: rec_interp


and the time-derivative of the magnetic flux is:

.. math::
    \frac{\partial \mathbf{b_i}}{\partial t} = - \mu_0 \Bigg [
    \Bigg ( \frac{t_{i+1}-t_i}{t_{i+1} - t_{i-1}} \Bigg ) \Bigg ( \frac{\mathbf{h_i} - \mathbf{h_{i-1}}}{t_i - t_{i-1}} \Bigg )
    + \Bigg ( \frac{t_i - t_{i-1}}{t_{i+1} - t_{i-1}} \Bigg ) \Bigg ( \frac{\mathbf{h_{i+1}} - \mathbf{h_{i}}}{t_{i+1} - t_i} \Bigg ) \Bigg ]
    :label: dbdt_interp


Once the field an its time-derivative have been computed at the receivers for every time channel, linear interpolation is carried out to compute the fields at the correct time channels. Where :math:`\mathbf{Q}` is a block-diagonal matrix of :math:`Q` that takes the magnetic fields from edges to the receivers at all times, :math:`\mathbf{P}` performs the operation in :eq:`dbdt_interp`, :math:`\mathbf{I}` is an identity matrix, :math:`\mathbf{L_1}` performs the linear interpolation to the correct time channels for the magnetic field and :math:`\mathbf{L_2}` performs the linear interpolation to the correct time channels for the time-derivative, the predicted data is given by:

.. math::
    \mathbf{d} = \begin{bmatrix} \mathbf{L_1} & \\ & \mathbf{L_2} \end{bmatrix} \begin{bmatrix} \mathbf{I} \\ \mathbf{P} \end{bmatrix} \mathbf{Q \, u} = \mathbf{Q_t \, u}
    :label: dpre


We let :math:`\mathbf{Q_t}` represent an operator that projects the magnetic fields on cell edges to the data. :math:`\mathbf{u}` is a vector that contains the magnetic fields on cell edges at all time steps (see :eq:`sys_forward` )

.. _theory_sensitivity:

Sensitivities
-------------

To solve the inverse problem, we will need to compute the sensitivity matrix. By differentiating the data with respect to the resistivities: 

.. math::
    \mathbf{J} = \frac{\partial \mathbf{d}}{\partial \boldsymbol{\rho}} = - \mathbf{Q_t \, A^{-1} \, G}


Where :math:`\mathbf{A}` is the full system defined in :eq:`sys_forward` , :math:`\mathbf{Q_t}` is defined in :eq:`dpre` and

.. math::
    \mathbf{G} = \mathbf{C^T} \, diag \big ( \mathbf{C} \, (\mathbf{u - \tilde u_0} ) \big )  \, \mathbf{A_{fc}^T} \, diag \big ( \mathbf{V} \,\boldsymbol{\rho} \big ) 


where

.. math::
    \mathbf{\tilde u_0} = f_0^{-1} \begin{bmatrix} f_0 \mathbf{u_0} \\ f_1 \mathbf{u_0} \\ \vdots \\ f_n \mathbf{u_0} \end{bmatrix}



.. _theory_inv:

Inverse Problem
---------------

We are interested in recovering the conductivity distribution for the Earth. However, the numerical stability of the inverse problem is made more challenging by the fact rock conductivities/resistivities can span many orders of magnitude. To deal with this, we define the model as the log-resistivity for each cell, e.g.:

.. math::
    \mathbf{m} = log (\boldsymbol{\rho}) = \log (\boldsymbol{\sigma}^{-1})


The inverse problem is solved by minimizing the following global objective function with respect to the model:

.. math::
    \phi (\mathbf{m}) = \phi_d (\mathbf{m}) + \beta \phi_m (\mathbf{m})
    :label: global_objective


where :math:`\phi_d` is the data misfit, :math:`\phi_m` is the model objective function and :math:`\beta` is the trade-off parameter. The data misfit ensures the recovered model adequately explains the set of field observations. The model objective function adds geological constraints to the recovered model. The trade-off parameter weights the relative emphasis between fitting the data and imposing geological structures.

.. note:: Although the code defines the electrical properties of the Earth internally in terms of the electrical resistivity, the models imported an exported by the code are electrical conductivity models.


.. _theory_inv_misfit:

Data Misfit
^^^^^^^^^^^

Here, the data misfit is represented as the L2-norm of a weighted residual between the observed data (:math:`d_{obs}`) and the predicted data for a given conductivity model :math:`\boldsymbol{\sigma}`, i.e.:

.. math::
    \phi_d = \frac{1}{2} \big \| \mathbf{W_d} \big ( \mathbf{d_{obs}} - \mathbb{F}[\boldsymbol{\rho}] \big ) \big \|^2
    :label: data_misfit_2


where :math:`W_d` is a diagonal matrix containing the reciprocals of the uncertainties :math:`\boldsymbol{\varepsilon}` for each measured data point, i.e.:

.. math::
    \mathbf{W_d} = \textrm{diag} \big [ \boldsymbol{\varepsilon}^{-1} \big ] 


.. important:: For a better understanding of the data misfit, see the `GIFtools cookbook <http://giftoolscookbook.readthedocs.io/en/latest/content/fundamentals/Uncertainties.html>`__ .

.. _theory_MOF:

Model Objective Function
^^^^^^^^^^^^^^^^^^^^^^^^

Due to the ill-posedness of the problem, there are no stable solutions obtained by freely minimizing the data misfit, and thus regularization is needed. The regularization uses penalties for both smoothness, and likeness to a reference model :math:`m_{ref}` supplied by the user. The model objective function is given by:

.. math::
    \phi_m = \frac{\alpha_s}{2} \!\int_\Omega w_s | m - & m_{ref} |^2 dV
    + \frac{\alpha_x}{2} \!\int_\Omega w_x \Bigg | \frac{\partial}{\partial x} \big (m - m_{ref} \big ) \Bigg |^2 dV \\
    &+ \frac{\alpha_y}{2} \!\int_\Omega w_y \Bigg | \frac{\partial}{\partial y} \big (m - m_{ref} \big ) \Bigg |^2 dV
    + \frac{\alpha_z}{2} \!\int_\Omega w_z \Bigg | \frac{\partial}{\partial z} \big (m - m_{ref} \big ) \Bigg |^2 dV
    :label: MOF1


where :math:`\alpha_s, \alpha_x, \alpha_y` and :math:`\alpha_z` weight the relative emphasis on minimizing differences from the reference model and the smoothness along each gradient direction. And :math:`w_s, w_x, w_y` and :math:`w_z` are additional user defined weighting functions.

An important consideration comes when discretizing the regularization onto the mesh. The gradient operates on
cell centered variables in this instance. Applying a short distance approximation is second order
accurate on a domain with uniform cells, but only :math:`\mathcal{O}(1)` on areas where cells are non-uniform. To
rectify this a higher order approximation is used (Haber, 2012). The second order approximation of the model
objective function can be expressed as:

.. math::
    \phi_m (\mathbf{m}) = \mathbf{\big (m-m_{ref} \big )^T W^T W \big (m-m_{ref} \big )}


where the regularizer is given by:

.. math::
    \mathbf{W^T W} =& \;\;\;\;\alpha_s \textrm{diag} (\mathbf{w_s \odot v}) \\
    & + \alpha_x \mathbf{G_x^T} \textrm{diag} (\mathbf{w_x \odot v_x}) \mathbf{G_x} \\
    & + \alpha_y \mathbf{G_y^T} \textrm{diag} (\mathbf{w_y \odot v_y}) \mathbf{G_y} \\
    & + \alpha_z \mathbf{G_z^T} \textrm{diag} (\mathbf{w_z \odot v_z}) \mathbf{G_z}
    :label: MOF


The Hadamard product is given by :math:`\odot`, :math:`\mathbf{v_x}` is the volume of each cell averaged to x-faces, :math:`\mathbf{w_x}` is the weighting function :math:`w_x` evaluated on x-faces and :math:`\mathbf{G_x}` computes the x-component of the gradient from cell centers to cell faces. Similarly for y and z.

If we require that the recovered model values lie between :math:`\mathbf{m_L  \preceq m \preceq m_H}` , the resulting bounded optimization problem we must solve is:

.. math::
    &\min_m \;\; \phi_d (\mathbf{m}) + \beta \phi_m(\mathbf{m}) \\
    &\; \textrm{s.t.} \;\; \mathbf{m_L \preceq m \preceq m_H}
    :label: inverse_problem


A simple Gauss-Newton optimization method is used where the system of equations is solved using ipcg (incomplete preconditioned conjugate gradients) to solve for each G-N step. For more
information refer again to (Haber, 2012) and references therein.


Inversion Parameters and Tolerances
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. _theory_cooling:

Cooling Schedule
~~~~~~~~~~~~~~~~

Our goal is to solve Eq. :eq:`inverse_problem` , i.e.:

.. math::
    &\min_m \;\; \phi_d (\mathbf{m}) + \beta \phi_m(\mathbf{m - m_{ref}}) \\
    &\; \textrm{s.t.} \;\; \mathbf{m_L \leq m \leq m_H}


but how do we choose an acceptable trade-off parameter :math:`\beta`? For this, we use a cooling schedule. This is described in the `GIFtools cookbook <http://giftoolscookbook.readthedocs.io/en/latest/content/fundamentals/Beta.html>`__ . The cooling schedule can be defined using the following parameters:

    - **beta_max:** The initial value for :math:`\beta`
    - **beta_factor:** The factor at which :math:`\beta` is decrease to a subsequent solution of Eq. :eq:`inverse_problem`
    - **beta_min:** The minimum :math:`\beta` for which Eq. :eq:`inverse_problem` is solved before the inversion will quit
    - **Chi Factor:** The inversion program stops when the data misfit :math:`\phi_d \leq N \times Chi \; Factor`, where :math:`N` is the number of data observations


.. _theory_GN:

Gauss-Newton Update
~~~~~~~~~~~~~~~~~~~

For a given trade-off parameter (:math:`\beta`), the model :math:`\mathbf{m}` is updated using the Gauss-Newton approach. Because the problem is non-linear, several model updates may need to be completed for each :math:`\beta`. Where :math:`k` denotes the Gauss-Newton iteration, we solve:

.. math::
    \mathbf{H}_k \, \mathbf{\delta m}_k = - \nabla \phi_k
    :label: GN_gen


using the current model :math:`\mathbf{m}_k` and update the model according to:

.. math::
    \mathbf{m}_{k+1} = \mathbf{m}_{k} + \alpha \mathbf{\delta m}_k
    :label: GN_update


where :math:`\mathbf{\delta m}_k` is the step direction, :math:`\nabla \phi_k` is the gradient of the global objective function, :math:`\mathbf{H}_k` is an approximation of the Hessian and :math:`\alpha` is a scaling constant. This process is repeated until a max number of GN iterations have been performed, i.e.

.. math::
    k = iter \_ per \_ beta


.. _theory_IPCG:

Gauss-Newton Solve
~~~~~~~~~~~~~~~~~~

Here we discuss the details of solving Eq. :eq:`GN_gen` for a particular Gauss-Newton iteration :math:`k`. Using the data misfit from Eq. :eq:`data_misfit_2` and the model objective function from Eq. :eq:`MOF` , we must solve:

.. math::
    \Big [ \mathbf{J^T W_d^T W_d J + \beta \mathbf{W^T W}} \Big ] \mathbf{\delta m}_k =
    - \Big [ \mathbf{J^T W_d^T W_d } \big ( \mathbf{d_{obs}} - \mathbb{F}[\mathbf{m}_k] \big ) + \beta \mathbf{W^T W m}_k \Big ]
    :label: GN_expanded


where :math:`\mathbf{J}` is the sensitivity of the data to the current model :math:`\mathbf{m}_k`. The system is solved for :math:`\mathbf{\delta m}_k` using the incomplete-preconditioned-conjugate gradient (IPCG) method. This method is iterative and exits with an approximation for :math:`\mathbf{\delta m}_k`. Let :math:`i` denote an IPCG iteration and let :math:`\mathbf{\delta m}_k^{(i)}` be the solution to :eq:`GN_expanded` at the :math:`i^{th}` IPCG iteration, then the algorithm quits when:
|

1. the system is solved to within some tolerance and additional iterations do not result in significant increases in solution accuracy, i.e.:

.. math::
    \| \mathbf{\delta m}_k^{(i-1)} - \mathbf{\delta m}_k^{(i)} \|^2 / \| \mathbf{\delta m}_k^{(i-1)} \|^2 < tol \_ ipcg


|
2. a maximum allowable number of IPCG iterations has been completed, i.e.:

.. math::
    i = max \_ iter \_ ipcg



