## Research log
[Stack bottom](https://github.com/li3tuo4/li3tuo4.github.io/blob/master/log.md#some-weekly-tasks)

### Cache flush in ARM
#### In ARM trusted firmware
L1 and L2 cache flush in ARM (dcsw_flush_level1/2):
Discussion in [ARM forum](https://community.arm.com/developer/ip-products/processors/f/cortex-a-forum/7271/is-it-necessary-for-arm-v8-soc-to-flush-l2-cache-to-dram)
Code in [cortext_a57.S](https://github.com/ARM-software/arm-trusted-firmware/blob/620d9832f96ffcaf86d38b703ca913438d6eea7c/lib/cpus/aarch64/cortex_a57.S#L561)

ARM's ATF (do_dcsw_op macro): [cache_helper.S](https://github.com/ARM-software/arm-trusted-firmware/blob/620d9832f96ffcaf86d38b703ca913438d6eea7c/lib/aarch64/cache_helpers.S#L90)

The first input to do_dcsw_op is "x0: The operation type (0-2), as defined in arch.h" defined in:
[arch.h](https://github.com/ARM-software/arm-trusted-firmware/blob/620d9832f96ffcaf86d38b703ca913438d6eea7c/include/arch/aarch64/arch.h#L119)

dc instruction is used for cache op = cisw on specified level

##### 16.66 DC

>Data Cache operation.

>This instruction is an alias of SYS.

>The equivalent instruction is SYS #op1, C7, Cm, #op2, Xt.

>Syntax
`DC <dc_op>, Xt`

>Where:

`<dc_op>`
>Is a DC instruction name, as listed for the DC system instruction group, and can be one of the values shown in Usage.
`op1`
>Is a 3-bit unsigned immediate, in the range 0 to 7.
`Cm`
>Is a name Cm, with m in the range 0 to 15.
`op2`
>Is a 3-bit unsigned immediate, in the range 0 to 7.
`Xt`
>Is the 64-bit name of the general-purpose source register.

#### Demo flushx
If we do a ecall in user program, we will get to this interrupt handler code: [mentry.S](https://github.com/li3tuo4/riscv-pk-zcu/blob/917baf3506d82884a496cc41a1e26c0bb576da41/machine/mentry.S#L97)

So, I guess I will add the flushx here after the context is saved!

##### dc op explanation
Discussed in [ARM infocenter](http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.den0024a/BABJDBHI.html)
>11.5. Cache maintenance
>It is sometimes necessary for software to clean or invalidate a cache. This might be required when the contents of external memory have been changed and it is necessary to remove stale data from the cache. It can also be required after MMU-related activity such as changing access permissions, cache policies, or virtual to Physical Address mappings, or when I and D-caches must be synchronized for dynamically generated code such as JIT-compilers and dynamic library loaders.

>Invalidation of a cache or cache line means to clear it of data, by clearing the valid bit of one or more cache lines. The cache must always be invalidated after reset as its contents are undefined. This can also be viewed as a way of making changes in the memory domain outside the cache visible to the user of the cache.

>Cleaning a cache or cache line means writing the contents of cache lines that are marked as dirty, out to the next level of cache, or to main memory, and clearing the dirty bits in the cache line. This makes the contents of the cache line coherent with the next level of the cache or memory system. This is only applicable for data caches in which a write-back policy is used. This is also a way of making changes in the cache visible to the user of the outer memory domain, but is only available for data cache.

>Zero. This zeroes a block of memory within the cache, without the need to first of all read its contents from the outer domain. This is only available for data cache.

>For each of these operations, you can select which of the entries the operation should apply to:

>All, means the entire cache and is not available for the data or unified cache

>Modified Virtual Address (MVA), another name for VA, is the cache line that contains a specific Virtual Address

>Set or Way is a specific cache line selected by its position within the cache structure

>AArch64 cache maintenance operations are performed using instructions which have the following general form:

  `<cache> <operation>{, <Xt>}`
>A number of operations are available.

>Table 11.1. Data cache, instruction cache, and unified cache operations

>Cache	Operation	Description	
>AArch32

>Equivalent
```
DC	CISW	Clean and invalidate by Set/Way	DCCISW
CIVAC	Clean and Invalidate by Virtual Address to Point of Coherency	DCCIMVAC
CSW	Clean by Set/Way	DCCSW
CVAC	Clean by Virtual Address to Point of Coherency	DCCMVAC
CVAU	Clean by Virtual Address to Point of Unification	DCCMVAU
ISW	Invalidate by Set/Way	DCISW
IVAC	Invalidate by Virtual Address, to Point of Coherency	DCIMVAC
DC	ZVA	Cache zero by Virtual Address	-
IC	IALLUIS	Invalidate all, to Point of Unification, Inner Sharable	ICIALLUIS
IALLU	Invalidate all, to Point of Unification, Inner Shareable	ICIALLU
IVAU	Invalidate by Virtual Address to Point of Unification	ICIMVAU
```
#### How ARM Linux uses cache flush
```c
/*
 *	__flush_dcache_area(kaddr, size)
 *
 *	Ensure that any D-cache lines for the interval [kaddr, kaddr+size)
 *	are cleaned and invalidated to the PoC.
 *
 *	- kaddr   - kernel address
 *	- size    - size in question
 */
```
Source code in [cache.S](https://github.com/torvalds/linux/blob/fa3d493f7a573b4e4e2538486e912093a0161c1b/arch/arm64/mm/cache.S#L110)

Macro dcache_by_line_op defined in [assembler.h](https://elixir.bootlin.com/linux/v4.14/source/arch/arm64/include/asm/assembler.h#L341)


### Plan of FPGA testing flushx
Change Rocketchip to flushx branch.
Compile and run normal programs to make sure flushx hardware change does not affect others.
Create a test program, based on some simple benchmark; add volatile inline assembly flushx. Use flushx to replace the fence.i? No, unless we have operand to configure on/off of RF flush, or it's safe to assume the GPR is not live anymore.
Compile and put new program into system and run.
Consider adding flushx in Operating System.

#### Where to add flushx in Linux
Trap entry is in [entry.S](https://github.com/westerndigitalcorporation/RISC-V-Linux/blob/13cb16d5e8e11ebca490cad50cc5abbd222839a1/linux/arch/riscv/kernel/entry.S#L147)

syscall can be made via standard SBI, which handles ecall and arguments in [sbi.h](https://github.com/riscv/riscv-linux/blob/32b1573d1f118844d859341d095e005ca5ba572e/arch/riscv/include/asm/sbi.h#L30)


### Verilator 4.0 for multithreading
`numactl -C 0,1,2,3 executable`

### Some weekly tasks
:coffee:
- [ ] subtypes of cache flush
  - cache writeback
  - cache invalidate (discard)
- [ ] Develop iTLB flush
- [ ] Test iTLB flush
- [ ] Dev dTLB flush
- [ ] Test dTLB flush

#### Problem 1 about M_SFENCE in pipeline
```scala
 // on pipeline flushes, cause mem_npc to hold the sequential npc, which
  // will drive the W-stage npc mux
  when (mem_reg_valid && mem_reg_flush_pipe) {
    mem_reg_sfence := false
  }.elsewhen (ex_pc_valid) {
    mem_ctrl := ex_ctrl //Tuo only if mem_reg_flush_pipe is false, pipe regs will propagate
    mem_reg_rvc := ex_reg_rvc
    mem_reg_load := ex_ctrl.mem && isRead(ex_ctrl.mem_cmd)
    mem_reg_store := ex_ctrl.mem && isWrite(ex_ctrl.mem_cmd)
    mem_reg_sfence := ex_sfence
    mem_reg_btb_resp := ex_reg_btb_resp
    mem_reg_flush_pipe := ex_reg_flush_pipe
    mem_reg_slow_bypass := ex_slow_bypass

    mem_reg_cause := ex_cause
    mem_reg_inst := ex_reg_inst
    mem_reg_raw_inst := ex_reg_raw_inst
    mem_reg_pc := ex_reg_pc
    mem_reg_wdata := alu.io.out
    mem_br_taken := alu.io.cmp_out

    //TODO Tuo ex_sfence affects mem_reg_rs2 generation
    when (ex_ctrl.rxs2 && (ex_ctrl.mem || ex_ctrl.rocc || ex_sfence)) {
      val typ = Mux(ex_ctrl.rocc, log2Ceil(xLen/8).U, ex_ctrl.mem_type)
      mem_reg_rs2 := new StoreGen(typ, 0.U, ex_rs(1), coreDataBytes).data
    }
    when (ex_ctrl.jalr && csr.io.status.debug) {
      // flush I$ on D-mode JALR to effect uncached fetch without D$ flush
      mem_ctrl.fence_i := true
      mem_reg_flush_pipe := true
    }
  }
  ```
  `mem_reg_flush_pipe` will reset `mem_reg_sfence`.
  
  #### Found some discussion about cache control instruction proposals in RISC-V forums
  
  [draft6](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/qXbzqaQbDXU/Hs1AO9xDCQAJ)
  [chinese summary](https://github.com/cnrv/home/pull/93/commits/013b536f2b6dff1bc0e91778a27d5c1d93afc3ac)
  - [x] Have a read! :sweat_smile:
  
  #### Modify TODOs
  As specified in the cache control instruction discussion in *riscv-isa-dev*. It makes sense to me to create two subtypes of flushx:
  - One just writes back
  - One just invalidates entire cache
  - Consider adding cache area as input of these two
  
  #### Dynamic analysis about resetting in D-Cache
  Every cycle cleans one cache set. It takes 64 cycles for 64 sets.
