---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: 内存基础介绍
canvasWidth: 1920
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# open graph
seoMeta:
  # By default, Slidev will use ./og-image.png if it exists,
  # or generate one from the first slide if not found.
  ogImage: auto
  # ogImage: https://cover.sli.dev
---

# 内存基础介绍

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---

<toc />

---

## 硬件位置

大部分的电脑会有4个插槽, 1,3为1个通道, 2,4为另外一个通道. 优先插入2,4位置. 并且保证内存容量一致.

<div grid="~ cols-2">

![](/logo.png){width=100%}  

![](/logo.png){width=100%}

</div>
---

## 通道信息
双通道,每个通道2个channel

<div grid="~ cols-2 gap-4">

![](/logo.png)

![](/logo.png)

</div>

<v-drag-arrow style="color: red" two-way pos="521,411,276,-64"/>

<v-drag-arrow style="color: red" two-way pos="516,356,276,-64"/>

<v-drag-arrow style="color: red" two-way pos="405,274,276,-64"/>

<v-drag text-3xl pos="833,242,216,66" style="color: red">
    7根控制线
</v-drag>

<v-drag text-3xl pos="669,170,240,66" style="color: red">
    32根数据线
</v-drag>

<v-drag text-3xl pos="618,390,240,66" style="color: red">
    21根地址线
</v-drag>

---
layout: two-cols-header
---

## 层次结构

2G颗粒 = 8 Bank Group * 256M = 32 Bank * 64M = 32 Bank * 65535行 * 8KiB(8192列)(实际上一个bank会由8个array构成,他们同时读写同一行的同一列)

::left::
![](/logo.png){width=60%}

外部封装-DRAM-互联矩阵x2-球栅阵列

![](/logo.png){width=60%}

::right::


<img src="/logo.png" style="width: 60%"/>

![](/logo.png){width=60%}

<p>
2**16行, 2**11
</p>

---
layout: center
---

![](/logo.png){width=90%}

---

## DRAM 和 SRAM

![](/logo.png)

||SRAM|DRAM|
|---|----|----|
|结构|复杂|简单|
|数据保持时间|永久|64ms|
|读写速度|瞬时|需要等待电容充满|
|用处|L1 L2 cache|内存颗粒|

---

## SRAM
把输入用非门变成0,1,或者1,0 作为 RS触发器的输入

<div grid="~ cols-2">

![](/logo.png)

* 当保持位为0时,不管输入什么,输出都不变
* 当保持位位1时,输出变为输入后不再改变

</div>

<div grid="~ cols-2">

![](/logo.png)

![](/logo.png)

</div>


---
layout: two-cols-header
---

## DRAM

::left::

![](/logo.png)

::right::

* MOS管控制是否激活
* 电容保存数据(高电压为1)

![](/logo.png)

---
layout: two-cols-header
---

## 双层电路设计

::left::
![](/logo.png)


::right::
![](/logo.png)

![](/logo.png)


---

## 单比特的读取方式

由于电容的电量很小, 所以需要外接一个感应放大器

<div grid="~ cols-2">


<div>

![](/logo.png){width=90%}

1. 如果是读,就把感应放大器充电到0.5V,  
让感应放大器根据电容的相应变成1或者0
2. 接通电源,电容根据保存的数值给感应放大器充电或者放电

</div>


<div>

![](/logo.png){width=80%}

3. 感应放大器读取后反向给电容充电
4. 如果是写, 直接把感应放大器和数据相连,  
电压设置成1或者0即可

</div>

</div>


---
layout: two-cols-header
---

## 实际应用


::left::



![](/logo.png){width=90%}

![](/logo.png)


::right::

![](/logo.png)

1. 第一次传输 bankgroup\[0:3\], bank\[4:5\], row\[6:21\]
2. 激活行, 把感应放大器充电到0.5V
3. 第二次传输 column\[22:32\]


---
layout: two-cols-header
---

## 行和列的选择

![](/logo.png)

---
layout: two-cols-header
---

## 行和列选择的逻辑图

::left::
![](/logo.png)

上图: 行选择器, 根据行地址, 激活对应的行  
右图: 列选择器, 根据列地址输出对应的比特  

::right::
![](/logo.png)

---


## 内存时序

如果是连续地址的访问, 就可以避免重复激活行, 节约感应放大器充电和行地址发送时间

![](/logo.png){width=93%}

---
layout: two-cols-header
---

## 内存分配

内存的物理地址直接使用有什么问题

::left::

* 直接使用物理地址, 导致地址无法复用

![](/logo.png)

::right::

* 直接使用内存地址, 任何一个程序能直接修改其他程序的变量, 存在安全问题

![](/logo.png)

---

## Page Table

我们让每个程序使用完整的虚拟地址对应的内存. 当程序真正访问某段虚拟内存时, 我们才申请真正的物理内存提供给这个程序.

比如虚拟地址32bit, 其中12bit当作offset维持不变, 剩下的20bit映射到18bit的物理内存地址.

![](/pagetable.png){width=130%}

---

## page faults

当程序访问的内存不再内存里时,抛出page faults, 由DMA把磁盘内的数据搬移到内存, 同时CPU处理其他进程(15~50us)  

![](/pagefaults.png){width=90%}

---

## 多级Page Table

物理地址32bit, 其中12bit(4K页表)当作offset维持不变, 剩下的20bit需要保存到entry  
pagetable的entry数: 1M(2\*\*20), 每个entry: 如果是4字节(地址加标志位),就需要4M  
如果服务器有500个进程,就需要2G内存空间.  

<div grid="~ cols-3 gap-4">

<div col-span-2>

![](/二级页表.png){width=100%}

</div>

<div>

我们把32bit分成10bit(一级页表索引) + 10bit(二级页表索引) + 12bit(offset)  

这样一个程序最开始只需要载入一级页表就可以正常运行. 

当访问地址时, 根据前10bit查找知道二级页表的内存地址. 再根据后20bit查找到内存页表的位置

除了一级页表, 其他的次级页表都允许被swap出去, 这样一个程序就能只占用4K内存了

> linux下一般用5级页表

</div>

</div>

---

## 参考资料

* DRAM和SRAM的区别: https://www.youtube.com/watch?v=5xej9bNg5nE
* 内存物理原理: https://www.youtube.com/watch?v=7J7X7aZvMXQ
* 虚拟内存概念: https://www.youtube.com/watch?v=A9WLYbE0p-I
* 一个bank对应多个array: https://www.youtube.com/watch?v=Mhqi70OPW0o
* 多级页表: https://www.youtube.com/watch?v=Z4kSOv49GNc
* 高性能计算机架构: https://www.youtube.com/watch?v=mMHkIa6Lkek

---
