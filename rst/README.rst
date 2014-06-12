NOTES
=====

This is my "yellow-stick board". Whenever I find an information I think it may
be useful in near future, I put it here.

CONTENTS
--------

1. `GPU related notes`_
2. `OpenCL based libraries`_
3. `MPI notes`_
4. `FMI notes`_
5. `MpCCI notes`_

.. _GPU related notes:
GPU related notes
^^^^^^^^^^^^^^^^^

* How to hide some CUDA devices from an application? Use ``CUDA_VISIBLE_DEVICES``,
  see `this article <https://devblogs.nvidia.com/parallelforall/cuda-pro-tip-control-gpu-visibility-cuda_visible_devices/>`_
  and `this blog post <http://acceleware.com/blog/cudavisibledevices-masking-gpus>`_.

* CUDA Environment variables. There is a bunch of environment variables which
  affect CUDA drivers, see `the documentation
  <http://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#env-vars>`_.

.. _OpenCL based libraries:
OpenCL based libraries
^^^^^^^^^^^^^^^^^^^^^^

Here is list of libraries that may be useful for OpenCL programming:

* `VexCL <https://github.com/ddemidov/vexcl>`_ vector expression template
  library for OpenCL/CUDA. Created for ease of GPGPU development with C++.

  Many useful things including vector expressions for `scattered data
  interpolation with multilevel B-Splines <https://github.com/ddemidov/vexcl#mba>`_.
  Note that `Boost.Numeric.Odeint <www.boost.org/libs/numeric/odeint/doc/html/index.html>`_
  (ODE integrators in C++) supports OpenCL via `VexCL <https://github.com/ddemidov/vexcl>`_.

* `Boost.Compute <http://kylelutz.github.io/compute/>`_. Not yet an official
  boost library. Provides C++ interface to multi-core CPU and GPGPU computing
  platforms. Header only.

.. _MPI notes:
MPI notes
^^^^^^^^^

Compilation should be performed with ``mpicc`` or ``mpic++``. Regarding
OpenMPI, these executables are **not** just shell scripts, but normal ELF
executables. So, it's not feasible to replace them with standard compilers +
extra flags (which are unknown).

Once compiled, a distributed MPI program is started with ``mpirun``. It accepts
a configuration file which defines the  list of nodes to run the job on. You
may scale your application easily by just modifying the list of nodes.

The minimal skeleton for an MPI program is the following

.. code-block:: c

  #include <mpi.h>
  #include <stdio.h>
  #include <stdlib.h>

  int main(int argc, char** argv) {
    // Initialize the MPI environment. The two arguments to MPI Init are not
    // currently used by MPI implementations, but are there in case future
    // implementations might need the arguments.
    MPI_Init(NULL, NULL);

    // Get the number of processes
    int world_size;
    MPI_Comm_size(MPI_COMM_WORLD, &world_size);

    // Get the rank of the process
    int world_rank;
    MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);

    // ...

    // Finalize the MPI environment. No more MPI calls can be made after this
    MPI_Finalize();
  }

The `mpirun` starts multiple instances of the program. Each one receives
different rank (the ``world_rank``) in a "global" communicator called
``MPI_COMM_WORLD``.

An MPI program creates one or more communicators. Each communicator defines a
group of one or more processes that can communicate. There is always one
communicator, the ``MPI_COMM_WORLD`` which is available just after
``MPI_Init()`` and contains all the processes started by ``mpirun``, but 
obviously you can create custom communicators containing only subgroups of the
available processes.

BTL stands for Byte Transfer Layer, and BML is a BTL Management Layer. BTL
represents available communication mechanisms, for example loopback, TCP,
infiniband.

You may configure many parameters via MCA parameters. For example, you may
instruct MPI to use only a subset of available BTL mechanisms (for example
loopback and infiniband).

.. code-block:: sh

    ptomulik@node00:$ mpirun -mca btl self,openib -n4 -hostfile host_file ./mpi_hello_world


.. _FMI notes:
FMI notes
^^^^^^^^^

`FMI <https://www.fmi-standard.org/>`_ stands for Functional Mockup Interface.
The standard is being implemented by serveral vendors of engineering tools to
support cosimulation.

The **FMI 1.0** has the following limitations, which render it not suitable to
implement the waveform-Newton algorithm:

- it does not support waveforms, i.e. it assumes that the subsystems and
  master exchange only the values *u(tc_i)* and *y(tc_i)* (subsystems' inputs
  and outputs) at single time point, as an option it may also exchange
  derivatives, but this is not a big deal for us;
- it assumes that the whole system consists of submodules (in-out blocks)
  connected directly one to each other; there seems to be no natural way to
  introduce constraints equations; generally the kinematical constraints can't
  be effectively expressed by input-output connections,
- it assumes particular form of data to be sent (variables, derivatives); for
  waveform-based co-simulations a more effective ways may be used, for example
  B-spline coefficients to describe trajectory over *tc_i*, *tc_{i+1}*.

For the representation of waveforms we probably could use existing standard to
define inputs and outputs such that they would represent waveforms, but the
waveforms had to be of constant size (constant number of representative
points). The other downside is that the interpretation of inputs and outputs
would be unclear and inconsistent with the current standard terminology.

The *FMI 2.0* doesn't seem to have these issues addressed (I still have to
investigate this).

.. _MpCCI notes:
MpCCI notes
^^^^^^^^^^^

`MpCCI <http://www.mpcci.de/>`_ stands for Mesh-based Code Coupling Interface.
It's being developed by `Fraunhofer SCAI <http://www.scai.fraunhofer.de/en.html>`_ 
(Fraunhofer Institute for Algorithms and Scientific Computing). Lot of 
`customers <http://www.mpcci.de/reference-projects/mpcci-customers.html>`_
from several engineering fields have used the MpCCI solutions.

MpCCI software is a **closed-source**, **non-free** solution. It's distributed
as object code under a `scapos EULA license <http://www.mpcci.de/fileadmin/mpcci/download/LicenseAgreements/MpCCI-20110302-scapos-End-User-License-Agreement.pdf>`_.
There are three types of the license: commercial (12 months), research (12
months), evaluation (30 days). Current version is **4.3**. 

*MpCCI Coupling Environment* is the standard for simulation code coupling. It
provides an application independent interface for the coupling of different
simulation codes. Direct communication between coupled codes is implemented by
providing **adapters** for commercial codes. The adapters use existing APIs of
the simulation tools. The list of supported tools may be found in the
`manual <http://www.mpcci.de/fileadmin/mpcci/download/MpCCI-4.3.0/doc/pdf/MpCCIdoc.pdf>`_
(Release Notes, II-5.2). **MSC Adams is supported** on 64-bit platforms.

Components:

- Code Adapter,
- GUI (to define coupling setup and to start simulation),
- Coupling Server (environment handling, communication between codes,
  **neighborhood computation** and **interpolation**),

Types of coupling in general:

- **strong coupling**: all governing equations of a coupled problem are
  combined in a large system; this **is not** the basic MpCCI approach,
  but may be achieved in an iterative coupling scenario,
- **weak coupling**: each problem is solved separately and some variables are
  exchanged and inserted into the equations of the other problem; this **is**
  the basic MpCCI approach,

Aspects of data exchange in MpCCI:

- **association**: each point and/or element is linked to a partner in the
  other system; the process of finding partners is called *neighborhood
  search*. 
- **interpolation**: the quantities must be transferred to the associated
  partner on the other mesh; different mesh geometries, data distributions and
  the conservation of fluxes must be considered here.

The basic structure is shown in `manual <http://www.mpcci.de/fileadmin/mpcci/download/MpCCI-4.3.0/doc/pdf/MpCCIdoc.pdf>`_ (User Manual, V-1.1).

Physical domains (User Manual, V-3.1):

- Solid mechanics (FEM),
- Fluid mechanics (CFD),
- Acoustics,
- Fluid pipe systems,
- Solid heat transfer,
- Fluid heat transfer,
- Heat radiation,
- Electromagnetism,
- **Multibody dynamics**.

Coupling process:

- initialization,
- iteration,
- finalization.

During the iteration, which also consists of several time steps in a transient
problem, the data is exchanged several times. [..] The data is also not
associated with certain time steps or iterations. The possible coupling
algorithms are mainly determined by the capabilities of the simulation codes
and the corresponding code adapters.Send and receive operations can appear at
different states of the computation:

- at the beginning of each time step,
- at the end of each time step,
- before or after an iteration step,
- on direct demand of the user.

The data to be sent is first stored by MpCCI, i.e. it may be received later by
the other code. MpCCI can **store several sets of data**, which are then sent
to the other code depending on the synchronization point set by the code.

Types of problems and coupling algorithms:

- **stationary** problems: the coupling algorithm does not have a big influence
  on the solution,
- **transient** problems: two general cases - both codes send and receive data
  (cycle), or one code only sends while the other only receives.

Coupling algorithms `manual <http://www.mpcci.de/fileadmin/mpcci/download/MpCCI-4.3.0/doc/pdf/MpCCIdoc.pdf>`_
(User Manual, V-3.4) define possible schedules of data exchange between
subsystems. For transient problems with strong interactions (e.g. FSI with
incompressible fluid), **iterative coupling algorithms** (V-3.4.4) are
proposed. Gauss-Jacobi and Gauss-Seidel schemas are presented. According to
(V-3.4.4.4) the iterative coupling is not supported by ADAMS.

It seems, that waveform-relaxation algorithms could be constructed using
iterative coupling schemes and non-matching time steps (V-3.4.7). It's not sure,
however, if the two techniques can be mixed (three actually: add subcycling,
V-3.4.6).
