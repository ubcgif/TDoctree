.. _theory:

Background Theory
=================

This section aims to provide the user with a basic review of the physics, discretization, and optimization
techniques used to solve the frequency domain electromagnetics problem. It is assumed
that the user has some background in these areas. For further reading see (:cite:`Nabighian1991`).

.. important::

    This code uses the following coordinate system and Fourier convention to solve Maxwell's equations:
        - X = Easting, Y = Northing, Z +ve downward (left-handed)
        - An :math:`e^{-i \omega t}` Fourier convention

.. _theory_fundamentals:

Fundamental Physics
-------------------

Maxwell's equations provide the starting point from which an understanding of how electromagnetic
fields can be used to uncover the substructure of the Earth. In the frequency domain Maxwell's
equations are:

.. math::
    \begin{align}
        \nabla \times &\mathbf{E} - i\omega\mu \mathbf{H} = 0 \\
        \nabla \times &\mathbf{H} - \sigma \mathbf{E} = \mathbf{s} \\
        &\mathbf{\hat{n} \times H} = 0
    \end{align}
    :label: maxwells_eq

where :math:`\mathbf{E}` and :math:`\mathbf{H}` are the electric and magnetic fields, :math:`\mathbf{s}` is some external source and :math:`e^{-i\omega t}` is suppressed.
Symbols :math:`\mu`, :math:`\sigma` and :math:`\omega` are the magnetic permeability, conductivity, and angular frequency, respectively. This formulation assumes a quasi-static mode so that the system can be viewed as a diffusion equation (Weaver, 1994; Ward and Hohmann, 1988 in :cite:`Nabighian1991`). By doing so, some difficulties arise when
solving the system;

    - the curl operator has a non-trivial null space making the resulting linear system highly ill-conditioned
    - the conductivity :math:`\sigma` varies over several orders of magnitude
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


Forward Problem
---------------

Direct Solver Approach
^^^^^^^^^^^^^^^^^^^^^^

To solve the forward problem, we must first discretize and solve for the fields in Eq. :eq:`maxwells_eq`, where :math:`e^{-i\omega t}` is suppressed. Using finite volume discretization, the electric fields on cell edges (:math:`\mathbf{u_e}`) are obtained by solving the following system at every frequency:

.. math::
    \big [ \mathbf{C^T \, M_\mu \, C} + i\omega \mathbf{M_\sigma} \big ] \, \mathbf{u_e} = - i \omega \mathbf{s}
    :label: discrete_e_sys

where :math:`\mathbf{C}` is the curl operator and:

.. math::
    \begin{align}
    \mathbf{M_\mu} &= diag \big ( \mathbf{A^T_{f2c} V} \, \boldsymbol{\mu^{-1}} \big ) \\
    \mathbf{M_\sigma} &= diag \big ( \mathbf{A^T_{e2c} V} \, \boldsymbol{\sigma} \big ) \\
    \end{align}

where :math:`\mathbf{V}` is a diagonal matrix containing  all cell volumes, :math:`\mathbf{A_{f2c}}` averages from faces to cell centres and :math:`\mathbf{A_{e2c}}` averages from edges to cell centres. The magnetic permeabilities and conductivities for each cell are contained within vectors :math:`\boldsymbol{\mu}` and :math:`\boldsymbol{\sigma}`, respectively.

Once the electric field on cell edges has been computed, the electric (:math:`\mathbf{E}`) and magnetic (:math:`\mathbf{H}`) fields at observation locations can be obtain via the following:

.. math::
    \begin{align}
    \mathbf{E} &= \mathbf{Q_e \, u_e} = \mathbf{Q_c \, A_{e2c} \, u_e} \\
    \mathbf{H} &= \mathbf{Q_h \, u_e} = \frac{1}{i \omega} \mathbf{Q_c} \, diag(\boldsymbol{\mu}^{-1}) \, \mathbf{A_{f2c} C \, u_e}
    \end{align}
    :label: fields_projected

where :math:`\mathbf{Q_c}` represents the appropriate projection matrix from cell centers to a particular receiver (Ex, Ey, Ez, Hx, Hy or Hz). If we let

.. math::
    \mathbf{A}(\sigma) = \mathbf{C^T \, M_\mu \, C} + i\omega \mathbf{M_\sigma}
    :label: A_operator

then :eq:`fields_projected` can be written as:

.. math::
    \begin{align}
    \mathbf{E} &= -i\omega \mathbf{Q_e \, A}(\sigma)^{-1} \, \mathbf{s} \\
    \mathbf{H} &= -i\omega \mathbf{Q_h \, A}(\sigma)^{-1} \, \mathbf{s}
    \end{align}
    :label: fwd_solution


.. _theory_solver:

Iterative Solver Approach
^^^^^^^^^^^^^^^^^^^^^^^^^

For this approach we decompose the electric field as follows:

.. math::
    \mathbf{u_e} = \mathbf{a} + \mathbf{G} \phi
    :label: e_decomposition

where :math:`\mathbf{u_e}` is the fields on cell edges, :math:`\mathbf{a}` is the vector potential, :math:`\phi` is the scalar potential and :math:`\mathbf{G}` is the discrete gradient operator. To compute the electric fields, the `BiCGstab <https://en.wikipedia.org/wiki/Biconjugate_gradient_stabilized_method>`__ algorithm is used to solve the following system:

.. math::
    \begin{bmatrix} \mathbf{A} (\sigma) + \mathbf{D} & -i\omega \mathbf{M_\sigma G} \\ -i\omega \mathbf{G^T M_\sigma} & -i\omega \mathbf{G^T M_\sigma G} \end{bmatrix}
    \begin{bmatrix} \mathbf{a} \\ \phi \end{bmatrix} = 
    \begin{bmatrix} -i\omega\mathbf{s} \\ -i\omega \mathbf{G^T s} \end{bmatrix}
    :label: maxwell_a_phi

where

.. math::
    \mathbf{D} = \mathbf{G}  \, diag \big ( \mathbf{A^T_{n2c} V} \, \boldsymbol{\mu^{-1}} \big ) \mathbf{G^T}

is a matrix that is added to the (1,1) block of Eq. :eq:`maxwell_a_phi` to improve the stability of the system and :math:`\mathbf{A}` is given by Eq. :eq:`A_operator`. Once Eq. :eq:`maxwell_a_phi` is solved, Eq. :eq:`e_decomposition` is used to obtain the electric fields on cell edges and Eq. :eq:`fields_projected` computes the fields at the receivers.

Adjustable parameters for solving Eq. :eq:`maxwell_a_phi` iteratively using BiCGstab are defined as follows:

     - **tol_bicg:** relative tolerance (stopping criteria) when solver is used during forward modeling; i.e. solves Eq. :eq:`discrete_e_sys`. Ideally, this number is very small (~1e-10).
     - **tol_ipcg_bicg:** relative tolerance (stopping criteria) when solver needed in computation of :math:`\delta m` during Gauss Newton iteration; i.e. must solve Eq. :eq:`sensitivity_fields` to solve Eq. :eq:`GN_gen`. This value does not need to be as large as the previous parameter (~1e-5).
     - **max_it_bicg:** maximum number of BICG iterations (~100)

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
    \mathbf{m} = log (\boldsymbol{\sigma})


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



