# 我们这个周末将这个做完！ 今天争取通过一些test

通过test的过程：

* No_L2 Cache 的test
* No prefech L1, L2
* +1 prefetch L2 使用 MIP insert
* +1 prefetch L2 使用 LIP insert
* 最后实现 LFU和strides prefetch



截止下午四点二十：

- [ ] lfu_short_gcc
- [ ] lfu_short_leela
- [ ] lfu_short_linpack
- [ ] lfu_short_matmul_naive
- [ ] lfu_short_matmul_tiled
- [ ] lfu_short_mcf
- [ ] mip_lfu_short_gcc
- [ ] mip_plus_one_pref_short_gcc
- [x] nol2_l1_dm_short_gcc
- [x] nol2_l1_fa_short_gcc
- [x] nol2_l1_sa_short_gcc
- [ ] nopref_short_gcc
- [ ] plus_one_pref_short_gcc
- [ ] plus_one_pref_short_leela
- [ ] plus_one_pref_short_linpack
- [ ] plus_one_pref_short_matmul_naive
- [ ] plus_one_pref_short_matmul_tiled
- [ ] plus_one_pref_short_mcf

截止晚上7.25

- [ ] lfu_short_gcc
- [ ] lfu_short_leela
- [ ] lfu_short_linpack
- [ ] lfu_short_matmul_naive
- [ ] lfu_short_matmul_tiled
- [ ] lfu_short_mcf
- [ ] mip_lfu_short_gcc
- [ ] mip_plus_one_pref_short_gcc
- [x] nol2_l1_dm_short_gcc
- [x] nol2_l1_fa_short_gcc
- [x] nol2_l1_sa_short_gcc
- [x] nopref_short_gcc
- [ ] plus_one_pref_short_gcc
- [ ] plus_one_pref_short_leela
- [ ] plus_one_pref_short_linpack
- [ ] plus_one_pref_short_matmul_naive
- [ ] plus_one_pref_short_matmul_tiled
- [ ] plus_one_pref_short_mcf

截止晚上7.44

- [ ] lfu_short_gcc
- [ ] lfu_short_leela
- [ ] lfu_short_linpack
- [ ] lfu_short_matmul_naive
- [ ] lfu_short_matmul_tiled
- [ ] lfu_short_mcf
- [ ] mip_lfu_short_gcc
- [x] mip_plus_one_pref_short_gcc
- [x] nol2_l1_dm_short_gcc
- [x] nol2_l1_fa_short_gcc
- [x] nol2_l1_sa_short_gcc
- [x] nopref_short_gcc
- [ ] plus_one_pref_short_gcc
- [ ] plus_one_pref_short_leela
- [ ] plus_one_pref_short_linpack
- [ ] plus_one_pref_short_matmul_naive
- [ ] plus_one_pref_short_matmul_tiled
- [ ] plus_one_pref_short_mcf

截止20.05:

- [ ] lfu_short_gcc
- [ ] lfu_short_leela
- [ ] lfu_short_linpack
- [ ] lfu_short_matmul_naive
- [ ] lfu_short_matmul_tiled
- [ ] lfu_short_mcf
- [ ] mip_lfu_short_gcc
- [x] mip_plus_one_pref_short_gcc
- [x] nol2_l1_dm_short_gcc
- [x] nol2_l1_fa_short_gcc
- [x] nol2_l1_sa_short_gcc
- [x] nopref_short_gcc
- [x] plus_one_pref_short_gcc
- [x] plus_one_pref_short_leela
- [x] plus_one_pref_short_linpack
- [x] plus_one_pref_short_matmul_naive
- [x] plus_one_pref_short_matmul_tiled
- [x] plus_one_pref_short_mcf



debug 两个小时 发现Cache write多加了一行。而且以为我重定向了cout和printf，segmentation fault和输出的内容不匹配。不能通过output file找到segmentation fault的位置。

但是更加了解了gdb的debug过程。

例如gdb的traceback（tb），conditional break：

break function if variable == 12345

例子：b cachesim_cache.cpp:Cache::Read(unsigned long, sim_stats*) if this->Time == 21149

而且gdb遇到segmentation fault会停止在发生segmentation fault的函数位置。

<img src="/Users/pb/Documents/My-study-notes/pictures/image-20240217130240369.png" alt="image-20240217130240369" style="zoom:33%;" />

这里gdb直接发现了是在Write函数中的236行发生segmentation fault.

但gdb这里只保留了一些信息（可能只有当前函数栈里的信息）

是不能运行的，只能通过print查看当时发生segmentation fault之前的variable信息。

但是这样要解决segmentation fault会容易很多。其实就不需要用conditional breakpoint了。



截止13.00

- [x] lfu_short_gcc
- [x] lfu_short_leela
- [x] lfu_short_linpack
- [x] lfu_short_matmul_naive
- [x] lfu_short_matmul_tiled
- [x] lfu_short_mcf
- [x] mip_lfu_short_gcc
- [x] mip_plus_one_pref_short_gcc
- [x] nol2_l1_dm_short_gcc
- [x] nol2_l1_fa_short_gcc
- [x] nol2_l1_sa_short_gcc
- [x] nopref_short_gcc
- [x] plus_one_pref_short_gcc
- [x] plus_one_pref_short_leela
- [x] plus_one_pref_short_linpack
- [x] plus_one_pref_short_matmul_naive
- [x] plus_one_pref_short_matmul_tiled
- [x] plus_one_pref_short_mcf





结果交上去不一样

- [ ] lfu_gcc.out
- [ ] lfu_leela.out
- [ ] lfu_linpack.out
- [ ] lfu_matmul_naive.out
- [ ] lfu_matmul_tiled.out
- [ ] lfu_mcf.out
- [ ] mip_lfu_gcc.out
- [ ] mip_plus_one_pref_gcc.out
- [ ] nol2_l1_dm_gcc.out
- [ ] nol2_l1_fa_gcc.out
- [ ] nol2_l1_sa_gcc.out
- [ ] nopref_gcc.out
- [ ] plus_one_pref_gcc.out
- [ ] plus_one_pref_leela.out
- [ ] plus_one_pref_linpack.out
- [ ] plus_one_pref_matmul_naive.out
- [ ] plus_one_pref_matmul_tiled.out
- [ ] plus_one_pref_mcf.out
- [ ] stride_gcc.out

原来是default config不能改，而且我交上去的代码有printf重定向。









