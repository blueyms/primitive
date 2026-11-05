---
title: 半导体行业的 Primitive
subtitle: 从器件到芯粒，把万亿晶体管产业拆成可复用的积木
date: 2026-11-05
tags:
  - semiconductor
  - primitive
  - industry-analysis
source: >-
  Agent Primitives: Reusable Latent Building Blocks for Multi-Agent Systems
  (PMLR 306, 2026)
---

# 半导体行业的 Primitive

> **一句话结论：半导体产业是人类最彻底的原语化产业。** 复杂产品不是从零定制，而是由少量可复用的“原语”在器件、工艺、设计、封装、运营五个层级上反复组合；竞争力取决于原语库的深度、组合效率与接口标准化程度，而不是单点定制的数量。

## 0. 摘要

- **原语不等于“简单零件”**。原语的本质是把内部复杂度封装在稳定接口之后：内部可以千变万化，对外必须暴露同一套可被机器读取、版本化、可验证的契约。
- **半导体至少有六级原语栈**：L0 器件/物理、L1 单元工艺、L2 工艺模块、L3 设计/IP、L4 封装/系统、L5 运营/商业。上层原语向下展开为下层原语，形成嵌套复用。
- **三种组合模式反复出现**：迭代评审（DTCO/OPC/良率闭环）、多候选选择（冗余修复/分档/多方案）、规划与执行分离（设计→流片→制造→封测）。
- **接口决定复用上限**。PDK、GDSII/OASIS、Liberty/LEF、UCIe、SECS/GEM 等标准，等价于论文中的 KV cache：它们让状态在长流程中低损耗传递；邮件、口头经验和 PDF 规格书则是“自然语言通信”，会在多步交接中累积误差。
- **复用不是免费**。它把成本从“重新发明”转移到“集成、验证、接口维护和知识库建设”；一次错误组合的代价可能是数百万美元与数月周期。

## 1. 方法：借论文的一把尺子

论文《Agent Primitives》观察到：现有 LLM 多智能体系统高度任务定制、角色靠手工设计、自然语言通信在长上下文与噪声下快速退化。作者把复杂系统分解为少量反复出现的内部计算模式——**Review（迭代评审）、Voting and Selection（投票与选择）、Planning and Execution（规划与执行）**——每个原语对外暴露与标准 Agent 相同的接口，内部用 **KV cache** 传递隐状态，再由 **Organizer + Knowledge Pool** 自动选配与组合。

这套方法可以迁移到半导体，但要做两处修正：

1. **半导体原语受物理约束**。软件原语可以近乎零边际成本复制；半导体原语有材料、设备、良率、热力学与量子效应约束，组合必须经过硅验证。
2. **半导体原语不可逆**。软件可以回滚；硬件流片后只能改金属层、做 ECO 或重开掩模，代价随节点上升。因此半导体对“接口正确性”的要求比软件更苛刻。

在这个意义上，半导体原语可以定义为：

> **在某一抽象层级上不可再分、能被反复实例化、通过稳定接口与其他原语组合，并能把一次性的研发、验证与产能成本摊销到多个产品、节点或客户的最小功能单元。**

### 1.1 六条判据

| 判据 | 含义 | 检验问题 |
|---|---|---|
| 反复出现 | 跨产品、跨节点、跨厂重复出现 | 这个单元是否只为一个产品存在？ |
| 层级内最小 | 在本层不可再分 | 再拆下去，是否就变成更下层原语？ |
| 接口稳定 | 机器可读、版本化、可验证 | 换一个团队、供应商或工厂还能接上吗？ |
| 可组合 | 可串联、并联、层级化组合 | 组合后是否仍暴露标准视图？ |
| 可验证 | 有量测、测试或仿真手段 | 失效能否被检出并定位？ |
| 可摊销 | NRE、掩模、IP、recipe 可复用 | 复用次数能否覆盖原语建设成本？ |

前五条是技术判据，第六条是经济判据。**一个单元只有在技术上可复用、经济上值得复用时，才构成真正的原语。**

## 2. 六级原语栈

原语不是平的清单，而是嵌套的栈：**上层原语向下展开为下层原语，下层原语向上提供稳定视图**。例如，一颗先进 SoC 可以展开为 IP → 标准单元 → 器件 → 工艺模块 → 单元工艺；但在设计者眼中，它仍然只是一组带时序模型的 IP。

| 层级 | 名称 | 核心问题 | 典型原语 | 对外接口 | 复用单位 |
|---|---|---|---|---|---|
| L0 | 器件 / 物理 | 电子如何被约束与开关 | MOS/GAA/CFET、1T1C、3D NAND 存储单元、互连/通孔、MIM/MOM、STI | compact model、layer map、design rules | 单个器件或存储单元 |
| L1 | 单元工艺 | 晶圆表面状态如何变换 | 光刻、刻蚀、沉积、注入、CMP、清洗、氧化/退火、量测/检测 | recipe + spec + metrology | 一次工艺步骤 |
| L2 | 工艺模块 | 多个步骤如何集成为一个可复用模式 | HKMG、dual damascene、多重曝光、3D NAND 堆叠、DRAM 电容、背面供电、Si 光子 | module spec + PCM/WAT | 一个工艺模块 |
| L3 | 设计 / IP | 功能如何被封装成可调用单元 | 标准单元、SRAM、PLL/ADC、SerDes/DDR PHY、CPU/NPU 核、DFT | Liberty/LEF/GDSII、IP-XACT、AMBA | 一个单元或 IP |
| L4 | 封装 / 系统 | 多个裸片如何组合成系统 | bump、RDL、TSV、interposer、chiplet/UCIe、hybrid bonding、2.5D/3D、CPO | bump map、UCIe、thermal/mechanical rules | 一个封装平台或芯粒 |
| L5 | 运营 / 商业 | 研发与产能如何被组织与摊销 | lot、recipe、tool、test program、foundry/OSAT、MPW、PDK/IP license | MES/SECS-GEM、PDK license、wafer spec | 一条产线或一个商业模式 |

### L0 · 器件/物理原语

**它是什么**：决定电子行为的最小物理结构。器件原语回答“用什么开关、存什么电荷、怎么连线、怎么隔离”。

**典型原语**：planar/FinFET/GAA nanosheet/CFET 晶体管；DRAM 的 1T1C；3D NAND 的电荷陷阱存储单元；SRAM 的 6T/8T bitcell；Cu/Ru/Co 互连与通孔；MIM/MOM 电容；STI/深槽隔离；HKMG 栅堆叠；应变工程与硅化物。

**外部接口**：compact model（如 BSIM）、版图层映射、design rule、可靠性模型。设计者不需要知道器件的内部电场分布，只需要一组可仿真的电学参数与几何规则。

**复用方式**：同一器件结构跨多个 IP、多个产品、多个节点复用；器件是工艺与设计之间最基础的“共同语言”。

### L1 · 单元工艺原语

**它是什么**：一次对晶圆状态的变换操作。用函数式语言说，它是 `wafer_state → wafer_state` 的算子。

**典型原语**：光刻（涂胶/曝光/烘烤/显影，DUV 浸没、EUV、High-NA、LELE/SADP/SAQP）；刻蚀（RIE/ICP、深硅刻蚀、原子层刻蚀）；沉积（CVD/PECVD/ALD/PVD、外延、电镀）；离子注入与退火（RTP、激光退火）；CMP；清洗；热氧化/扩散；量测与缺陷检测（CD-SEM、OCD、椭偏、AFM）。

**外部接口**：recipe 的参数窗口、膜厚/线宽/电阻/缺陷等 spec、量测配方。一个工艺步骤可以被不同产品复用，前提是它的输入晶圆状态与输出 spec 被完整定义。

**复用方式**：recipe 跨产品、跨节点移植；同一设备平台通过换 recipe 承担多种原语。

### L2 · 工艺模块原语

**它是什么**：把多个单元工艺组织成一个反复出现的集成模式。它是半导体版的“内部计算图”。

**典型原语**：CMOS 前道（阱/栅/侧墙/源漏外延/硅化物）、中道接触、后道 dual damascene 铜互连、HKMG 模块、多重曝光模块、3D NAND 的交替层沉积/通道孔刻蚀/阶梯/ONO、DRAM 的高深宽比电容、背面供电（BSPDN）、Si 光子模块、先进封装模块。

**外部接口**：模块级 spec（厚度、CD、方阻、应力、缺陷密度）、PCM/WAT 测试结构、工艺窗口。模块内部可以有几十道工序，但对外只暴露“是否满足模块规格”。

**复用方式**：模块跨节点、跨产品、跨厂复用；先进节点的大量工作不是发明新模块，而是把既有模块缩放到更小尺寸并重新验证。

### L3 · 设计/IP 原语

**它是什么**：把电路功能封装成可被 EDA 工具调用、可被 SoC 集成的单元。

**典型原语**：标准单元（INV/NAND/NOR/AOI/DFF/clock gate）、SRAM/寄存器文件/ROM 宏、PLL/ADC/DAC/LDO/bandgap、DDR PHY/PCIe/CXL/SerDes/UCIe、CPU/GPU/NPU/ISP 核、时钟树与电源网格、DFT/扫描/BIST/JTAG、验证 IP 与形式化属性。

**外部接口**：Liberty（时序/功耗）、LEF（物理抽象）、GDSII/OASIS（版图）、Verilog/SPICE 模型、IP-XACT 元数据、AMBA/AXI/CHI 总线协议。**同一个 IP 的内部实现可以完全不同，但对外必须提供同一套视图**，否则无法被替换与组合。

**复用方式**：IP 复用是 SoC 降低 NRE 的主要杠杆；平台化（同一套 IP 衍生多个 SKU）进一步放大复用率。

### L4 · 封装/系统原语

**它是什么**：把一颗或多颗裸片组合成可交付系统的物理与电气原语。

**典型原语**：bump/Cu pillar/µbump、RDL、TSV、Si/有机 interposer、chiplet 与 die-to-die 接口（UCIe/BoW/AIB）、hybrid bonding、2.5D/3D 堆叠（CoWoS/InFO/EMIB/Foveros/SoIC）、fan-out、SiP/AiP、CPO 光引擎、TIM/散热盖。

**外部接口**：bump map、UCIe 协议与电气参数、热/机械设计规则、测试访问接口。封装的“接口”决定了哪些芯粒可以互换、哪些只能定制。

**复用方式**：封装平台复用（同一 substrate/underfill/assembly flow 服务多个产品）、芯粒库复用、已知良好裸片（KGD）复用。

### L5 · 运营/商业原语

**它是什么**：让上述物理原语在时间、产能、成本与质量上可被组织的管理原语。

**典型原语**：wafer lot、FOUP、reticle、recipe、route、process step、tool/chamber、test program、SPC/APC/FDC、OEE、WAT/PCM、CP/FT、binning、MPW shuttle、PDK/IP license、foundry/OSAT 合同。

**外部接口**：MES/SECS-GEM（设备与产线通信）、PDK 版本与 license、wafer spec、测试程序与数据格式。运营原语决定“同样的技术能否被稳定地、可重复地、按成本交付”。

**复用方式**：跨产品共用产线、测试程序、封装料号与质量体系；用 knowledge pool（良率库、历史 tape-out、DTCO 记录）减少重复试错。

## 3. 三种组合模式

论文的三个原语不是半导体产业的直接映射，而是三种**反复出现的功能模式**。把它们放在一起看，会发现半导体产业的大部分复杂流程都可以归约为这三类组合。

| 论文原语 | 半导体对应 | “求解者” | “评审者/选择者/执行者” | 停止条件 |
|---|---|---|---|---|
| Review | DTCO、OPC/ILT、良率提升闭环 | 设计/工艺方案 | 仿真、量测、缺陷检测、良率分析 | DRC/LVS clean、yield/reliability 达标 |
| Voting and Selection | 冗余修复、CP/FT 分档、多重曝光分解、双源 | 多颗 die、多套 recipe、多供应商 | tester、repair 算法、binning、STA corner | 修复率、良率、成本目标 |
| Planning and Execution | 设计→流片→制造→封测 | 架构/floorplan/process flow | 物理实现、fab、OSAT | tape-out、硅验证、 qualification |
| Organizer | 架构师 + PDK/EDA flow + 产品定义 | 产品需求 | 原语选型与排序 | PPA、可制造性、成本 |
| Knowledge Pool | IP 库、良率库、DTCO 库、历史 tape-out | 历史配置 | 检索与复用 | 复用率、ramp 速度 |

### 3.1 迭代评审：DTCO / OPC / 良率闭环

- **Solver**：设计团队给出初始版图、器件尺寸或工艺 recipe。
- **Critic**：DRC/LVS、光刻仿真、OPC/ILT retarget、量测与缺陷检测、良率分析。
- **反馈**：修改版图、调整 recipe、改变 retarget 规则、增加 SRAF/assist feature。
- **停止条件**：DRC/LVS clean、光刻窗口足够、良率达到目标、可靠性通过。

这条闭环在先进节点尤其重要：设计规则不是从天上掉下来的，而是设计与工艺反复“评审—修正”后的沉淀物。**DTCO 的本质，就是把 Review 原语制度化为跨团队、跨公司的迭代流程。**

### 3.2 多候选选择：冗余、分档与多方案

- **并行 Solver**：多颗 die、多套工艺 recipe、多种多重曝光分解方案、多个供应商/第二来源。
- **Selector**：CP/FT 测试、冗余修复算法、binning、STA 多 corner 签核、可靠性筛选。
- **聚合**：选出可用 die、把 die 分到不同性能档位、把掩模层分配给不同曝光方案。

DRAM/SRAM 的冗余行列修复、先进封装里的 KGD 筛选、以及多重曝光的 mask assignment，都是同一类“在多个候选中选择/聚合”的原语。**它不是“投票”这么简单，而是在物理约束与成本函数下做候选筛选。**

### 3.3 规划与执行分离：设计—制造—封测

- **Planner**：架构定义、floorplan、工艺 flow、封装方案、测试计划。
- **Executor**：物理实现、mask synthesis、fab 工艺执行、OSAT 封装、CP/FT 测试。
- **共享计划**：GDSII/OASIS + process flow + test program + BOM + 封装图纸。

这条链路的关键不是“分工”，而是**计划必须能被下游无损执行**。计划中的任何一个模糊点（层映射、单位、corner、封装 pin map），都会在流片后被放大为 re-spin。

### 3.4 Organizer 与 Knowledge Pool

在论文中，Organizer 不解决问题，只输出“原语组合计划”；Knowledge Pool 存储历史上有效的组合。半导体产业的对应物非常具体：

- **Organizer** = 产品架构师 + PDK/EDA flow + 设计方法学。它决定用哪些 IP、哪些工艺模块、哪种封装、按什么顺序集成。
- **Knowledge Pool** = IP 目录、标准单元库、良率数据库、DTCO 规则、历史 tape-out 配置、MPW 结果、失效分析库。

没有知识库的 Organizer 只能“重新发明流程”：每个项目重新选 IP、重新试 recipe、重新爬良率。**原语复用的上限，往往不是原语不够多，而是知识库不够可检索、接口不够稳定。**

```text
产品需求
   │
   ▼
┌──────────────────────────────┐      ┌─────────────────────────────┐
│ Organizer                    │◄────►│ Knowledge Pool              │
│ 架构师 / PDK / EDA flow      │      │ IP 库 / 良率库 / tape-out   │
└──────────────┬───────────────┘      └─────────────────────────────┘
               │  组合计划
               ▼
┌──────────────┬──────────────┬──────────────┐
│ Review       │ Voting       │ Planning     │
│ DTCO/OPC     │ 冗余/分档     │ 设计→制造     │
└──────────────┴──────────────┴──────────────┘
               │
               ▼
   GDSII + process flow + test program
```

## 4. 接口：半导体的 “KV cache”

论文用 KV cache 代替自然语言，是因为自然语言在长上下文、多阶段交互中会累积误差。半导体产业有一条几乎同构的规律：

> **原语的价值不取决于内部有多复杂，而取决于接口能否在长流程中低损耗地传递状态。**

| 论文中的机制 | 半导体中的对应物 | 为什么重要 |
|---|---|---|
| KV cache 隐状态传递 | PDK、GDSII/OASIS、Liberty/LEF、IP-XACT、UCIe、SECS/GEM | 避免在接口处反复“编码—解码”，减少信息损失 |
| 自然语言通信 | 邮件、会议纪要、PDF datasheet、口头经验 | 长流程中版本漂移、单位歧义、误差累积 |
| RoPE 位置重编码 | 层映射、坐标系、单位、PDK 版本、UCIe lane 对齐 | 组合不同来源的原语时，必须重新对齐基准 |
| 同模型参数假设 | 同一节点、同一 fab、同一 PDK | 跨厂/跨节点需要重新表征，不能直接复用 |
| 通信噪声 | 污染、量测噪声、过期 spec、工艺漂移 | 多步流程会放大噪声，需要 SPC/APC/FDC 抑制 |
| Organizer | 架构师 + PDK/EDA flow | 决定原语的选型、顺序与组合 |
| Knowledge Pool | IP 库 / 良率库 / tape-out 库 | 复用历史有效组合，缩短 ramp |

几个具体的“接口失配”例子：

- **单位与偏置**：一句“驱动电流提升 5%”如果没有温度、偏置、器件尺寸与工艺 corner，无法复现；PDK + compact model + corner 才是无损传递。
- **层映射与版本**：两个团队用不同 PDK 版本、不同 layer map 做同一颗芯片，合并时会出现无法解释的 DRC 与 LVS 差异。
- **坐标与基准**：reticle stitching、mask alignment、die-to-die lane reversal、clock/reset domain crossing，都是“重编码”问题；不对齐，组合后功能正确也无法工作。
- **跨厂迁移**：同一设计从 A 厂搬到 B 厂，不是“换个 PDK 重新跑一遍”，而是重新做器件表征、设计规则适配、良率验证——等价于跨模型通信时的重新对齐。

因此，**标准化不是行政工作，而是原语复用的技术前提**。一个产业的标准越成熟，原语组合的交易成本越低，复用率越高。

## 5. 复用经济学

半导体的成本结构是“高固定成本 + 低边际成本”：掩模组、设计 NRE、IP license、验证、测试程序、封装开模、 qualification 都是一次性投入；晶圆与封装则是边际成本。原语复用的经济意义，就是**把固定成本摊到更多实例上**。

可以用一个简单的净收益公式来衡量一个原语是否值得建设：

```text
原语净收益 = Σ(复用次数 × 单次可避免成本)
             − 原语建设成本
             − 接口维护成本
             − 组合验证成本
```

其中“单次可避免成本”包括：重新设计、重新验证、重新开掩模、重新爬良率、重新做封装开模、重新写测试程序。

### 5.1 主要复用杠杆

| 杠杆 | 摊销的对象 | 典型做法 | 主要风险 |
|---|---|---|---|
| IP 复用 | 设计、验证、软件适配 | 平台化 SoC、IP 目录、硬核/软核 | 接口不匹配、功耗/面积不优 |
| 标准单元库 | 器件表征、时序/功耗建模 | 同一库服务多个设计 | 库与产品目标失配 |
| 工艺模块复用 | 工艺开发、良率 ramp | 跨节点缩放、跨产品移植 | 工艺窗口变窄、需重新验证 |
| 掩模/光罩复用 | 掩模组 NRE | 衍生型号、同掩模不同 binning | 产品差异化受限 |
| 芯粒（chiplet） | 硅面积、良率、NRE | 芯粒库、UCIe 组合、KGD | 封装/热/测试复杂度上升 |
| 封装平台 | 基板、开模、组装 | 同一封装服务多产品 | 引脚/散热/机械限制 |
| MPW shuttle | 掩模与流片成本 | 多项目共享一套掩模 | 排期与 IP 保密 |
| 测试程序复用 | 测试开发、测试时间 | 同一测试平台/程序 | 覆盖不足、误判 |

### 5.2 复用不是免费

论文报告了一个很有启发的现象：原语化系统相对单 Agent 只有约 1.3–1.6 倍的推理开销，但相对文本 MAS 却把 token 与延迟降低了 3–4 倍。换句话说，**原语化不是“零成本”，而是“用较小的组合开销，换掉巨大的通信开销”。**

半导体同理：复用降低了 NRE，但把成本转移到了集成、验证、接口维护和知识库建设。一个组织如果只建原语库、不建接口与验证能力，复用率越高，集成债务越重。

### 5.3 应该追踪的复用指标

- IP 复用率：一颗 SoC 中来自既有 IP 的面积/功能占比。
- 工艺步骤复用率：新 flow 中直接复用既有 recipe 的步骤占比。
- 掩模层复用率：衍生型号中未改动的掩模层占比。
- 封装 BOM 复用率：沿用既有 substrate/underfill/料号的占比。
- 测试程序复用率：沿用既有 test program 的 coverage 占比。
- 知识库命中率：新项目在 Knowledge Pool 中找到可参考历史配置的比例。

## 6. 产品 × 原语矩阵

不同产品不是“不同原语”，而是**同一原语库的不同组合权重**。下表给出一个粗粒度矩阵，用于快速判断一个产品的主要复用杠杆。

| 产品类型 | 核心器件原语 | 关键工艺模块 | 设计原语 | 封装原语 | 主要复用杠杆 |
|---|---|---|---|---|---|
| 逻辑 SoC（CPU/GPU/AP） | GAA/FinFET、SRAM、标准单元 | HKMG、dual damascene、多重曝光、BSPDN | CPU/GPU/NPU 核、SerDes、DDR PHY、NoC | 2.5D/3D、hybrid bonding、CoWoS/InFO | IP 复用、节点移植、芯粒 |
| DRAM | 1T1C、高深宽比电容、埋入式字线 | 高深宽比刻蚀、HKMG、EUV | DDR PHY、控制器、repair 逻辑 | TSV/HBM 堆叠、FCBGA | 工艺模块复用、设计复用、HBM 平台 |
| NAND | 电荷陷阱单元、串结构 | 3D NAND 堆叠、阶梯、通道孔、ONO | 控制器、ECC、FTL | 堆叠封装、SiP | 堆叠层复用、控制器 IP 复用 |
| 模拟/电源 | BJT/CMOS/LDMOS、精密无源 | BCD、厚金属、隔离、深槽 | PLL/ADC/LDO/bandgap、电源 IP | QFN/引线框架、Clip | 成熟节点复用、模拟 IP 复用 |
| 射频/GaN/SiC | HEMT/MOSFET、无源 | 外延、注入、隔离、厚金属 | RF IP、PA/LNA、匹配网络 | AiP、CPO、腔体封装 | 材料/模块复用、RF IP 复用 |
| Si 光子 | 波导、调制器、探测器 | SOI/SiN、混合集成、光耦合 | 光引擎 IP、SerDes、DSP | CPO、光纤阵列、2.5D | 平台复用、光引擎芯粒 |
| 芯粒/先进封装 | 裸片、TSV、RDL、bump | hybrid bonding、interposer、RDL | UCIe、die-to-die、KGD 测试 | CoWoS/InFO/EMIB/Foveros | 芯粒库、封装平台、KGD |

## 7. 边界与失效模式

原语化不是万能药。半导体原语的失效模式，大致可以归为五类。

### 7.1 物理边界

- **短沟道效应与量子隧穿**：栅极越来越难控制沟道，漏电与功耗墙限制继续缩放。
- **热密度与可靠性**：IR drop、电迁移、TDDB、HCI、NBTI 在先进节点与 3D 堆叠中同时收紧。
- **工艺变异**：掺杂、厚度、线宽、overlay 的随机性，使“同一个原语”在不同 die、不同 wafer、不同 lot 上表现不同。

### 7.2 良率悬崖

- 缺陷密度、颗粒污染、EUV stochastic defect、overlay/CDU 误差，会让良率在某个参数点后非线性崩塌。
- 3D 堆叠与芯粒把“单 die 良率”变成“组合良率”：KGD 筛选与堆叠良率成为新的瓶颈。

### 7.3 接口失配

- PDK 版本、layer map、单位、corner、pin map、UCIe lane 顺序、时钟/复位域，任何一个不对齐都会导致集成失败。
- 封装引入的热机械应力，会让“电学上正确的组合”在可靠性上失败。

### 7.4 单点依赖

- EUV/High-NA 设备、光刻胶、高纯材料、先进封装产能、EDA license，都是原语复用的外部约束。
- 一个原语如果只有单一来源，它的“可复用性”会被供应风险抵消。

### 7.5 过度复用

- **产品同质化**：复用过多会让产品失去差异化，尤其在成熟节点与消费类市场。
- **架构错配**：为低功耗优化的单元库，不一定适合高频；为移动端优化的 IP，不一定适合汽车可靠性。
- **锁定效应**：深度绑定某一 PDK、EDA 或封装平台后，迁移成本会反过来吞噬复用收益。

> **没有单一原语可以通吃所有产品。** 这与论文的实验结论一致：单个原语都能带来提升，但没有一个在所有任务上占优；真正的收益来自“针对任务选择合适的组合”。

## 8. 原语检查清单

评估一个单元是否值得作为半导体原语建设，可以问十个问题：

1. **复用频次**：它在多少个产品、节点、客户或项目中重复出现？
2. **接口契约**：它的对外接口是否机器可读、版本化、可验证？
3. **成本摊销**：它摊销的是 NRE、周期、良率、产能还是风险？
4. **组合开销**：集成、验证、接口维护的成本是否低于节省的成本？
5. **失效可测**：它的失效模式是否有量测、测试或仿真覆盖？
6. **第二来源**：是否有替代供应商、替代工艺或替代 IP？
7. **单点绑定**：它是否绑定单一设备、材料、EDA 或 PDK？
8. **迁移成本**：跨节点、跨厂、跨封装迁移需要哪些重新表征？
9. **差异化**：复用它是否会削弱产品差异化？
10. **知识库**：谁负责维护它的文档、版本、历史配置与失效案例？

## 9. 结论

1. **半导体的复杂度不是靠定制堆出来的，而是靠原语复用摊销的。** 先进节点不是“更复杂的定制”，而是“更多原语、更高密度组合、更严格接口”。
2. **原语的分层比原语的清单更重要。** 器件、单元工艺、工艺模块、设计/IP、封装/系统、运营/商业，六级栈共同决定一个产业能否把研发成本摊薄。
3. **接口是复用的瓶颈。** PDK、GDSII、Liberty/LEF、UCIe、SECS/GEM 这些“无聊的标准”，实际上是半导体版的 KV cache；没有它们，原语只能停留在 PPT 上。
4. **先进封装与芯粒，是原语思想从“片上”向“系统级”的延伸。** 当单 die 缩放变贵，组合多个可复用芯粒成为继续提升系统价值的主要路径。
5. **衡量竞争力的公式**：`原语库深度 × 组合效率 × 接口标准化程度 × 知识库复用率`。四项中任何一项接近零，整体收益都会接近零。

## 附录 A：与论文的逐项对照

| 论文概念 | 半导体对应 | 关键差异 |
|---|---|---|
| Agent Primitive | 器件/工艺/模块/IP/封装/运营原语 | 半导体原语有物理约束与边际成本 |
| KV cache 隐状态通信 | PDK、GDSII/OASIS、Liberty/LEF、UCIe、SECS/GEM | 半导体接口是标准与法律/商业契约的混合体 |
| Review | DTCO、OPC/ILT、良率闭环 | 反馈周期以周/月计，不是一次推理 |
| Voting and Selection | 冗余修复、分档、多重曝光分解、双源 | 选择受良率与成本函数约束 |
| Planning and Execution | 设计→流片→制造→封测 | 执行不可逆，错误代价高 |
| Organizer | 架构师 + PDK/EDA flow | 需要跨公司、跨工厂协作 |
| Knowledge Pool | IP 库、良率库、tape-out 库 | 数据孤岛与保密是主要障碍 |
| Accuracy / token / latency | 良率 / NRE / cycle time | 半导体的“准确率”是良率与可靠性 |
| 1.3–1.6× overhead | 集成、验证、接口维护开销 | 复用率越高，集成债务越重 |

## 参考资料

1. Jin, H., Kuang, P., Yu, Y., Yuan, X., & Wang, H. *Agent Primitives: Reusable Latent Building Blocks for Multi-Agent Systems.* PMLR 306, 2026.（本仓库附件：`assets/agent-primitives-reusable.pdf`）
2. UCIe Consortium. *UCIe Specification.* <https://www.uciexpress.org/specifications>
3. SEMI. *SEMI Standards*（含 SECS/GEM 系列 E5/E30/E37）. <https://www.semi.org/en/standards>
4. Accellera Systems Initiative. *IP-XACT / IEEE 1685.* <https://www.accellera.org/downloads/standards/ip-xact>
5. JEDEC. *JEDEC Standards & Documents.* <https://www.jedec.org/standards-documents>

> 说明：本文把论文的“原语”概念作为分析框架，不宣称半导体产业与多智能体系统严格同构。文中涉及的行业成本、节点与工艺数据为量级描述，具体数值随产品、节点、地区和年份变化。
