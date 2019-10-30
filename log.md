## Research log




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
- [x] subtypes of cache flush
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
  The variable (register) flushCounter is initialized to 196, i.e., `UInt(nSets * (nWay-1))`. This counter is reused later by `M_FLUSH_ALL` (and our `M_FLUSH_X`). The current impl of resetting mechanism discards d-cache but assumes that it is only enabled at the system reset. Therefore, the `flushCounter` is assigned to 0 after resetting is done, which is in range `[0,(nSets*nWays)-1]`.
  
  This assumption is very different to our use case, which is in the middle of program execution. We need to use an extra register as the counter, which is assigned to `UInt(nSets * (nWay-1))` after resetting is done.  
  
 ### A new plan about writing for the platform
[Link to the md](https://github.com/li3tuo4/li3tuo4.github.io/blob/master/publish_platform.md#plan-for-publishing-zcu102-rocket-chip-risc-v)
Consider whether to do it now or later.

### A bug regarding flush logics in rocket core
The flush logic will accidentally flush one more cache line after the entire flush operation is finished.
The code with the fix `&& !writebackxed` and `&& !flushxed`:
```  
s1_flush_valid := metaArb.io.in(5).fire() && !s1_flush_valid && !s2_flush_valid_pre_tag_ecc && release_state === s_ready && !release_ack_wait
metaArb.io.in(5).valid := flushing || (flushxing && !flushxed) || (writebackxing && !writebackxed) //Tuo add writebackxing in input
```
This bug creates huge trouble for M_WRITEBACK. After the entire cache writeback is done, an arbitrary cacheline will be flushed! metadata.coh becomes nothing (0) for that cacheline. Hence, a cache-miss will occur if later a memory reference is on that cacheline, which should have been a hit.

## About visualizing tilelink
Check [here](https://forums.sifive.com/t/tilelink-visualisation/1825)
```
Open the generated graphml file for your design using https://www.yworks.com/products/yed 11.
Then in the GUI select Layout => Hierarchical, pick some options, and hit ok.
```
## About compiling sel4test
Using new riscv-gcc tool chain can lead to the old sel4test `10.0.0-dev` failure during execution.
```
Starting test 90: Test all tests ran
Test suite failed. 85/90 tests passed.
*** FAILURES DETECTED ***


seL4 root server abort()ed
Debug halt syscall from user thread 0xffffffc087fd7a00 "sel4test-driver"
halting...Power off

```
And, new sel4test at master branch `10.1.1-dev` does not work on board. It works on qemu.
The system halts in
```
BOOT_CODE static void arch_init_freemem(region_t ui_reg, v_region_t ui_v_reg)
```
in `kernel/src/arch/riscv/kernel/boot.c`.

So, we need to build sel4test with release version (10.1.1):
```
repo init -u https://github.com/seL4/sel4test-manifest.git -b refs/tags/10.1.1
repo sync
```

The new argument to build sel4test for rocketchip zedboard for `10.1.1` is

`../init-build.sh -DPLATFORM=spike -DRISCV64=TRUE -DKernelSpikeInstance=rocket-chip-zedboard`

In `kernel/tools/hardware_gen.py`, an error can happen during build:
```
Traceback (most recent call last):
  File "/home/lituo/workspace/riscv/newsel4test/kernel/tools/hardware_gen.py", line 1063, in <module>
    main(args)
  File "/home/lituo/workspace/riscv/newsel4test/kernel/tools/hardware_gen.py", line 989, in main
    schema = yaml.load(args.schema, Loader=yaml.FullLoader)
AttributeError: 'module' object has no attribute 'FullLoader'
CMake Error at kernel/config.cmake:162 (message):
  Failed to generate:
  /home/lituo/workspace/riscv/newsel4test/cbuild/kernel/gen_headers/plat/machine/devices_gen.h
Call Stack (most recent call first):
  kernel/CMakeLists.txt:59 (include)
```

A simple patch to this error is to change `FullLoader` to `SafeLoader` in these two places. 

Time test of sel4test with flushx inserted:

```
Starting test 90: Test all tests ran
Test suite passed. 90 tests passed. 59 tests disabled.
All is well in the universe


seL4 root server abort()ed
Debug halt syscall from user thread 0xffffffc087fd7a00 "sel4test-driver"
halting...Power off

real    3m55.795s
user    2m34.812s
sys     1m11.008s
```
Time test of nominal sel4test:
```
Starting test 90: Test all tests ran
Test suite passed. 90 tests passed. 59 tests disabled.
All is well in the universe


seL4 root server abort()ed
Debug halt syscall from user thread 0xffffffc087fd7a00 "sel4test-driver"
halting...Power off

real    2m38.223s
user    1m43.080s
sys     0m45.376s
```
## Compile SPEC2006
For `483.xalancbmk`, use this [solution](https://yulistic.gitlab.io/2017/04/speccpu2006-formattertohtml.cpp-memset-was-not-declared-error/) to fix.

## Compile riscv-linux newer than 4.15
The reason of using new linux is that starting from `4.18`, perf_events is supported.
The error with gcc can be solved by this [solution](https://github.com/sifive/freedom-u-sdk/issues/66).
One more correction: use `CROSS_COMPILE=riscv64-unknown-linux-gnu-`.

### riscv-linux 4.20
 `cpu = of_find_node_by_path("/cpus");` in arch/riscv/kerne/time.c should be fixed to `cpu = of_find_node_by_path("/cpus/cpu@0")`.
 
 Add `CONFIG_HVC_RISCV_SBI=y` in `conf/linux_defconfig`. 
 
 ## Coremark run (flushxRISCV x native program)
 Run1:
 
 ```
 # ./coremark.exe 0x0 0x0 0x66 0 7 1 2000
2K performance run parameters for coremark.
CoreMark Size    : 666
Total ticks      : 15208
Total time (secs): 15.208000
Iterations/Sec   : 263.019463
Iterations       : 4000
Compiler version : GCC7.2.0
Compiler flags   : -O2 -fno-common -funroll-loops -finline-functions --param max-inline-
insns-auto=20 -falign-functions=4 -falign-jumps=4 -falign-loops=4 --param inline-min-spe
edup=10 -DPERFORMANCE_RUN=1  -lrt
Memory location  : Please put data memory location here
                        (e.g. code in flash, data on heap etc)
seedcrc          : 0xe9f5
[0]crclist       : 0xe714
[0]crcmatrix     : 0x1fd7
[0]crcstate      : 0x8e3a
[0]crcfinal      : 0x65c5
Correct operation validated. See README.md for run and reporting rules.
CoreMark 1.0 : 263.019463 / GCC7.2.0 -O2 -fno-common -funroll-loops -finline-functions -
-param max-inline-insns-auto=20 -falign-functions=4 -falign-jumps=4 -falign-loops=4 --pa
ram inline-min-speedup=10 -DPERFORMANCE_RUN=1  -lrt / Heap
```
Run2:

```
# ./coremark.exe 0x3415 0x3415 0x66 0 7 1 2000
2K validation run parameters for coremark.
CoreMark Size    : 666
Total ticks      : 15248
Total time (secs): 15.248000
Iterations/Sec   : 262.329486
Iterations       : 4000
Compiler version : GCC7.2.0
Compiler flags   : -O2 -fno-common -funroll-loops -finline-functions --param max-inline-
insns-auto=20 -falign-functions=4 -falign-jumps=4 -falign-loops=4 --param inline-min-spe
edup=10 -DPERFORMANCE_RUN=1  -lrt
Memory location  : Please put data memory location here
                        (e.g. code in flash, data on heap etc)
seedcrc          : 0x18f2
[0]crclist       : 0xe3c1
[0]crcmatrix     : 0x0747
[0]crcstate      : 0x8d84
[0]crcfinal      : 0x5249
Correct operation validated. See README.md for run and reporting rules.
```
## Existing flush instruction 
### x86_64 clflush() instruction counts

Example from Mastik's `fr_probe()`:

```
inline int vl_len(vlist_t vl) {
  assert(vl != NULL);
  return vl->len;
  401ea6:       8b 50 04                mov    0x4(%rax),%edx
  for (int i = 0; i < l; i++) 
  401ea9:       85 d2                   test   %edx,%edx
  401eab:       7e 22                   jle    401ecf <fr_probe+0x7f>
  401ead:       48 8b 40 08             mov    0x8(%rax),%rax
  401eb1:       83 ea 01                sub    $0x1,%edx
  401eb4:       48 8d 4c d0 08          lea    0x8(%rax,%rdx,8),%rcx
  401eb9:       0f 1f 80 00 00 00 00    nopl   0x0(%rax)
  401ec0:       48 8b 10                mov    (%rax),%rdx
  401ec3:       0f ae 3a                clflush (%rdx)
  401ec6:       48 83 c0 08             add    $0x8,%rax
  401eca:       48 39 c8                cmp    %rcx,%rax
  401ecd:       75 f1                   jne    401ec0 <fr_probe+0x70>
    clflush(vl_get(fr->evict, i));
}
  401ecf:       5b                      pop    %rbx
  401ed0:       5d                      pop    %rbp
  401ed1:       c3                      retq   
  401ed2:       0f 1f 40 00             nopl   0x0(%rax)
  401ed6:       66 2e 0f 1f 84 00 00    nopw   %cs:0x0(%rax,%rax,1)
  401edd:       00 00 00 
```
The hot loop is:
```
  401ec0:       48 8b 10                mov    (%rax),%rdx
  401ec3:       0f ae 3a                clflush (%rdx)
  401ec6:       48 83 c0 08             add    $0x8,%rax
  401eca:       48 39 c8                cmp    %rcx,%rax
  401ecd:       75 f1                   jne    401ec0 <fr_probe+0x70>
```
Thereby, without fusion it at least costs 5 dynamic instruction fetch and 5xinst_width (3.71 bytes per instruction, in [UCB/EECS-2016-130](https://people.eecs.berkeley.edu/~krste/papers/EECS-2016-130.pdf)) dynamic instruction bytes, per cache line.

### ARM in seL4

The main L1 flush function is in `kernel/src/arch/arm/machine/cache.c`.
The main L1D flush function is in `kernel/src/arch/arm/armv/armv8-a/64/cache.c`.

```
void
cleanInvalidateL1Caches(void)
{
    dsb();
    cleanInvalidate_D_PoC();
    dsb();
    invalidate_I_PoU();
    dsb();
}
```

```
void
cleanInvalidate_D_PoC(void)
{
    int clid = readCLID();
    int loc = LOC(clid);
    int l;

    for (l = 0; l < loc; l++) {
        if (CTYPE(clid, l) > ARMCacheI) {
            word_t s = readCacheSize(l, 0);
            int lbits = LINEBITS(s);
            int assoc = ASSOC(s);
            int assoc_bits = wordBits - clzl(assoc - 1);
            int nsets = NSETS(s);
            int w;

            for (w = 0; w < assoc; w++) {
                int s;

                for (s = 0; s < nsets; s++) {
                    cleanInvalidateByWSL((w << (32 - assoc_bits)) |
                                         (s << lbits) | (l << 1));
                }
            }
        }
    }
}
```
```
static inline void invalidate_I_PoU(void)
{
    asm volatile("ic iallu");
    isb();
}
```

#### ARM Flush Cost Modeling

The main function assembly:
```
ffffff8000011de0 <cleanInvalidateL1Caches>:
ffffff8000011de0:       a9bf7bfd        stp     x29, x30, [sp, #-16]!
ffffff8000011de4:       910003fd        mov     x29, sp
ffffff8000011de8:       d5033f9f        dsb     sy
ffffff8000011dec:       97ffff57        bl      ffffff8000011b48 <cleanInvalidate_D_PoC>
ffffff8000011df0:       d5033f9f        dsb     sy
ffffff8000011df4:       d508751f        ic      iallu
ffffff8000011df8:       d5033fdf        isb
ffffff8000011dfc:       d5033f9f        dsb     sy
ffffff8000011e00:       a8c17bfd        ldp     x29, x30, [sp], #16
ffffff8000011e04:       d65f03c0        ret
```
```
ffffff8000011b48 <cleanInvalidate_D_PoC>:
ffffff8000011b48:       d539002d        mrs     x13, clidr_el1
ffffff8000011b4c:       2a0d03ee        mov     w14, w13
ffffff8000011b50:       531869ad        ubfx    w13, w13, #24, #3
ffffff8000011b54:       340005cd        cbz     w13, ffffff8000011c0c <cleanInva
lidate_D_PoC+0xc4>
ffffff8000011b58:       0b0d05ad        add     w13, w13, w13, lsl #1
ffffff8000011b5c:       d280000c        mov     x12, #0x0                       
// #0
ffffff8000011b60:       5280000a        mov     w10, #0x0                       
// #0
ffffff8000011b64:       5280002f        mov     w15, #0x1                       
// #1
ffffff8000011b68:       1aca29c0        asr     w0, w14, w10
ffffff8000011b6c:       12000800        and     w0, w0, #0x7
ffffff8000011b70:       7100041f        cmp     w0, #0x1
ffffff8000011b74:       54000449        b.ls    ffffff8000011bfc <cleanInvalidat
e_D_PoC+0xb4>  // b.plast
ffffff8000011b78:       d53a0001        mrs     x1, csselr_el1
ffffff8000011b7c:       2a0c03eb        mov     w11, w12
ffffff8000011b80:       d51a000c        msr     csselr_el1, x12
ffffff8000011b84:       d5390000        mrs     x0, ccsidr_el1
ffffff8000011b88:       d51a0001        msr     csselr_el1, x1
ffffff8000011b8c:       53033009        ubfx    w9, w0, #3, #10
ffffff8000011b90:       12000805        and     w5, w0, #0x7
ffffff8000011b94:       110010a5        add     w5, w5, #0x4
ffffff8000011b98:       530d6c00        ubfx    w0, w0, #13, #15
ffffff8000011b9c:       93407d28        sxtw    x8, w9
ffffff8000011ba0:       11000403        add     w3, w0, #0x1
ffffff8000011ba4:       dac01108        clz     x8, x8
ffffff8000011ba8:       11000529        add     w9, w9, #0x1
ffffff8000011bac:       1ac521e5        lsl     w5, w15, w5
ffffff8000011bb0:       52800007        mov     w7, #0x0                        // #0
ffffff8000011bb4:       1ac821e8        lsl     w8, w15, w8
ffffff8000011bb8:       52800006        mov     w6, #0x0                        // #0
ffffff8000011bbc:       d503201f        nop
ffffff8000011bc0:       2a0b00e4        orr     w4, w7, w11
ffffff8000011bc4:       52800002        mov     w2, #0x0                        // #0
ffffff8000011bc8:       52800001        mov     w1, #0x0                        // #0
ffffff8000011bcc:       d503201f        nop
ffffff8000011bd0:       2a040040        orr     w0, w2, w4
ffffff8000011bd4:       93407c00        sxtw    x0, w0
ffffff8000011bd8:       d5087e40        dc      cisw, x0
ffffff8000011bdc:       11000421        add     w1, w1, #0x1
ffffff8000011be0:       0b050042        add     w2, w2, w5
ffffff8000011be4:       6b01007f        cmp     w3, w1
ffffff8000011be8:       54ffff4c        b.gt    ffffff8000011bd0 <cleanInvalidate_D_PoC+0x88>
ffffff8000011bec:       110004c6        add     w6, w6, #0x1
ffffff8000011bf0:       0b0800e7        add     w7, w7, w8
ffffff8000011bf4:       6b06013f        cmp     w9, w6
ffffff8000011bf8:       54fffe4c        b.gt    ffffff8000011bc0 <cleanInvalidate_D_PoC+0x78>
ffffff8000011bfc:       11000d4a        add     w10, w10, #0x3
ffffff8000011c00:       9100098c        add     x12, x12, #0x2
ffffff8000011c04:       6b0d015f        cmp     w10, w13
ffffff8000011c08:       54fffb01        b.ne    ffffff8000011b68 <cleanInvalidate_D_PoC+0x20>  // b.any
ffffff8000011c0c:       d65f03c0        ret
```
The hot loop in `cleanInvalidate_D_PoC`:
```
ffffff8000011bc0:       2a0b00e4        orr     w4, w7, w11
ffffff8000011bc4:       52800002        mov     w2, #0x0                        // #0
ffffff8000011bc8:       52800001        mov     w1, #0x0                        // #0
ffffff8000011bcc:       d503201f        nop
ffffff8000011bd0:       2a040040        orr     w0, w2, w4
ffffff8000011bd4:       93407c00        sxtw    x0, w0
ffffff8000011bd8:       d5087e40        dc      cisw, x0
ffffff8000011bdc:       11000421        add     w1, w1, #0x1
ffffff8000011be0:       0b050042        add     w2, w2, w5
ffffff8000011be4:       6b01007f        cmp     w3, w1
ffffff8000011be8:       54ffff4c        b.gt    ffffff8000011bd0 <cleanInvalidate_D_PoC+0x88>
ffffff8000011bec:       110004c6        add     w6, w6, #0x1
ffffff8000011bf0:       0b0800e7        add     w7, w7, w8
ffffff8000011bf4:       6b06013f        cmp     w9, w6
ffffff8000011bf8:       54fffe4c        b.gt    ffffff8000011bc0 <cleanInvalidate_D_PoC+0x78>
```

**l1dc_hot_loop_insts = sets x ways x 7 + ways x 8**

 //assume loc == 1
 
**l1dc_flush_insts (`cleanInvalidate_D_PoC`) = l1dc_hot_loop_insts(sets,ways) + front(=30) + back(=5)**

**l1c_flush_insts (`cleanInvalidateL1Caches`) = l1dc_flush_insts + 10**

## L1 flush from user mode in ARM

```
00000000004006d8 <__clear_cache>:
  4006d8:       14000002        b       4006e0 <__aarch64_sync_cache_range>
  4006dc:       00000000        .inst   0x00000000 ; undefined

00000000004006e0 <__aarch64_sync_cache_range>:
  4006e0:       b0000403        adrp    x3, 481000 <__TMC_END__+0x5f0>
  4006e4:       b94a5862        ldr     w2, [x3, #2648]
  4006e8:       35000082        cbnz    w2, 4006f8 <__aarch64_sync_cache_range+0x18>
  4006ec:       d53b0024        mrs     x4, ctr_el0
  4006f0:       2a0403e2        mov     w2, w4
  4006f4:       b90a5864        str     w4, [x3, #2648]
  4006f8:       d3504c44        ubfx    x4, x2, #16, #4
  4006fc:       52800083        mov     w3, #0x4                        // #4
  400700:       12000c45        and     w5, w2, #0xf
  400704:       1ac42064        lsl     w4, w3, w4
  400708:       51000482        sub     w2, w4, #0x1
  40070c:       8a220002        bic     x2, x0, x2
  400710:       1ac52063        lsl     w3, w3, w5
  400714:       eb01005f        cmp     x2, x1
  400718:       540000c2        b.cs    400730 <__aarch64_sync_cache_range+0x50>  // b.hs, b.nlast
  40071c:       93407c84        sxtw    x4, w4
  400720:       d50b7b22        dc      cvau, x2
  400724:       8b040042        add     x2, x2, x4
  400728:       eb02003f        cmp     x1, x2
  40072c:       54ffffa8        b.hi    400720 <__aarch64_sync_cache_range+0x40>  // b.pmore
  400730:       d5033b9f        dsb     ish
  400734:       51000462        sub     w2, w3, #0x1
  400738:       8a220000        bic     x0, x0, x2
  40073c:       eb00003f        cmp     x1, x0
  400740:       540000c9        b.ls    400758 <__aarch64_sync_cache_range+0x78>  // b.plast
  400744:       93407c62        sxtw    x2, w3
  400748:       d50b7520        ic      ivau, x0
  40074c:       8b020000        add     x0, x0, x2
  400750:       eb00003f        cmp     x1, x0
  400754:       54ffffa8        b.hi    400748 <__aarch64_sync_cache_range+0x68>  // b.pmore
  400758:       d5033b9f        dsb     ish
  40075c:       d5033fdf        isb
  400760:       d65f03c0        ret
  400764:       00000000        .inst   0x00000000 ; undefined
```
ARMv8's `dc cvau` and `ic ivau` are explained in [doc](https://cs140e.sergio.bz/docs/ARMv8-A-Programmer-Guide.pdf).
These two instructions flush the content to PoU, which is the unified L2 cache.

## Confirmed! No L2 in rocket-core generator.

See [link](https://github.com/chipsalliance/rocket-chip/issues/653):
```
Unfortunately, we never had a production-ready L2. Also, the previous L2 spoke a now obsolete version of TileLink. At present there is no L2 in the repository. :-(
```

## Petalinux kernel source
[Xilinx forum](https://forums.xilinx.com/t5/Embedded-Linux/Petalinux-where-can-I-find-the-kernel-sources-in-a-Petalinux/td-p/811189) shows the kernel source is located in `<plnx-proj-root>/build/tmp/work-shared/plnx_aarch64/kernel-source`.
But, you must set the flag `RM_WORK_EXCLUDE += "linux-xlnx"` in config file located in `<plnx-proj-root>/project-spec/meta-user/conf/petalinuxbsp.conf`. This is useful for compiling kernel modules, which can use kernel functions, such as cache flush.

## Kernel module programming for measuring cycles
Tutorial is given by [Intel](https://www.intel.com/content/dam/www/public/us/en/documents/white-papers/ia-32-ia-64-benchmark-code-execution-paper.pdf), which relies on kernel module programming.

## Add PERF and taskset to ARM Linux
Adding PERF is explained in the [post](https://forums.xilinx.com/t5/Embedded-Linux/added-perf-component-not-found-in-rootfs-from-qemu-boot/td-p/851596).

Taskset.
