---
title: 操作系统 - 中断
date: 2021-03-02 12:44:28
categories: 
- OS
- 中断
tags: [OS,中断]
keywords: [OS,中断]
---

## 中断

设备发出通知给CPU，CPU接收到中断信息后，会马上向OS反馈中断信号的到来，然后由OS处理中断信号。

## 软中断和硬中断

1. 硬中断是由硬件设备的中断控制器产生的电信号，软中断是由指令直接给出的；
2. 硬中断可屏蔽，软中断不可屏蔽
3. 硬中断来自外部事件，具有突发性和随机性；软中断是由程序安排好产生的。

## 中断处理程序

在相应特定中断时，内核会执行中断处理程序；例如一个设备的中断处理程序就是它的驱动的一部分，它需要通知设备中断信号已经被接收，同时还需要完成一些紧急任务（比如将网卡中的数据包拷贝到内存）。

## 上半部和下半部

由上述可知，中断处理的一些任务比较紧急，所以中断处理程序需要很快运行；但是同样地我们也希望中断处理程序完成更多的工作量。所以将中断能处理分为上半部和下半部。

- 中断处理程序一步执行，可能会打断其他重要代码，为避免打断的代码停止时间过长，因此需要执行得越快越好；
- 中断处理过程中其他同级中断会被屏蔽；
- 硬件处理的时限要求
- 上半部不能被打断，下半部可以被打断。

上半部只执行一些紧急的、有严格时限的工作；能被延后的工作推迟到下半部中。

例如网卡的中断，复制数据包到内存是上半部的工作，处理数据包等在下半部进行。