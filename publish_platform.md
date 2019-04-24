# Plan for publishing ZCU102 Rocket-chip RISC-V

## Outline

* Introduction
  * Goal: provide rich evaluatoin of RISC-V with Rocket Chip on ZCU102

* Background

* Experimental Methodology
  * ZCU102 porting
  * Configs
    * Different tool chains (LLVM vs GCC)
    * Application execution environment (AEE, OS vs PK)
    * Tune the microarch and ISA (cache config, and instruction set extension)
  * Benchmarks
    * SPEC2006
    * Interesting domains, such as AI and IoT
    * EEMBC
  * Comparison to soft-core such as Xilinx's
  * Use performance counter to obtain events, which takes long time to get in software and HDL simulation (sure about software?).
* Experiment and Results
  * TBD
