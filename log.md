### Cache flush
L1 and L2 cache flush in ARM (dcsw_flush_level1/2):
Discussion in https://community.arm.com/developer/ip-products/processors/f/cortex-a-forum/7271/is-it-necessary-for-arm-v8-soc-to-flush-l2-cache-to-dram
Code in https://github.com/ARM-software/arm-trusted-firmware/blob/620d9832f96ffcaf86d38b703ca913438d6eea7c/lib/cpus/aarch64/cortex_a57.S#L561

ARM's ATF (do_dcsw_op macro): https://github.com/ARM-software/arm-trusted-firmware/blob/620d9832f96ffcaf86d38b703ca913438d6eea7c/lib/aarch64/cache_helpers.S#L90

The first input to do_dcsw_op is "x0: The operation type (0-2), as defined in arch.h" defined in:
https://github.com/ARM-software/arm-trusted-firmware/blob/620d9832f96ffcaf86d38b703ca913438d6eea7c/include/arch/aarch64/arch.h#L119

dc instruction is used for cache op = cisw on specified level

#### 16.66 DC
Data Cache operation.

This instruction is an alias of SYS.

The equivalent instruction is SYS #op1, C7, Cm, #op2, Xt.

Syntax
DC <dc_op>, Xt

Where:

<dc_op>
Is a DC instruction name, as listed for the DC system instruction group, and can be one of the values shown in Usage.
op1
Is a 3-bit unsigned immediate, in the range 0 to 7.
Cm
Is a name Cm, with m in the range 0 to 15.
op2
Is a 3-bit unsigned immediate, in the range 0 to 7.
Xt
Is the 64-bit name of the general-purpose source register.
