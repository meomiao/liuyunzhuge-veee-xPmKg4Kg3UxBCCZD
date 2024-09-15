
在软件开发领域，优化性能和简化效率仍然至关重要。多年来，.NET 平台一直在创新，为开发人员提供基础设施，以打造弹性和高效的软件解决方案。今天要写的这篇文章源自昨天在朋友圈发的文章《[UWP 通过 .NET 9 和Native AOT 的支持实现 UWP 应用的现代化](https://github.com)》\[1],一位小伙伴的对话让我想全面梳理下Native AOT的现在的进展。

[![image](https://img2023.cnblogs.com/blog/510/202409/510-20240915100939860-832061451.png "image")](https://github.com)

#### 什么是 .NET Native AOT？

.NET Native Ahead\-of\-Time （AOT） 编译是 .NET 平台中的一项前沿进步。使用 AOT 时，C\# 代码在开发人员计算机上被编译为本机代码。这与在运行时将代码编译为本机代码的传统方法形成鲜明对比。

下面的架构说明了这一点。.NET 传统编译涉及两个步骤：

1. C\# 编译生成包含**中间语言 （IL）** 代码的 DLL 文件。此类 DLL 称为 **.NET 程序集**。
2. 执行 .NET 程序时，**.NET 运行时**（**CLR 公共语言运行时**）将加载 .NET 程序集。CLR 的子系统负责将 IL 代码编译为由 CPU 直接执行的本机代码。此子系统称为 **JIT （Just\-In\-Time） 编译器**。它之所以得名，是因为它仅在首次调用该方法时编译该方法的 IL 代码。

另一方面，.NET Native AOT 编译由一个步骤组成。**将 C\# 源代码编译为开发人员计算机上的本机代码**。此过程包括将 C\# 代码转换为 IL 代码，然后转换为 Native 代码，形成一个两步编译过程。但这是一个实现细节。这就是 AOT .NET 程序集 框在下面的架构中为灰色的原因。

[![image](https://img2023.cnblogs.com/blog/510/202409/510-20240915102606909-224733204.png "image")](https://github.com)

#### .NET Native AOT 的优势

.NET Native Ahead\-of\-Time （AOT） 编译带来了一系列优势：

* **增强的性能：**通过将代码预编译为本机计算机指令，.NET Native AOT 显著**缩短了启动时间**并提高了应用程序的整体性能。在**无服务器**方案中，如果应用程序针对每个请求启动，这可能会产生重大差异。此外，运行时没有 JIT 编译开销，这意味着执行速度更快，从而提供更流畅的用户体验。
* **简化部署：**AOT 编译的应用程序通常会导致**依赖项为零或较少的独立可执行文件**。这简化了部署过程，可以更轻松地在各种平台和设备之间分发应用程序，而无需额外的安装或运行时组件。
* **更小的应用程序大小：**通过**修剪**不必要的代码，AOT 可以大大减小应用程序的大小。这不仅可以节省存储空间，还可以优化应用程序的内存占用，这在移动设备或 IoT 设备等资源受限的环境中尤为重要。
* **增强的知识产权保护：**AOT 编译将源代码转换为优化的机器代码，这使得**逆向工程尝试更具挑战性**。生成的本机代码比 IL 代码更加模糊，并且难以破译，因为 IL 代码可以轻松反编译为原始 C\# 代码。这增强了应用程序中嵌入的敏感算法、业务逻辑和专有方法的安全性。

#### .NET Native AOT 的缺点

使用 AOT 获得的好处不可避免地伴随着某些缺点。他们是：

* **特定于平台的编译：**.NET Native AOT 生成特定于平台的本机代码，针对特定体系结构或操作系统进行定制。例如，与常规 .NET 程序集不同，在 Windows 上使用 AOT 生成的可执行文件在 Linux 上不起作用。
* **不支持跨 OS 编译**。例如，在 Windows 机器上，您无法编译 Linux 本机版本，反之亦然。
* **对 Reflection 的部分支持：**反射依赖于动态代码生成和运行时类型发现，这与 AOT 编译代码的预编译和静态性质相冲突。但是，通常的 Reflection 用法与 AOT 配合得很好。
* **需要 AOT 兼容的依赖项：**AOT 编译要求项目中使用的所有库和依赖项都与 AOT 兼容。依赖于反射、运行时代码生成或其他动态行为的库可能与 AOT 不兼容，这可能会导致冲突或运行时错误。
* **增加构建时间：**AOT 编译涉及在构建过程中预先生成本机代码。这个额外的步骤会显著增加构建时间，特别是对于大型项目或具有大量代码库的应用程序。
* **需要适用于 C\+\+ 的桌面开发工具：**AOT 只能在安装这些工具的情况下进行编译，这些工具在您的硬盘驱动器上最多可重 7GB。

.NET 9 的 Native AOT（Ahead\-of\-Time Compilation）是微软在.NET 9版本中重点发展的一项技术，旨在提升应用程序的性能。Native AOT 技术可以将.NET 代码预先编译为本地代码，从而实现更快的启动时间和更高的执行效率。

[在.NET 9中，微软已经将Native AOT作为提升性能的关键点之一](https://github.com)\[2]。此外，微软还宣布了对通用Windows平台（UWP）的初步支持，允许开发者使用.NET 9和Native AOT技术来现代化改造现有的UWP应用。这一举措为UWP开发者提供了一条升级路径，使他们能够利用最新的.NET和Native AOT技术来改进其应用程序。 .NET 9的Native AOT不仅限于UWP平台，它还支持老旧的Windows 7和XP环境，这标志着AOT技术在兼容性方面的突破。然而，对于Android平台的Native AOT支持，目前尚未完成，尤其是JNI（Java Native Interface）支持，这被认为是一个较大的功能需求，还有WPF/Winform 的Native AOT支持也需要在.NET 10 才能够完成。

#### .NET 的Native AOT技术通过在编译阶段对代码进行优化，使得生成的可执行文件更小且启动速度更快。这一突破性功能不仅实现了对老旧Windows 7和Windows XP环境的支持，还为性能要求高且依赖旧版系统的开发者提供了新的可能性。具体来说，.NET 9版本中，为了确保向下兼容性，X86架构下的AOT（Ahead\-of\-Time）编译器的支持扩展，.NET 9采用了精心设计的编译策略，确保了对Win7及XP API的兼容性，使代码能够无缝运行，允许开发人员将应用程序在编译阶段就优化为能够在老旧的Windows系统上运行。LoongArch架构和Risc\-V架构下的AOT 编译器支持，社区也在继续完善之中。

在.NET 9中，对Native AOT（按需编译）支持和JNI（Java Native Interface）的支持有以下具体进展：

* 在.NET 9中，引入了两个新的属性，允许开发者设计功能开关。这些功能开关可以在.NET库（以及你自己）中使用来切换某些功能区域。如果一个功能不被支持，在裁剪或使用Native AOT进行编译时，将移除那些不受支持且不必要的功能，从而减小应用程序的大小。
* 在.NET MAUI的测试中，通过调用JNI来获取Java数组元素的性能比使用string.Split和新的Span方法更差。这表明开发者正在考虑如何在未来版本中优化这一过程。
* .NET 9在Android平台上对Native AOT的支持主要体现在通过新属性实现的功能开关，以及通过Native AOT减少应用大小的能力。

对于开发者而言，学习和采用.NET 9的Native AOT技术需要具备以下前置知识或技能：

1. **对.NET平台的理解**：首先，开发者需要对.NET平台有基本的了解，包括其架构、运行时环境以及如何在不同平台上部署应用。这有助于理解Native AOT技术如何与现有.NET生态系统集成。
2. **熟悉C\#或F\#编程语言**：由于.NET 9支持通过C\#或F\#进行开发，因此掌握这些编程语言是必要的。此外，了解这些语言的高级特性将有助于更有效地利用Native AOT带来的性能优势。
3. **了解编译器原理**：Native AOT（Native Application Optimized Translation）是一种预编译技术，它允许开发者直接生成机器码而不是依赖JIT（即时编译）。因此，对编译器的工作原理有一定的了解可以帮助开发者更好地理解和使用[Native AOT技术](https://github.com):[FlowerCloud机场](https://hushicha.org)\[3]。
4. **性能优化经验**：由于Native AOT旨在提供可预测的性能并减少资源消耗，因此具备一定的性能优化经验是有益的。这包括对内存管理、代码优化等方面的深入了解。
5. **云原生和微服务架构知识**：虽然不是必须的，但了解云原生应用和微服务架构的相关知识可以增强开发者在使用.NET 9时构建高效、可扩展的应用的能力。这是因为.NET 9特别强调了在这些架构中的性能表现，参见<https://github.com/shanyou/p/18015105>
6. \[3]Native AOT技术指南： [https://github.com/hez2010/p/17999838/guidance\-for\-dotnet\-nativeaot](https://github.com/hez2010/p/17999838/guidance-for-dotnet-nativeaot "https://github.com/hez2010/p/17999838/guidance-for-dotnet-nativeaot")
7. \[4].NET 9 RC1发布: [https://mp.weixin.qq.com/s/WabCosc39FlI3Wrylf7CbQ](https://mp.weixin.qq.com/s/WabCosc39FlI3Wrylf7CbQ "https://mp.weixin.qq.com/s/WabCosc39FlI3Wrylf7CbQ")
