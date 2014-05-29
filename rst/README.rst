NOTES
=====

This is a board with yellow sticks. Whenever I find an information I think it
may be usefull in near future, I put it here.

CONTENTS
--------

1. `GPU related notes`_
2. `OpenCL based libraries`_


.. _GPU related notes:
GPU related notes
^^^^^^^^^^^^^^^^^

How to hide some CUDA devices from an application
`````````````````````````````````````````````````

Use ``CUDA_VISIBLE_DEVICES``, see `this article <https://devblogs.nvidia.com/parallelforall/cuda-pro-tip-control-gpu-visibility-cuda_visible_devices/>`_ and `this blog post <http://acceleware.com/blog/cudavisibledevices-masking-gpus>`_.

CUDA Environment variables
``````````````````````````

There is a bunch of environment variables which affect CUDA drivers, see `the documentation <http://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#env-vars>`_.

.. _OpenCL based libraries: 
OpenCL based libraries
^^^^^^^^^^^^^^^^^^^^^^

  * `VexCL <https://github.com/ddemidov/vexcl>`_ vector expression template library for OpenCL/CUDA. Created for ease of GPGPU development with C++. Many useful things including vector expressions for `scattered data interpolation with multilevel B-Splines <https://github.com/ddemidov/vexcl#mba>`_. Note that `Boost.Numeric.Odeint <www.boost.org/libs/numeric/odeint/doc/html/index.html>`_ (ODE integrators in C++) supports OpenCL via `VexCL <https://github.com/ddemidov/vexcl>`_.
