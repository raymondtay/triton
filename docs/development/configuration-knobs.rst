Configuration Knobs
===================

Triton provides numerous environment variables (called "knobs") to control compilation, runtime behavior, debugging, and performance. This guide documents all available knobs.

Usage
-----

Knobs can be set as environment variables:

.. code-block:: bash

    TRITON_INTERPRET=1 python my_script.py

Or programmatically in Python:

.. code-block:: python

    import triton
    triton.knobs.runtime.interpret = True

Configuration Knobs Reference
-----------------------------

Build Knobs
^^^^^^^^^^^

These control how the native compiler is invoked.

- ``CC`` - C compiler to use
- ``TRITON_CUDACRT_PATH`` - Path to CUDA runtime library
- ``TRITON_CUDART_PATH`` - Path to CUDA toolchain

Cache Knobs
^^^^^^^^^^^

Control caching behavior and file locations.

- ``TRITON_HOME`` - Base directory for Triton's cache (default: ``~/``)
- ``TRITON_CACHE_DIR`` - Directory for cached compiled kernels (default: ``~/.triton/cache``)
- ``TRITON_DUMP_DIR`` - Directory for dumped IR (default: ``~/.triton/dump``)
- ``TRITON_OVERRIDE_DIR`` - Directory for kernel overrides (default: ``~/.triton/override``)

Compilation Knobs
^^^^^^^^^^^^^^^^^

Control compilation behavior.

- ``TRITON_KERNEL_OVERRIDE`` - Enable kernel override mode (bool)
- ``TRITON_KERNEL_DUMP`` - Dump IR at each compilation stage (bool)
- ``LLVM_EXTRACT_DI_LOCAL_VARIABLES`` - Emit full debug info (bool)
- ``TRITON_STORE_BINARY_ONLY`` - Store only binary, not IR (bool)
- ``TRITON_ALWAYS_COMPILE`` - Always compile, ignore cache (bool)
- ``USE_IR_LOC`` - Use IR line numbers instead of Python line numbers (values: ``ttir``, ``ttgir``, or unset)
- ``TRITON_ENABLE_ASAN`` - Enable AddressSanitizer (AMD only, bool)
- ``TRITON_DISABLE_LINE_INFO`` - Remove line info from output (bool)
- ``TRITON_FRONT_END_DEBUGGING`` - Disable exception wrapping for debugging (bool)
- ``TRITON_ALLOW_NON_CONSTEXPR_GLOBALS`` - Allow non-constexpr global variables (bool)
- ``TRITON_INSTRUMENTATION_MODE`` - Instrumentation mode string
- ``TRITON_DUMP_PTXAS_LOG`` - Dump PTXAS compilation log (bool)
- ``TRITON_MOCK_PTX_VERSION`` - Mock PTX version for testing

Runtime Knobs
^^^^^^^^^^^^^

Control runtime behavior.

- ``TRITON_INTERPRET`` - Run via Python interpreter instead of GPU (bool)
- ``TRITON_DEBUG`` - Enable debug output (bool)
- ``TRITON_OVERRIDE_ARCH`` - Override GPU architecture string
- ``TRITON_PRINT_AUTOTUNING`` - Print autotuning results (bool)
- ``TRITON_CACHE_AUTOTUNING`` - Cache autotuning results (bool)
- ``TRITON_DEFAULT_FP_FUSION`` - Enable FP fusion by default (bool)
- ``TRITON_F32_DEFAULT`` - Default precision for tl.dot with f32 (values: ``ieee``, ``tf32``, ``tf32x3``)

NVIDIA Backend Knobs
^^^^^^^^^^^^^^^^^^^^

Control NVIDIA-specific behavior.

- ``TRITON_CUOBJDUMP_PATH`` - Path to cuobjdump binary
- ``TRITON_NVDISASM_PATH`` - Path to nvdisasm binary
- ``TRITON_PTXAS_PATH`` - Path to ptxas binary
- ``TRITON_PTXAS_BLACKWELL_PATH`` - Path to ptxas-blackwell binary
- ``NVPTX_ENABLE_DUMP`` - Dump NVPTX IR (bool)
- ``DISABLE_PTXAS_OPT`` - Disable PTXAS optimizations (bool)
- ``PTXAS_OPTIONS`` - Additional options to pass to ptxas
- ``TRITON_LIBDEVICE_PATH`` - Path to libdevice.bc
- ``TRITON_LIBCUDA_PATH`` - Path to libcuda.so

AMD Backend Knobs
^^^^^^^^^^^^^^^^^

Control AMD-specific behavior.

- ``AMDGCN_USE_BUFFER_OPS`` - Use buffer operations (bool, default: true)
- ``AMDGCN_USE_BUFFER_ATOMICS`` - Use buffer atomics (requires buffer ops, bool)
- ``AMDGCN_ANALYZE_SMALL_TENSOR_RANGE`` - Analyze small tensor range (bool)
- ``AMDGCN_ENABLE_DUMP`` - Dump AMDGCN IR (bool)
- ``TRITON_LIBHIP_PATH`` - Path to libhip.so
- ``TRITON_HIP_USE_BLOCK_PINGPONG`` - Use block ping-pong (bool, auto)
- ``TRITON_HIP_USE_IN_THREAD_TRANSPOSE`` - Use in-thread transpose (bool, auto)
- ``TRITON_HIP_USE_ASYNC_COPY`` - Use async copy (bool, auto)
- ``AMDGCN_SCALARIZE_PACKED_FOPS`` - Scalarize packed float ops (bool)
- ``TRITON_DUMP_MIR`` - Path to dump MIR files
- ``TRITON_SWAP_MIR`` - Path to external MIR files to use
- ``TRITON_SWAP_MIR_ENABLE_MISCHED`` - Enable MIR scheduler (bool)

Proton Profiler Knobs
^^^^^^^^^^^^^^^^^^^^^

Control Proton profiling behavior.

- ``TRITON_PROTON_DISABLE`` - Disable Proton (bool)
- ``TRITON_CUPTI_LIB_PATH`` - Path to CUPTI library
- ``TRITON_CUPTI_LIB_BLACKWELL_PATH`` - Path to CUPTI library for Blackwell
- ``TRITON_PROFILE_BUFFER_SIZE`` - Profile buffer size in bytes (default: 64MB)
- ``TRITON_ENABLE_NVTX`` - Enable NVTX markers (bool)
- ``TRITON_ENABLE_HW_TRACE`` - Enable hardware tracing (Blackwell+, bool)

Commonly Used Knobs for Debugging
---------------------------------

IR Dumping
~~~~~~~~~~

.. code-block:: bash

    MLIR_ENABLE_DUMP=1 python my_script.py

This dumps the IR before every MLIR pass. To dump only for a specific kernel:

.. code-block:: bash

    MLIR_ENABLE_DUMP=my_kernel_name python my_script.py

To specify a dump directory:

.. code-block:: bash

    MLIR_DUMP_PATH=/path/to/dump MLIR_ENABLE_DUMP=1 python my_script.py

LLVM IR Dumping
~~~~~~~~~~~~~~~

.. code-block:: bash

    LLVM_IR_ENABLE_DUMP=1 python my_script.py

This dumps the LLVM IR before every pass.

Reproducer Generation
~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

    TRITON_REPRODUCER_PATH=/tmp/reproducer.mlir python my_script.py

This generates an MLIR reproducer file at the specified path before each compiler stage.

Interpreter Mode
~~~~~~~~~~~~~~~~

.. code-block:: bash

    TRITON_INTERPRET=1 python my_script.py

This runs kernels via the Python interpreter on CPU, useful for debugging and prototyping.

Performance Knobs
-----------------

.. code-block:: bash

    # Use clang and lld for faster builds
    TRITON_BUILD_WITH_CLANG_LLD=true pip install -e .

    # Use ccache for faster rebuilds
    TRITON_BUILD_WITH_CCACHE=true pip install -e .

    # Limit build jobs to reduce memory usage
    MAX_JOBS=4 pip install -e .

    # Disable build isolation for faster nop builds
    pip install -e . --no-build-isolation
