##第一章 绪论
RISC-V（被发音为“risk-five”）是一个新的指令系统结构（ISA），它最初被设计用来支持计算机体系结构的研究和教育，但是现在我们希望它成为一个对于工业实现来说标准的开源体系结构。RISC-V的目标定义包括:
* 一个学术界和工业界都可以完全免费获得的开源的指令集；
* 一个真正适用于本地硬件实现，而不仅仅适用于仿真或者二进制翻译的指令集；
* 避免了特定的微体系结构设计风格（例如，微码设计，按序，去耦，乱序）或实现技术（例如，全定制，特定应用集成电路，现场可编程门阵列）的“过度设计”，但是允许任何设计风格或者实现技术有效的实现；
* 分为针对定制的加速器或者教育目的的基集是非常有用的基本整型指令集以及用以支持通用软件发展可选的标准拓展集两部分；
* 支持2008年修订的IEEE-754的浮点标准；
* 支持大量用户级指令集拓展以及特定的变体；
* 具有针对32位或64位地址空间的应用、操作系统内核以及硬件实现的变体；
* 支持高并行性的多核实现，包括异构的多核处理器；
* 支持可选的变长集指令拓展，并且支持针对高性能、代码尺寸或者能效的可选的密集指令编码格式；
* 一个完全虚拟化的指令集，使管理程序的开发变得更容易；
* 简化了新的管理层和超级管理层的指令集的设计。
> 在我们的设计方案里的注释将被格式化为这一段一样，并且能够被跳过，假如读者只感兴趣于规格本身。
