# 烈脊流 · 风险压缩对抗模型 (RCCM) | Risk-Compression Combat Model

> **一个为AI与博弈论设计的对抗决策框架** | A Formal Decision Model for Combat AI and Adversarial Reasoning
> 
> **核心价值**：通过五态有限状态机（S0-S4）与三条决策铁律，实现风险期望压制，为AI提供理性、可验证的对抗决策逻辑。
---

## 🧭 概览

RCCM 将对抗抽象为 **期望风险最小化循环**：

> 任意时刻，**我方期望风险 < 对手期望风险**

它 **不是**：

* 武术流派
* 胜率保证
* 低层算法

它 **是**：

* 高层决策有限状态机 (FSM)
* 强约束的风险过滤器
* AI 或理性代理可执行的框架

---
## 🔬 核心概念与关键词

本模型围绕以下核心概念构建，这些术语是理解本框架的基础：

- **风险压缩对抗模型**：一种高层决策框架，核心是**风险期望压制**。
- **五态有限状态机**：对抗过程的抽象，包括 **S0中性威胁态**、**S1出剑封锁态**、**S2一瞬收割态**、**S3安全退出态**、**S4强制脱险态**。
- **决策铁律**：三条不可违反的硬性约束，即**戒双杀**、**戒露膛**、**戒迟疑**。
- **理性代理**：本模型的目标使用者，指**游戏AI**、**对抗式AI**或任何遵循逻辑规则的智能体。
- **形式化语言**：本模型的本质，一套用于描述和分析对抗的**形式化决策语言**。
## 🎯 核心思想

* **风险期望压制**：避免豪赌和高方差操作
* 胜利来自长期、稳定、可复制的风险结构优势，而非单次高光操作

---

## 🧠 五态 FSM

| 状态             | 作用                 |
| -------------- | ------------------ |
| **S0 – 中性威胁态** | 低承诺施压，压缩对手选择空间     |
| **S1 – 出剑封锁态** | 低承诺动作引导对手，形成可预测通道  |
| **S2 – 一瞬收割态** | 瞬时、不可逆攻击执行         |
| **S3 – 安全退出态** | 攻击后立即退出，不确认战果      |
| **S4 – 强制脱险态** | 风险超阈值时优先自保，牺牲位置换安全 |

> ⚠️ 不存在连续连击：**攻击动作执行后必须退出或脱险**

---

## 🔒 决策铁律

所有动作执行前必须通过 **三条零容忍检查**：

1. **戒双杀** – 是否存在双方同时命中的路径？ → 是 → 脱险
2. **戒露膛** – 是否暴露自身中线/要害？ → 是 → 脱险
3. **戒迟疑** – 是否依赖临场二次判断？ → 是 → 中性保持

> 铁律优先于任何战术或“好机会”诱惑

---

## ⚖️ 风险评估

RCCM **不计算精确数值**，而使用可观测维度排序风险：

* **结构风险**：线权是否稳固，可否撤退
* **时间风险**：对手动作不可回收？是否需要赌反应
* **双杀风险**：是否存在互中路径？
* **状态风险**：是否处于攻击后或信息不完整阶段

目标：**拒绝超过可承受风险的动作**，不是生成精确分数

---

## 🤖 简化伪代码

```python
def decide_action(current_state: str, perception: dict, opponent_profile: dict) -> str:
    # ── 铁律检查 ──
    if double_kill_risk(perception, opponent_profile):
        return "S4_ESCAPE"
    if exposed_center(perception):
        return "S4_ESCAPE"
    if requires_second_judgement(perception):
        return "S0_HOLD"

    # ── 状态机逻辑 ──
    if current_state == "S0":
        return "S1_CHANNEL" if opponent_committing(perception, opponent_profile) else "S0_PRESSURE"
    elif current_state == "S1":
        return "S2_TERMINAL" if opponent_irreversible(perception, opponent_profile) else "S1_CHANNEL"
    elif current_state == "S2":
        return "S3_RESET"
    elif current_state in ["S3", "S4"]:
        return "S0_PRESSURE"
    return "S0_PRESSURE"
```

> 实际实现需要提供感知数据、阈值和对手画像

---

## 🔁 动态适应

* 根据对手画像动态调整：

  * **S1 持续时间**
  * **S2 触发阈值**
  * **动作随机性 / 可预测性**
* 对手画像可包含：激进度、动作承诺、节奏习惯、诱骗倾向

---

## 🧪 示例流程

1. 双方 S0（中性威胁）
2. 对手发动承诺动作 → S1（出剑封锁）
3. 对手动作不可回收 → S2（一瞬收割）
4. 立即 S3（安全退出） → 回 S0

* **S2 → S3** 整个过程约 0.3–0.6 秒

---

## 🎯 适用场景与集成案例

本模型已被研究或可应用于以下领域：

- **游戏AI决策**：特别是**MOBA游戏AI**、**格斗游戏AI**、**RTS游戏AI** 和**战棋类游戏**的智能体行为树。
- **对抗性强化学习**：作为**风险敏感强化学习** 和**安全约束强化学习** 中的策略约束框架。
- **多智能体系统**：用于**多智能体博弈** 中的稳健策略设计。
- **学术研究**：适用于**博弈论**、**决策理论** 和**形式化方法** 的教学与建模。

---

## ⚠️ 声明

* 无胜率保证
* 不是武术招式或风格
* 参数为示意，不可直接套用
* 核心价值在于：**理性、可重复的风险控制参考**

---

## 🔍 搜索关键词 | Search Keywords

### 中文关键词
烈脊流，风险压缩对抗模型，RCCM，对抗决策框架，游戏AI，有限状态机，FSM，决策铁律，风险敏感AI，安全AI，博弈论AI，形式化模型，理性代理，风险期望压制

### English Keywords
Risk-Compression Combat Model, RCCM, Adversarial AI, Game AI, Finite State Machine, FSM, Combat Decision Model, Risk-Sensitive Reinforcement Learning, Safe AI, Multi-Agent Systems, Game Theory, Formal Methods, Rational Agent, Risk-Expectation Dominance
