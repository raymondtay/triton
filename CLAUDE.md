# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Triton is a language and compiler for writing highly efficient custom Deep-Learning primitives. It uses MLIR as its compiler infrastructure and supports NVIDIA GPUs (via CUDA/PTX) and AMD GPUs (via ROCm/HIP).

## Build Commands

```shell
# Install development dependencies
make dev-install

# Build (incremental)
make

# Run tests
make test          # All tests (requires GPU)
make test-nogpu    # Compiler tests only (no GPU required)
make test-unit     # Python unit tests
make test-lit      # MLIR lit tests
make test-gluon    # Gluon tests
```

## Key Architecture Points

**Compiler Stack:**
- Python frontend (`python/triton/language/`) -> AST
- Triton IR (`lib/Dialect/Triton/IR/`) -> MLIR dialect
- TritonGPU IR (`lib/Dialect/TritonGPU/IR/`) -> GPU-specific lowering
- Target-specific lowering (NVGPU/AMDGPU) -> LLVM IR
- LLVM JIT or PTX/AMDGCN generation

**Dialects:**
- `Triton` - Core operations (dot, load, store, branch, etc.)
- `TritonGPU` - GPU-specific constructs (warps, blocks, layouts)
- `TritonNvidiaGPU` - NVIDIA-specific ops (TMA, tensor memory)
- `TritonAMDGPU` - AMD-specific ops
- `TritonInstrument` - Debugging/instrumentation

**Layout System:**
TritonGPU uses `LinearLayout` for describing data layouts (blocked, sliced, etc.). Key files:
- `lib/Dialect/TritonGPU/IR/LinearLayoutConversions.cpp`
- `include/triton/Dialect/TritonGPU/IR/LinearLayout.h`

**Python Structure:**
- `python/triton/compiler/` - Compilation pipeline
- `python/triton/runtime/` - JIT, autotuner, driver abstraction
- `python/triton/language/` - Python DSL for kernels
- `python/triton/backends/` - GPU backend implementations

## Common Tasks

**Adding a new MLIR op:**
1. Define in `include/triton/Dialect/Triton/IR/Ops.td` (or subdialect)
2. Implement in `lib/Dialect/Triton/IR/Ops.cpp`
3. Add Python wrapper in `python/triton/language/semantic.py`
4. Add lowering pass in `lib/Dialect/TritonGPU/Transforms/` or `lib/Conversion/`

**Adding a new pass:**
- Place in `lib/Dialect/TritonGPU/Transforms/` (or relevant dialect)
- Register in `include/triton/Dialect/TritonGPU/Transforms/Passes.td`

## Configuration Knobs

See `python/triton/knobs.py` for environment variables:
- `MLIR_ENABLE_DUMP=1` - Dump IR at each pass
- `TRITON_INTERPRET=1` - Run via Python interpreter (no GPU)
- `TRITON_DEBUG=1` - Enable debug output
- `LLVM_IR_ENABLE_DUMP=1` - Dump LLVM IR
- `TRITON_KERNEL_DUMP=1` - Dump compiled kernels
- `TRITON_ALWAYS_COMPILE=1` - Disable cache

## Git Workflow

- All commits must be pushed to git origin **"upstream"**
- Always create pull requests - never commit directly to "upstream"
