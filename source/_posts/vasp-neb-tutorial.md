---
title: VASP+VTST 计算过渡态 — NEB 方法
date: 2026-07-13 17:00:00
tags: [VASP, VTST, NEB, 过渡态]
---

> 本文为原创，内容参考刘锦程老师的 NEB 教程整理而成。过渡态计算是催化反应机理研究中的关键步骤，NEB 方法是其中最常用的技术之一。

## 原理

过渡态（transition state）搜索，即优化到一阶鞍点，是 MEP（Minimum Energy Path）路径上能量最高的点。在过渡态位置上，仅在沿着反应路径方向上是能量极大点，而在与之正交的其他所有方向上都是能量极小点。

![势能面与过渡态示意图](/img/lvthw/neb-tutorial/pes-3d.jpg)

## NEB 方法

NEB 是 chain-of-states 搜索方法的一种，经过长时间的发展形成了准确性好、稳定性好的 CI-NEB 方法。做法是在反应物到产物之间插入一系列结构（共 P-1 个），反应物编号为 0，产物编号为 P。优化不是对每个点孤立地优化，而是优化一个函数，每一步所有点一起运动。

![NEB 与 CI-NEB 对比](/img/lvthw/neb-tutorial/neb-vs-cineb.jpg)

这 P+1 个点保持第一和最后一个点不动，其他的点受到两个力的作用：
1. 来自势能面的力
2. chain 方向上的弹簧力

![弹簧力示意图](/img/lvthw/neb-tutorial/spring-force.jpg)

其中第一项相当于让每个点都在势能面上向着最稳定的方向移动；第二项含有弹簧力常数，相当于每个珠子之间的拉力和排斥力，避免珠子之间的距离过短或过长。

![NEB 受力公式](/img/lvthw/neb-tutorial/equation-neb.jpg)

### 传统 NEB 方法遇到的问题

1. 反应路径偏离 MEP，在势能面拐点处会出现两个珠子距离过远的问题
2. 难以找到最高点，需要插入非常多的点，浪费计算资源

![传统 NEB 的路径偏离问题](/img/lvthw/neb-tutorial/traditional-neb-problems.jpg)

### NEB 的 Nudge 过程

通过 Nudge 过程，弹簧力垂直于路径的分量被投影掉，弹簧力平行于路径的分量完全保留；势能力在路径方向上的分量被投影掉，势能力垂直于路径上的分量将会引导结构点正确移动。

![Nudge 过程示意图](/img/lvthw/neb-tutorial/nudge-process.jpg)

![Nudge 受力公式](/img/lvthw/neb-tutorial/nudge-formula.jpg)

这样优化收敛后结构点就能正确描述真实的 MEP。

## CI-NEB 方法

CI-NEB（Climbing Image NEB）由 G. Henkelman（VTST 开发者）提出，专门考虑了定位过渡态的问题。

### 与普通 NEB 的关键区别

能量最高的点不受相邻点的弹簧力，避免位置被拉离过渡态，而且将此点平行于路径方向的势能力分量的符号反转，促使此点沿着路径往能量升高的方向爬到过渡态。

CI-NEB 方法只需要很少的点（包含初末态总共 5 个甚至 3 个点）就能准确定位过渡态。

> ⚠️ 注意：CI-NEB 插点并非越多越好！由于能量最高的点可以自动爬坡，有时插 1 个点也能精确找到过渡态，大多数时候插 3-4 个点已完全足够。

---

## CI-NEB 计算步骤

### 步骤一：优化初态和末态结构

分别优化反应物和产物的结构。

### 步骤二：检查初末态结构的相似程度

使用 VTST 脚本 `dist.pl` 检查两个 CONTACAR 的相似程度：

```bash
dist.pl ini/CONTCAR fin/CONTCAR
```

若返回值 < 5 Å，一般可以进行下一步。

> 注意：如果数值非常大，要检查初态和末态的原子顺序是否一一对应。

### 步骤三：插点

使用 `nebmake.pl` 插点，插点数目可取 dist.pl 返回值 / 0.8：

```bash
nebmake.pl ../is/CONTCAR ../fs/CONTCAR 1
```

这个例子插一个点就够了。

成功后会生成 `00`、`01`、`02` 等文件夹，分别对应初态、中间点和末态。

### 步骤四：复制 OUTCAR

将初末态对应的 OUTCAR 复制到对应文件夹中，以便后续数据分析。

### 步骤五：检查插入点的合理性（可跳过）

```bash
nebmovie.pl 0
```

生成 `movie.xyz`，可用 Jmol 查看所有结构。

### 步骤六：准备 INCAR

在结构优化 INCAR 基础上修改：

```incar
# 电子步收敛（关键参数）
EDIFF = 1E-7

# 结构优化收敛标准
EDIFFG = -0.03

# VTST 优化算法标志
IBRION = 3
POTIM = 0

# 优化算法选择（推荐 1、2 或 7）
IOPT = 1

# 开启 NEB 方法
ICHAIN = 0

# CI-NEB 爬坡
LCLIMB = .TRUE.

# 插点个数
IMAGES = 1

# 弹簧力常数（默认 -5）
SPRING = -5

# 其他参数
NSW = 300
ISIF = 2
```

![INCAR 设置截图](/img/lvthw/neb-tutorial/incar-example.jpg)

**IOPT 优化算法选择：**

| IOPT | 算法 | 适用场景 |
|:----:|------|---------|
| 0 | VASP 自带优化器 | 默认 |
| 1 | LBFGS | 适合精收敛 |
| 2 | CG | 适合精收敛 |
| 3 | Quick-Min | — |
| 4 | Steepest Descent | — |
| 7 | FIRE | 适合粗收敛 |

### 步骤七：准备 POTCAR 和 KPOINTS

同结构优化，直接复制即可。

### 步骤八：提交任务

> ⚠️ 注意：使用的核数必须整除插点的数量。为保证最大并行效率，节点数最好等于插点的个数。

### 步骤九：跟踪检查收敛

```bash
# 生成 movie.xyz 查看反应路径
nebmovie.pl 1

# 查看计算收敛情况
nebef.pl
# 或
nebefs.pl
```

![nebef.pl 输出示例](/img/lvthw/neb-tutorial/output-example.jpg)

输出结果中，三列分别为：最大原子受力、能量、相对初态的能量。

---

## 完整 INCAR 示例

```incar
SYSTEM = Au
ENCUT = 400
ISMEAR = 1
SIGMA = 0.2
NELMIN = 5
NELM = 300
EDIFF = 1E-7
EDIFFG = -0.03
IBRION = 3
POTIM = 0
NSW = 300
ISIF = 2
ISTART = 1
ICHARG = 1
LCHARG = .TRUE.
LWAVE = .TRUE.
LREAL = Auto
ISYM = 0
ICHAIN = 0
LCLIMB = .TRUE.
IMAGES = 1
SPRING = -5
IOPT = 1
```

---

## 注意事项

1. **EDIFF** 参数非常关键！过渡态对力计算精度要求极高，更精准的电子步收敛会有更精准的力，可以加速收敛
2. **EDIFFG** 可适当放宽到 -0.03，复杂体系可放宽到 -0.05
3. CI-NEB 插点不是越多越好，3-4 个点通常足够
4. 计算过程中可用 `nebef.pl` 实时监控收敛情况
