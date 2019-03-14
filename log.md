### Cache flush
L1 and L2 cache flush in ARM (dcsw_flush_level1/2):
https://community.arm.com/developer/ip-products/processors/f/cortex-a-forum/7271/is-it-necessary-for-arm-v8-soc-to-flush-l2-cache-to-dram

ARM's ATF (do_dcsw_op macro):
https://github.com/ARM-software/arm-trusted-firmware/blob/master/lib/aarch64/cache_helpers.S
