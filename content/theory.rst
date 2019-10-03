.. _theory:

Background Theory
=================

This section aims to provide the user with a basic review of the physics, discretization, and optimization
techniques used to solve the time domain electromagnetics problem. It is assumed
that the user has some background in these areas. For further reading see (:cite:`Nabighian1991`).

.. _theory_fundamentals:

Fundamental Physics
-------------------

Maxwell's equations provide the starting point from which an understanding of how electromagnetic
fields can be used to uncover the substructure of the Earth. The time domain Maxwell's
equations are:

.. math::
    \begin{align}
        \nabla \times & \vec{e} = - \partial_t \vec{b} \\
        \nabla \times & \vec{h} - \vec{j} = \vec{s} \, f(t) \\
        \rho \vec{j} &= \vec{e} \\
        \vec{b} = \mu \vec{h}
    \end{align}
    :label: maxwells_eq

where :math:`\vec{e}`, :math:`\vec{h}`, :math:`\vec{j}` and :math:`\vec{b}` are the electric field, magnetic field, current density and magnetic flux density, respectively. :math:`\vec{s}` contains the geometry of the source term and :math:`f(t)` is a time-dependent waveform. Symbols :math:`\mu` and :math:`\rho` represent the magnetic permeability and electrical resistivity, respectively. This formulation assumes a quasi-static mode so that the system can be viewed as a diffusion equation (Weaver, 1994; Ward and Hohmann, 1988 in :cite:`Nabighian1991`). By doing so, some difficulties arise when
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
     :width: 700


When working with Octree meshes, the underlying mesh is defined as a regular 3D orthogonal grid where
the number of cells in each dimension are :math:`2^{n_1} \times 2^{n_2} \times 2^{n_3}`. The cell widths for the underlying mesh
are :math:`h_1, \; h_2, \; h_3`, respectively. This underlying mesh
is the finest possible, so that larger cells have side lengths which increase by powers of 2.
The idea is that if the recovered model properties change slowly over a certain volume, the cells
bounded by this volume can be merged into one without losing the accuracy in modeling, and are
only refined when the model begins to change rapidly.



Discretization of Operators
---------------------------

The operators div, grad, and curl are discretized using a finite volume formulation. Although div and grad do not appear in :eq:`maxwells_eq`, they are required for the solution of the system. The divergence operator is discretized in the usual flux-balance approach, which by Gauss' theorem considers the current flux through each face of a cell. The nodal gradient (operates on a function with values on the nodes) is obtained by differencing adjacent nodes and dividing by edge length. The discretization of the curl operator is computed similarly to the divergence operator by utilizing Stokes theorem by summing the magnetic field components around the edge of each face. Please see :cite:`Haber2012` for a detailed description of the discretization process.

.. _theory_fwd:

Forward Problem
---------------

.. _theory_initial_cond:

Initial Conditions
^^^^^^^^^^^^^^^^^^

To solve for the first time step using direct or iterative methods, we will need to first solve for the electric fields at :math:`t=t_0`. Assuming the source is static for :math:`t \leq t_0`, :eq:`maxwells_eq` reduces to:

.. math::
    \begin{align}
        \nabla \cdot \vec{j} &= - f_0 \, \nabla \cdot \vec{s} \\
        \vec{j} &= \sigma \vec{e} \\
        \vec{e} &= \nabla \phi
    \end{align}
    :label: maxwells_dc

where :math:`\vec{j}` is the current density, :math:`\phi` is the scalar potential and :math:`f_0` is the waveform at :math:`t \leq t_0`. By applying the finite volume method, the static electric fields are obtained by solving the following system:

.. math::
    \big [ \mathbf{G^T \, N_e^T \, M_\sigma \, N_e \, G} \big ] \, \phi_0 = -f_0 \, \mathbf{G^T \, s_j}
    :label: system_dc

where :math:`\phi_0` lives on nodes, :math:`\mathbf{s_j}` defines the geometry of the source discretized to the mesh and

.. math::
    \begin{align}
    \mathbf{M_\sigma} &= diag \big ( \mathbf{A^T_{e2c} V} \, \boldsymbol{\sigma} \big ) \\
    \mathbf{G} &= \mathbf{P_n \, \tilde G \, N_n}
    \end{align}
    :label: grad_operator

:math:`\mathbf{V}` is a diagonal matrix containing  all cell volumes, and :math:`\mathbf{A_{e2c}}` averages from edges to cell centres. The conductivity for each cell is contained within the vector :math:`\boldsymbol{\sigma}`. The matrix :math:`\mathbf{N_e}` provides edge constraints which address inaccuracies associated with 'hanging edges' in the OcTree mesh. The matrix :math:`\mathbf{N_n}` provides nodal constraints which address inaccuracies associated with 'hanging nodes' in the OcTree mesh. :math:`\mathbf{P_n}` is a projection matrix. :math:`\mathbf{\tilde G}` is the gradient operator, thus :math:`\mathbf{G}` is a modified gradient operator.

Once obtained, the electric field on cell edges at :math:`t=t_0` is obtained via:

.. math::
    \mathbf{e_0} = \mathbf{G \, \phi_0}
    :label: e_0


.. note:: This problem must be solved for each source. However, LU factorization is used to make solving for many right-hand sides more efficient.


.. _theory_direct:

Direct Solver Approach
^^^^^^^^^^^^^^^^^^^^^^

To solve the forward problem :eq:`maxwells_eq`, we must first discretize in space and then in time. Using finite volume approach, the electric fields on cell edges (:math:`\mathbf{e}`) discretized in space are described by:

.. math::
    \mathbf{C^T \, M_\mu \, C \, e} + \mathbf{N_e^T \, M_\sigma \, N_e} \, \partial_t \mathbf{e} = - \mathbf{s} \, \partial_t f
    :label: discrete_e_sys

where :math:`\mathbf{M_\sigma}` and :math:`\mathbf{N_e}` are defined in :eq:`system_dc` and

.. math::
    \begin{align}
    \mathbf{M_\mu} &= diag \big ( \mathbf{A^T_{f2c} V} \, \boldsymbol{\mu^{-1}} \big ) \\
    \mathbf{C} &= \mathbf{\tilde C \, N_e}
    \end{align}
    :label: curl_operator

:math:`\mathbf{A_{f2c}}` averages from faces to cell centres. The inverse magnetic permeability for each cell is contained within the vector :math:`\boldsymbol{\mu}`. :math:`\mathbf{\tilde C}` is the curl operator and :math:`\mathbf{C}` is a modified curl operator.

Discretization in time is performed using backward Euler. Thus for a single transmitter, we must solve the following for every time step :math:`\Delta t_i`:

.. math::
    \mathbf{A_i \, e_{k+1}} = \mathbf{-B_i \, e_k} + \mathbf{q_i}
    :label: back_euler

where

.. math::
    \begin{align}
    \mathbf{A_i} &= \mathbf{C^T \, M_\mu \, C } + \Delta t_i^{-1} \mathbf{N_e^T \, M_\sigma \, N_e} \\
    \mathbf{B_i} &= - \Delta t_i^{-1} \mathbf{N_e^T \, M_\sigma \, N_e} \\
    \mathbf{q_i} &= - \Delta t_i^{-1} \mathbf{N_e^T \, s} \big [ f_{k+1} - f_k \big ]
    \end{align}
    :label: a_operator 

Now let :math:`\mathbf{A_{dc}}` and :math:`\mathbf{q_{dc}}` define the matrix and right-hand side in :eq:`system_dc`. The forward problem can be expressed as:

.. math::
	\begin{bmatrix}
	\mathbf{A_{dc}} & & & & & \\
	\mathbf{B_1 \, G} & \mathbf{A_1} & & & & \\
	& \mathbf{B_2} & \mathbf{A_2} & & & \\
	& & & \ddots & & \\
	& & & & \mathbf{B_n} & \mathbf{A_n}
	\end{bmatrix}
	\begin{bmatrix}
	\phi_0 \\ \mathbf{e_1} \\ \mathbf{e_2} \\ \vdots \\ \mathbf{e_n}
	\end{bmatrix} =
	\begin{bmatrix}
	\mathbf{q_{dc}} \\ \mathbf{q_1} \\ \mathbf{q_2} \\ \vdots \\ \mathbf{q_n}
	\end{bmatrix}
	:label: sys_forward


.. note:: This problem must be solved for each source. However, LU factorization for each unique time step length is used to make solving for many right-hand sides more efficient.


.. _theory_iterative:

Iterative Solver Approach
^^^^^^^^^^^^^^^^^^^^^^^^^

For this approach we decompose the electric field according to the Helmholtz decomposition:

.. math::
    \vec{e} = \vec{a} + \nabla \phi
    :label: e_decomposition

After formulating Maxwell's equations in terms of :math:`\vec{a}` and :math:`\phi`, discretizing in space according to the finite volume method and discretizing in time according to backward Euler, we must solve the following numerical system at each time step :math:`\Delta t_i`:

.. math::
    \begin{bmatrix} \mathbf{A_i} + \mathbf{D} & - \mathbf{B_i \, G} \\ - \mathbf{G^T \, B_i} & \Delta t_i \, \mathbf{G^T \, B_i \, G} \end{bmatrix}
    \begin{bmatrix} \mathbf{a_i} \\ \phi_i \end{bmatrix} = 
    \begin{bmatrix} \mathbf{b_i}  \\ \mathbf{G^T \, b_i} \end{bmatrix}
    :label: maxwell_a_phi

where :math:`\mathbf{a_i}` is the vector potential on edges, :math:`\phi_i` is the scalar potential on nodes, :math:`\mathbf{G}` is the modified gradient operator given by :eq:`grad_operator` and

.. math::
    \begin{align}
    \mathbf{D} &= \mathbf{G}  \, diag \big ( \mathbf{A^T_{n2c} V} \, \boldsymbol{\mu^{-1}} \big ) \mathbf{G^T} \\
    \mathbf{b_i} &= \mathbf{q_i - B_i \, e_k}
    \end{align}

The matrix :math:`\mathbf{N_n}` provides nodal constraints which address inaccuracies associated with 'hanging nodes' in the OcTree mesh. :math:`\mathbf{P_n}` is a projection matrix. And :math:`\mathbf{\tilde G}` is the gradient operator. :math:`\mathbf{D}` is a matrix that is added to the (1,1) block of Eq. :eq:`maxwell_a_phi` to improve the stability of the system. :math:`\mathbf{A_i}`, :math:`\mathbf{B_i}` and :math:`\mathbf{q_i}` are defined in :eq:`a_operator`.

Once :eq:`maxwell_a_phi` is solved, the electric fields on cell edges can be computed numerically according to:

.. math::
	\mathbf{e_i} = \mathbf{a_i} + \mathbf{G \, \phi_i}

To solve :eq:`maxwell_a_phi` we use a block preconditionned conjugate gradient algorithm. For the preconditionner, we do 2 SSOR iterations of :eq:`maxwell_a_phi`. Adjustable parameters for solving Eq. :eq:`maxwell_a_phi` iteratively using BiCGstab are defined as follows:

     - **tol_bicg:** relative tolerance (stopping criteria) when solver is used during forward modeling; i.e. solves Eq. :eq:`discrete_e_sys`. Ideally, this number is very small (~1e-10).
     - **tol_ipcg_bicg:** relative tolerance (stopping criteria) when solver needed in computation of :math:`\delta m` during Gauss Newton iteration; i.e. must solve Eq. :eq:`sensitivity_fields` to solve Eq. :eq:`GN_gen`. This value does not need to be as large as the previous parameter (~1e-5).
     - **max_it_bicg:** maximum number of BICG iterations (~100)


.. note:: This problem must be solved for each source.

.. _theory_initial_h:

Magnetic Field at t0
^^^^^^^^^^^^^^^^^^^^

When computing magnetic field data (not needed for :math:`\vec{e}` or :math:`\partial_t \vec{b}`), we will need to compute magnetic fields at :math:`t=t_0`. Assuming the source is static for :math:`t \leq t_0`, :eq:`maxwells_eq` can be reformulated in terms of a vector potential :math:`\vec{a}` and a scalar potential :math:`\phi`:

.. math::
	\begin{align}
	\nabla \times \mu_{-1} \times \vec{a} + \nabla \mu^{-1} \nabla \cdot \vec{a} &= \sigma \nabla \phi + \vec{s}\, f_0 \\
	\vec{b} &= \nabla \times \vec{a} \\
	\vec{e} &= \nabla \phi
	\end{align}

where the second term is added for stability assuming the Coulomb Gauge (:math:`\nabla \cdot \vec{a} = 0`) condition holds. Using the finite volume approach, we can solve for the discrete vector potential :math:`\mathbf{a_0}`:

.. math::
	\mathbf{A_{m} \, a_0} = \mathbf{q_m}


where :math:`\mathbf{a_0}` lives on edges and

.. math::
    \begin{align}
        \mathbf{A_{m}} &= \mathbf{C^T \, M_\mu \, C + G \, N_n^T }\, diag \big ( \mathbf{A^T_{n2c} V} \, \boldsymbol{\mu^{-1}} \big ) \mathbf{N_n \, G^T} \\
        \mathbf{q_{m}} &= \mathbf{s} \, f_0 + \mathbf{M_\sigma G \, e_0}
    \end{align}

Once we solve for :math:`\mathbf{a_0}`, the magnetic field is computed via:

.. math::
	\mathbf{b_0} = \mathbf{C \, a_0}

where :math:`\mathbf{C}` is define in :eq:`curl_operator`.


.. note:: This problem must be solved for each source. However, LU factorization is used to make solving for many right-hand sides more efficient.


.. _theory_data:

Data
----

Electric Field
^^^^^^^^^^^^^^

The electric field on cell edges at each time step  (:math:`\mathbf{e_i}`) is computed using direct or iterative methods. A linear operator :math:`\mathbf{Q_e}` is constructed to integrate the electric field over the length of the receiver wire and divide by its length. Thus the electric field data for time step :math:`i` is given by:

.. math::
	\mathbf{d_i} = \mathbf{Q_e \, N_e \, e_i}

Linear interpolation is then used to compute the data for the correct time channel.


Time-Derivative of Magnetic Flux
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The electric field on cell edges at each time step  (:math:`\mathbf{e_i}`) is computed using direct or iterative methods. A linear operator :math:`\mathbf{Q_b}` is constructed to integrate the electric field over path of the receiver loop and multiply by -1. By Faraday's law, will compute the time-derivative of the magnetic flux density. Thus dB/dt data for time step :math:`i` is given by:

.. math::
	\mathbf{d_i} = \mathbf{Q_b \, N_e \, e_i}

Linear interpolation is then used to compute the data for the correct time channel.

H-Field
^^^^^^^

The electric field on cell edges at each time step  (:math:`\mathbf{e_i}`) is computed using direct or iterative methods. The magnetic field (:math:`\mathbf{b_0}`) at :math:`t=t_0` is computed by :ref:`solving an a-phi system <theory_initial_h>`. In this case, the H-field data are computed according to:

.. math::
	\mathbf{d_i} = \mathbf{d_{i-1}} - \mu_0^{-1} \Delta t_i \, \mathbf{Q_b \, e_i} 


Where :math:`\mathbf{Q_h}` is a linear operator that projects :math:`\mathbf{b_0}` from cell faces to the locations of the receivers:

.. math::
	\mathbf{d_0} = \mu_0^{-1} \, \mathbf{Q_h \, b_0}

Linear interpolation is then used to compute the data for the correct time channel.

.. _theory_sensitivity:

Sensitivity
-----------

Electric and magnetic field observations are split into their real and imaginary components. Thus the data at a particular frequency for a particular reading is organized in a vector of the form:

.. math::
    \mathbf{d} = [E^\prime_{x}, E^{\prime \prime}_{x}, E^\prime_{y}, E^{\prime \prime}_{y}, E^\prime_{z}, E^{\prime \prime}_{z}, 
    H^\prime_{x}, H^{\prime \prime}_{x}, H^\prime_{y}, H^{\prime \prime}_{y}, H^\prime_{z}, H^{\prime \prime}_{z}]^T
    :label: data_vector


where :math:`\prime` denotes real components and :math:`\prime\prime` denotes imaginary components. To determine the sensitivity of the data (i.e. :eq:`data_vector`) with respect to the model (:math:`\boldsymbol{\sigma}`), we must compute:

.. math::
    \frac{\partial \mathbf{d}}{\partial \boldsymbol{\sigma}} = \Bigg [ 
    \dfrac{\partial E_{x}^\prime}{\partial \boldsymbol{\sigma}} ,
    \dfrac{\partial E_{x}^{\prime\prime}}{\partial \boldsymbol{\sigma}} ,
    \dfrac{\partial E_{y}^\prime}{\partial \boldsymbol{\sigma}} ,
    \dfrac{\partial E_{y}^{\prime\prime}}{\partial \boldsymbol{\sigma}} ,
    \dfrac{\partial E_{z}^\prime}{\partial \boldsymbol{\sigma}} ,
    \dfrac{\partial E_{z}^{\prime\prime}}{\partial \boldsymbol{\sigma}} ,
    \dfrac{\partial H_{x}^\prime}{\partial \boldsymbol{\sigma}} ,
    \dfrac{\partial H_{x}^{\prime\prime}}{\partial \boldsymbol{\sigma}} ,
    \dfrac{\partial H_{y}^\prime}{\partial \boldsymbol{\sigma}} ,
    \dfrac{\partial H_{y}^{\prime\prime}}{\partial \boldsymbol{\sigma}} ,
    \dfrac{\partial H_{z}^\prime}{\partial \boldsymbol{\sigma}} ,
    \dfrac{\partial H_{z}^{\prime\prime}}{\partial \boldsymbol{\sigma}} , \Bigg ]^T


where the conductivity model :math:`\boldsymbol{\sigma}` is real-valued. To differentiate :math:`E^\prime_x` (or any other field component) with respect to the model, we require the derivative of the electric fields on cell edges (:math:`\mathbf{u_e}`) with respect to the model. This is given by:

.. math::
    \frac{\partial \mathbf{u_e}}{\partial \boldsymbol{\sigma}} = - i\omega \mathbf{A}^{-1} diag(\mathbf{u_e}) \, \mathbf{A_{e2c}^T V }
    :label: sensitivity_fields


.. note:: Eq. :eq:`sensitivity_fields` defines the sensitivities when using the direct solver formulation. Computations involving the sensitivities will differ if the :ref:`iterative solver approach<theory_solver>` is used.


.. _theory_inv:

Inverse Problem
---------------

We are interested in recovering the conductivity distribution for the Earth. However, the numerical stability of the inverse problem is made more challenging by the fact rock conductivities can span many orders of magnitude. To deal with this, we define the model as the log-conductivity for each cell, e.g.:

.. math::
    \mathbf{m} = log (\boldsymbol{\rho})


The inverse problem is solved by minimizing the following global objective function with respect to the model:

.. math::
    \phi (\mathbf{m}) = \phi_d (\mathbf{m}) + \beta \phi_m (\mathbf{m})
    :label: global_objective

where :math:`\phi_d` is the data misfit, :math:`\phi_m` is the model objective function and :math:`\beta` is the trade-off parameter. The data misfit ensures the recovered model adequately explains the set of field observations. The model objective function adds geological constraints to the recovered model. The trade-off parameter weights the relative emphasis between fitting the data and imposing geological structures.


.. _theory_inv_misfit:

Data Misfit
^^^^^^^^^^^

Here, the data misfit is represented as the L2-norm of a weighted residual between the observed data (:math:`d_{obs}`) and the predicted data for a given conductivity model :math:`\boldsymbol{\sigma}`, i.e.:

.. math::
    \phi_d = \frac{1}{2} \big \| \mathbf{W_d} \big ( \mathbf{d_{obs}} - \mathbb{F}[\boldsymbol{\sigma}] \big ) \big \|^2
    :label: data_misfit_2


where :math:`W_d` is a diagonal matrix containing the reciprocals of the uncertainties :math:`\boldsymbol{\varepsilon}` for each measured data point, i.e.:

.. math::
    \mathbf{W_d} = \textrm{diag} \big [ \boldsymbol{\varepsilon}^{-1} \big ] 


.. important:: For a better understanding of the data misfit, see the `GIFtools cookbook <http://giftoolscookbook.readthedocs.io/en/latest/content/fundamentals/Uncertainties.html>`__ .


Model Objective Function
^^^^^^^^^^^^^^^^^^^^^^^^

Due to the ill-posedness of the problem, there are no stable solutions obtained by freely minimizing the data misfit, and thus regularization is needed. The regularization uses penalties for both smoothness, and likeness to a reference model :math:`m_{ref}` supplied by the user. The model objective function is given by:

.. math::
    \begin{align}
    \phi_m = \frac{\alpha_s}{2} \!\int_\Omega w_s | m - & m_{ref} |^2 dV
    + \frac{\alpha_x}{2} \!\int_\Omega w_x \Bigg | \frac{\partial}{\partial x} \big (m - m_{ref} \big ) \Bigg |^2 dV \\
    &+ \frac{\alpha_y}{2} \!\int_\Omega w_y \Bigg | \frac{\partial}{\partial y} \big (m - m_{ref} \big ) \Bigg |^2 dV
    + \frac{\alpha_z}{2} \!\int_\Omega w_z \Bigg | \frac{\partial}{\partial z} \big (m - m_{ref} \big ) \Bigg |^2 dV
    \end{align}
    :label:

where :math:`\alpha_s, \alpha_x, \alpha_y` and :math:`\alpha_z` weight the relative emphasis on minimizing differences from the reference model and the smoothness along each gradient direction. And :math:`w_s, w_x, w_y` and :math:`w_z` are additional user defined weighting functions.

An important consideration comes when discretizing the regularization onto the mesh. The gradient operates on
cell centered variables in this instance. Applying a short distance approximation is second order
accurate on a domain with uniform cells, but only :math:`\mathcal{O}(1)` on areas where cells are non-uniform. To
rectify this a higher order approximation is used (:cite:`Haber2012`). The second order approximation of the model
objective function can be expressed as:

.. math::
    \phi_m (\mathbf{m}) = \mathbf{\big (m-m_{ref} \big )^T W^T W \big (m-m_{ref} \big )}

where the regularizer is given by:

.. math::
    \begin{align}
    \mathbf{W^T W} =& \;\;\;\;\alpha_s \textrm{diag} (\mathbf{w_s \odot v}) \\
    & + \alpha_x \mathbf{G_x^T} \textrm{diag} (\mathbf{w_x \odot v_x}) \mathbf{G_x} \\
    & + \alpha_y \mathbf{G_y^T} \textrm{diag} (\mathbf{w_y \odot v_y}) \mathbf{G_y} \\
    & + \alpha_z \mathbf{G_z^T} \textrm{diag} (\mathbf{w_z \odot v_z}) \mathbf{G_z}
    \end{align}
    :label: MOF

The Hadamard product is given by :math:`\odot`, :math:`\mathbf{v_x}` is the volume of each cell averaged to x-faces, :math:`\mathbf{w_x}` is the weighting function :math:`w_x` evaluated on x-faces and :math:`\mathbf{G_x}` computes the x-component of the gradient from cell centers to cell faces. Similarly for y and z.

If we require that the recovered model values lie between :math:`\mathbf{m_L  \preceq m \preceq m_H}` , the resulting bounded optimization problem we must solve is:

.. math::
    \begin{align}
    &\min_m \;\; \phi_d (\mathbf{m}) + \beta \phi_m(\mathbf{m}) \\
    &\; \textrm{s.t.} \;\; \mathbf{m_L \preceq m \preceq m_H}
    \end{align}
    :label: inverse_problem

A simple Gauss-Newton optimization method is used where the system of equations is solved using ipcg (incomplete preconditioned conjugate gradients) to solve for each G-N step. For more
information refer again to :cite:`Haber2012` and references therein.


Inversion Parameters and Tolerances
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. _theory_cooling:

Cooling Schedule
~~~~~~~~~~~~~~~~

Our goal is to solve Eq. :eq:`inverse_problem`, i.e.:

.. math::
    \begin{align}
    &\min_m \;\; \phi_d (\mathbf{m}) + \beta \phi_m(\mathbf{m - m_{ref}}) \\
    &\; \textrm{s.t.} \;\; \mathbf{m_L \leq m \leq m_H}
    \end{align}

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


where :math:`\mathbf{\delta m}_k` is the step direction, :math:`\nabla \phi_k` is the gradient of the global objective function, :math:`\mathbf{H}_k` is an approximation of the Hessian and :math:`\alpha` is a scaling constant. This process is repeated until any of the following occurs:

    1. The gradient is sufficiently small, i.e.:

        .. math::
            \| \nabla \phi_k \|^2 < \textrm{tol_nl}

    2. The smallest component of the model perturbation its small in absolute value, i.e.:

        .. math::
            \textrm{max} ( |\mathbf{\delta m}_k | ) < mindm

    3. A max number of GN iterations have been performed, i.e.

        .. math::
            k = \textrm{iter_per_beta} 


.. _theory_IPCG:

Gauss-Newton Solve
~~~~~~~~~~~~~~~~~~

Here we discuss the details of solving Eq. :eq:`GN_gen` for a particular Gauss-Newton iteration :math:`k`. Using the data misfit from Eq. :eq:`data_misfit_2` and the model objective function from Eq. :eq:`MOF`, we must solve:

.. math::
    \Big [ \mathbf{J^T W_d^T W_d J + \beta \mathbf{W^T W}} \Big ] \mathbf{\delta m}_k =
    - \Big [ \mathbf{J^T W_d^T W_d } \big ( \mathbf{d_{obs}} - \mathbb{F}[\mathbf{m}_k] \big ) + \beta \mathbf{W^T W m}_k \Big ]
    :label: GN_expanded


where :math:`\mathbf{J}` is the sensitivity of the data to the current model :math:`\mathbf{m}_k`. The system is solved for :math:`\mathbf{\delta m}_k` using the incomplete-preconditioned-conjugate gradient (IPCG) method. This method is iterative and exits with an approximation for :math:`\mathbf{\delta m}_k`. Let :math:`i` denote an IPCG iteration and let :math:`\mathbf{\delta m}_k^{(i)}` be the solution to :eq:`GN_expanded` at the :math:`i^{th}` IPCG iteration, then the algorithm quits when:

    1. the system is solved to within some tolerance and additional iterations do not result in significant increases in solution accuracy, i.e.:

        .. math::
            \| \mathbf{\delta m}_k^{(i-1)} - \mathbf{\delta m}_k^{(i)} \|^2 / \| \mathbf{\delta m}_k^{(i-1)} \|^2 < \textrm{tol_ipcg}


    2. a maximum allowable number of IPCG iterations has been completed, i.e.:

        .. math::
            i = \textrm{max_iter_ipcg}



