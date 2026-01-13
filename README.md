# 烈脊流 · 风险压缩对抗模型

**Risk-Compression Combat Model (RCCM)**

> A formal decision model for adversarial combat.
> Not a fighting style, but a rational framework for risk-controlled decision-making.

---

## 🧭 项目定位（What This Is）

**烈脊流（Risk-Compression Combat Model, RCCM）** 是一套（本来是我的剑术笔记的，但后来延伸出去了）
👉 **面向对抗场景的高层决策模型**，
其目标是：

> 在任何时刻，使自身承担的风险期望 **严格低于** 对手承担的风险期望。

它不是：

* ❌ 武术流派或招式体系
* ❌ 实战教学或胜率保证
* ❌ 具体算法或工程实现

它是：

* ✅ 一个**可被实现的对抗决策模型**
* ✅ 一个**以安全与风险控制为核心的状态机**
* ✅ 一个**更适合 AI / 理性代理体执行的人类对抗抽象**

---

## 🎯 核心思想（Core Idea）

烈脊流只承认一个目标：

> **风险期望压制（Risk Expectation Dominance）**

模型拒绝一切：

* 赌命
* 换血
* 同归于尽
* 依赖临场反应的豪赌式进攻

胜利不来自单次高光操作，而来自**长期、稳定、可复制的风险结构优势**。

---

## 🧠 核心架构：五态对抗状态机

烈脊流将复杂对抗压缩为一个**有限状态机（FSM）**：

```
S0 → (S1 / S2) → (S3 / S4) → S0
```

### 五个状态

* **S0｜中性威胁态（Neutral Pressure）**
  建立压力，不承诺攻击；压缩对手选择空间。

* **S1｜出剑封锁态（Channel Control）**
  使用低承诺动作限制对手选项，制造可预测通道。

* **S2｜一瞬收割态（Terminal Window）**
  在极短、不可逆时间窗内完成攻击。

  > 进入即执行，执行即离开。

* **S3｜安全退出态（Safe Reset）**
  攻击后立即脱离，不确认战果、不补击。

* **S4｜强制脱险态（Emergency Escape）**
  风险失控时优先自保，牺牲位置换安全。

⚠️ 模型**不存在连击循环或持续进攻态**。
任何攻击行为，都会强制进入退出或脱险逻辑。

---

## 🔒 决策铁律（Hard Constraints）

所有动作在执行前，必须依次通过三条**不可违反的铁律**：

1. **戒双杀**
   是否存在双方同时被命中的现实路径？
   → 是，则否决。

2. **戒露膛**
   是否暴露自身核心要害或中线？
   → 是，则否决。

3. **戒迟疑**
   是否依赖临场二次判断或临时变招？
   → 是，则否决。

> 这三条铁律构成一个**动作安全门控系统**，
> 高于任何战术、风格或“好机会”的诱惑。
def decide_action(current_state: str, perception: dict, opponent_profile: dict) -> str:
    """
    RCCM 主决策函数
    :param current_state: 当前状态 ('S0', 'S1', 'S2', 'S3', 'S4')
    :param perception: 当前感知数据（距离、角度、速度、轨迹预测等）
    :param opponent_profile: 对手动态画像（激进度、习惯等，可影响阈值）
    :return: 下一状态（或动作标签）
    """
    
    # ── 铁律检查（任何状态下都必须先过这三关） ──
    if double_kill_risk(perception, opponent_profile):
        return "S4_ESCAPE"          # 零容忍双杀
    
    if exposed_center(perception):
        return "S4_ESCAPE"          # 中线暴露 = 立即自保
    
    if requires_second_judgement(perception):
        return "S0_HOLD"            # 信息不完整，强制回中性
    
    # ── 状态转移逻辑 ──
    if current_state == "S0":
        # 在中性态下倾向于持续施压，但对手有明显承诺才进S1
        if opponent_committing(perception, opponent_profile):
            return "S1_CHANNEL"
        return "S0_PRESSURE"        # 持续中性施压（微前进/控距）

    elif current_state == "S1":
        # 通道控制态：等待对手动作不可回收
        if opponent_irreversible(perception, opponent_profile):
            return "S2_TERMINAL"
        return "S1_CHANNEL"         # 继续维持通道

    elif current_state == "S2":
        # 终端态只允许瞬时执行，然后强制退出
        return "S3_RESET"

    elif current_state in ["S3", "S4"]:
        # 攻击后/脱险后，统一回中性重构
        return "S0_PRESSURE"

    # 防御性默认（理论上不应到达）
    return "S0_PRESSURE"

    def double_kill_risk(perception: dict, profile: dict = None) -> bool:
    """
    双杀风险判断 - 零容忍
    三个条件必须**同时**满足才算有双杀风险
    """
    # 必要条件1: 角度过小（武器轨迹威胁中线）
    angle_to_midline = perception.get('opponent_weapon_angle_to_my_midline', 90)
    if angle_to_midline >= 30:  # 30°是硬阈值，可调
        return False
    
    # 必要条件2: 时间差极小（几乎同时命中）
    time_diff = perception.get('predicted_hit_time_diff', 1.0)  # 我方命中 - 对方命中，单位秒
    threshold = 0.15 * (profile.get('opponent_speed_factor', 1.0))  # 可根据对手速度微调
    if abs(time_diff) >= threshold:
        return False
    
    # 必要条件3: 我方当前动作回收能力不足
    if not perception.get('can_fully_recover_current_action', True):
        return True  # 三条件同时成立 → 有双杀风险
    
    return False
---

## ⚖️ 风险评估：不是算数，而是排序

烈脊流不追求精确风险数值，而使用**可观测维度的快速判定**。

### 核心风险维度

* **结构风险**：线权是否稳固？是否仍可防可退？
* **时间风险**：对手动作是否已不可回收？是否需要赌反应？
* **双杀风险**：是否存在合理的互中路径？（零容忍）
* **状态风险**：是否处于攻击后或判断不完整阶段？

风险评估的目标只有一个：

> **判断当前动作是否越过“不可承受风险阈值”**

而不是计算一个“精确风险值”。

---

## 🔁 对手适应与动态调整

烈脊流不是固定套路，而是一个**会调整出手标准的系统**。

在对抗中持续更新对手画像，例如：

* 激进程度
* 动作承诺强度
* 节奏与习惯
* 诱骗与反制倾向

根据画像动态调整：

* S2 触发阈值
* S1 持续时间
* 动作选择的随机性与可预测性

---

## 🧪 示例：简化对抗 Walkthrough

一个典型流程：

1. 双方处于 **S0**，我方前压控距
2. 对手激进启动攻击（承诺不足）
3. 我方进入 **S1**，低承诺封锁、引导
4. 对手动作不可回收
5. 进入 **S2**，瞬时执行攻击
6. 立即进入 **S3**，安全退出，回到 **S0**

整个 **S2 → S3** 过程应在 **0.3–0.6 秒量级** 内完成，
体现其“瞬时、不可纠缠”的本质。

---

## 🤖 为什么它对 AI / 游戏开发者有价值？

* 明确的 **状态机结构**
* 可实现的 **硬约束决策铁律**
* 风险评估基于 **可观测特征**
* 天然适合作为：

  * AI 决策层
  * 行为树 / 规则树的上层约束
  * 策略网络的前置安全过滤器

烈脊流更像一个 **Decision Safety Shell**，
而不是一个“教你怎么打”的系统。

---

## 📌 适用场景

* 对抗式 AI 研究
* 游戏 AI（MOBA / 对战 / 战棋）
* 博弈论教学与分析
* 对抗行为复盘与结构评估

---

## ⚠️ 声明与边界

* 本模型**不保证实战胜率**
* 不等同于具体招式或比赛策略
* 示例参数均为示意，**不可直接套用**
* 作者本人亦无法稳定完整执行该模型（属预期情况）

烈脊流的意义不在于“能否被完全执行”，
而在于它提供了一个**严格、理性的风险控制参照上限**。

---

## 🧩 结语

> 烈脊流不是一把能直接使用的剑，
> 而是一张关于“如何不被自己杀死”的对抗蓝图。

欢迎 critique、扩展与实现。
