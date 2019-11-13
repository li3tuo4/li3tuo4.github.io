# Machine Learning

A 2019-April paper about tensorflowlite on RISC-V in [CARRV](https://carrv.github.io/2019/papers/carrv2019_paper_7.pdf) 
Pros: full ISA extension for tensorflowlite is designed (optimized a subset of V extension)
Pros: spike test with gcc newlib
Cons: implementation in spike only, not real hardware (they are working on it now) 
Cons: no real compiler support, currently like what we do (assembler hacking)

```
We are developing in-pipeline microarchitectural support for a
subset of the vector instructions for machine learning accelerator.
Our microarchitecture will include dedicated vector registers, 
vector caches and support for multiple precision arithmetic and logical operations. 
Additionally, we will explore sparsity-aware microarchitecture design for the in-pipeline accelerator. 
As we develop the in-pipeline accelerator we will modify the subset of instructions in
Table 1 as needed and update the software tool-chain accordingly.
We will evaluate our design in terms of performance, power and
area. We will open source our software modifications to TF Lite, as
well as the micro-architecture design to the wider community in
due course.
```
## Notes
We can use and optimize SIMD resources for memory safety --- this is done by watchdoglite on Intel --- just like what CAARV19 paper author did for V extension.
