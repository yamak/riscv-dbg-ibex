# RISC-V Debug Support for various Cores

This module is an implementation of a debug unit compliant with the [RISC-V
debug specification](https://github.com/riscv/riscv-debug-spec) v0.13.1. It is
used in the [cva6](https://github.com/pulp-platform/cva6),
[cv32e40p](https://github.com/pulp-platform/cv32e40p) and
[ibex](https://github.com/lowRISC/ibex) cores.

## Fork Information: Ibex Compatibility Patch

**This repository is a fork of [riscv-dbg](https://github.com/pulp-platform/riscv-dbg),** modified to work smoothly with the [Ibex](https://github.com/lowRISC/ibex) core. A patch from *Michael Schaffner*, originally designed for the [ibex-demo-system](https://github.com/lowRISC/ibex-demo-system), has been applied. This patch replaces certain PULP-specific primitives with lowRISC primitives and updates the CDC/FIFO modules for compatibility.

### What’s Changed?

1. **Ibex Demo System Patch**  
   - Modifies `dm_csrs.sv`, `dmi_cdc.sv`, and related files to replace PULP primitives with `prim_fifo_sync` and `prim_fifo_async_simple`.
   - Adjusts signal handling to align with Ibex’s test and reset environment.

2. **Test/Scan Mode Updates**  
   - Adds `testmode_i` inputs in select modules for Ibex test strategies.
   - Includes minor reset and clock synchronization changes.

### Why This Fork?

- **Ibex Integration**: The upstream `pulp_riscv_debug` repository targets PULP-specific flows. This fork incorporates the necessary modifications so you can seamlessly use it in an Ibex-based SoC or project.
- **Simplified Submodule Flow**: By referencing this fork as a submodule, you avoid manual patching whenever you pull updates.

## Implementation
We use an execution-based technique, also described in the specification, where
the core is running in a "park loop". Depending on the request made to the debug
unit via JTAG over the Debug Transport Module (DTM), the code that is being
executed is changed dynamically. This approach simplifies the implementation
side of the core, but means that the core is in fact always busy looping while
debugging.

## Features
The following features are currently supported

* Parametrizable buswidth for `XLEN=32` `XLEN=64` cores
* Accessing registers over abstract command
* Program buffer
* System bus access (only `XLEN`)
* DTM with JTAG interface

These are not implemented (yet)

* Trigger module
* Quick access using abstract commands
* Accessing memory using abstract commands
* Authentication

## Limitations
* The JTAG clock frequency needs to be lower than the system's clock frequency (see also https://github.com/pulp-platform/riscv-dbg/issues/163). 

## Tests

We use OpenOCD's [RISC-V compliance
tests](https://github.com/riscv/riscv-openocd/blob/riscv/src/target/riscv/riscv-013.c),
our custom testbench in `tb/` and
[riscv-tests/debug](https://github.com/riscv/riscv-tests/tree/master/debug).
