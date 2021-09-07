# csapp个人理解（更新中）



## 一、第三章 程序的机器级表示

call指令：

%rsp - 8；

将call指令的下一条指令压入栈中，



ret指令：

从栈中取出（call指令的下一条指令），存储到PC（%rip）中；

rsp+8；



push指令：

pushq  %rbp    将%rbp中存放的值压入栈中（%rsp-8）；



pop指令：

popq %rbp     将当前%rsp指向的位置的值压入%rbp中（%rsp+8）；



return-oriented programming （attack lab）

要查找的指令要以ret结尾 或以  nop（no operation）+  ret结尾

ex. c3  or  90 c3  etc  执行原理与ret指令原理一致



## 二、cache + virtual memory

![](D:\CS\csapp-\2.png)

!(D:\CS\csapp-\tlb hit.png)

![](D:\CS\csapp-\1.png)

TLB是一种cache，TLBT（tag）和TLBI（index）为VA的VPN部分



1.cpu产生一个虚拟地址，mmu从TLB中取出相应的PTE，  TLB hit；

A.如果PTE有效位是1，hit，mmu构造一个物理地址，根据系统的高速缓存的实现，物理地址被分成CT（标识位 tag）CI（index ，索引位）和CO（offset， 偏移位）;

如果cache hit，直接从cache返回数据给cpu；

如果cache miss， 从内存中读取数据并缓存到cache中（详见第六章）。

B.如果PTE有效位是0，触发page fault 异常， exception handler 把此虚拟地址映射到物理内存中，并更新PTE和TLB，返回到cpu，重新执行这条指令。此时，mmu再次从TLB中取出PTE，有效位是1，mmu构造pa，重复A过程。



2.1.cpu产生一个虚拟地址，TLB miss

mmu直接访问物理内存中的PTE，VPN作为PTE索引，

A.如果PTE中对应项有效位是1，将此PTE cache到TLB中，mmu构造PA，访问高速缓存；

如果cache hit，直接从cache返回数据给cpu；

如果cache miss， 从内存中读取数据并缓存到cache中

B.如果PTE中对应项有效位是0，触发page fault 异常,接下来与上述B过程一致。