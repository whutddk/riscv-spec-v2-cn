### 1.2 指令长度编码

基本 RISC-V ISA 是定长的 32-bit 的指令，自然地必须在 32-bit 边界对齐。可是，标准的 RISC-V 编码方案被设计来支持变长指令的 ISA 拓展（每条指令在长度上可以是任何数量个 16-bit 指令包，并且包自然地在 16-bit 的边界上对齐）。第 13 章描述的标准的压缩 ISA 拓展通过提供压缩的 16-bit 的指令来缩短代码长度，放宽对齐的约束条件来允许所有的指令（16-bit 和 32-bit）能够在任意 16-bit 的边界上对齐来提高代码密度。

图 1.1 阐明了标准 RISC-V 指令长度的编码约定。在基本 ISA 中，所有 32-bit 的指令的最低两位都被设置成 11。可选的
16-bit 的压缩指令集拓展的最低两位都被设置成 00，01，或者 10。编码超过 32 位的标准指令集拓展有额外的低序位被设置为1，图 1.1 中所展示的 48-bit 和 64-bit 长度的指令就遵循这个约定。指令长度在 80 位和 304 位之间的指令被编码使用一个 4-bit 的字段来给出除了前 5×16 位字以外的 16-bit 字的数量。用 11 或者更多低位的操作码位设置为 1 的编码格式会为未来更长的指令编码保留。

> 给定代码尺寸以及一种压缩格式的节能，我们想要去构建对一种压缩格式支持的 ISA 编码方案，而不是作为一种事后想法增加它，但是未来允许更简单的实现，我们不想把压缩格式弄成强制的。我们也想可选地允许更长的指令去支持实验和大规模的指令集拓展。虽然我们的编码约定需要一个更紧致的核心 RISC-V ISA 的编码，但是这有很多有益的影响。
一个标准 G ISA 的实现只需要保存最高 30 位的有效位在指令缓冲器（节省了6.25%的空间）。当指令缓冲器再填满的时候，任何的指令（低位被清除）在存储到缓存器中之前都应该被重新编码成合法的 30-bit 的指令来防止非法的指令自陷行为。
    或许更重要的是通过压缩我们的基本 ISA 成一个 32-bit 指令字的子集，我们可以预留更多可获得的空间给定制的拓展。特别是基本的 RV32I ISA 在 32-bit 的指令字中使用少于 1/8 的编码空间。和在第 9 章中描述的一样，一个保留对大于等于 32-bit 的标准指令集拓展的支持，但是不需要支持标准压缩指令拓展的实现可以映射 3 个额外的 30-bit 的指令空间到 32-bit 定长的指令编码格式中。进一步来说，假如这个实现也不需要长度大于 32-bit 的指令的话，那么它能够保留更多的四个主要的操作码。

> 我们把它（任何长度的全零指令都是不合法的，因为这会迅速地造成错误的跳转到零位存储器区域）看做一个特点。

虽然基本 RISC-V ISA 有一个小头优先的存储器系统，但是不标准的变体可以提供一个大头优先或者双端存储器系统。指令根据实现的物理字节顺序被存储在存储器中，并且每 16-bit 的包被存储在一个存储器半字中。包含一个指令的包被存储在增加的半字地址中，最低地址的包保存在指令规范中的最低位，例如，指令总是以一个小头优先的包顺序存储，而不去考虑存储器系统的字节顺序。在图 1.2 中的代码序列将正确地存储一个 32-bit 的指令到存储器中，而不去考虑存储器系统的字节顺序。

> 我们给 RISC-V 的存储器系统选择小头优先的字节顺序，是因为小头优先的系统目前在商业上是占有优势的（所有 x86 系统；针对 ARM 的 iOS, Android,和 Windows）。一个次要的原因是我们也已经发现小头优先的存储器系统对于硬件设计师来说是更自然的。可是，例如 IP 网络等特定应用领域使用大头优先的数据结构，并且我们也留开放的不标准的大头优先或者双端系统的可能性。
我们不得不去固定字节顺序（包被存储在存储器中，不去考虑存储器系统的字节顺序）去确保长度的编码位总是在半字地址序的最前面。这使得变长指令的长度能够快速地被取指部件通过只检查第一个 16-bit 指令包的最前面几位断定。一旦我们已经决定了固定采用一个小头优先的存储系统和指令包顺序，那么自然而然地会将长度的编码位放在指令格式的 LSB 的位置来避免分解操作码字段。

