# 中文头脑风暴标准报告结构与字段规范

## 1. 适用范围

本规范用于记录、交换和评审中文头脑风暴结果。报告应同时满足以下要求：

1. 可追溯：每个创意都能追溯到输入材料、观察、访谈、数据或使用的方法。
2. 可验证：关键假设、未知项和验证动作均有明确记录。
3. 可关联：创意之间的依赖、组合、冲突、替代和演化关系可被识别。
4. 可执行：每个保留中的创意都有唯一且具体的下一步行动。
5. 可比较：所有创意使用同一组必填字段和受控状态值。

## 2. 标准报告结构

报告按以下顺序组织：

1. report_meta：报告标识、标题、版本、语言、时间与状态。
2. brief：议题、目标、范围、约束和成功标准。
3. sources：报告引用的来源清单。
4. methods：本次使用的发散、组合、筛选或验证方法。
5. ideas：标准化创意清单。
6. synthesis：主题聚类、共识、分歧和组合机会。
7. decisions：采纳、待验证、搁置或淘汰的决策记录。
8. unknowns：跨创意或报告级未知项。
9. next_steps：报告级后续行动。

除 report_meta、brief、ideas 外，其他顶层字段在无内容时仍应保留，并按空值规则填写。

## 3. 通用字段规则

### 3.1 命名与格式

- 字段名统一使用小写 snake_case。
- 标识符统一使用字符串，不使用纯数字。
- 日期使用 YYYY-MM-DD。
- 日期时间使用 RFC 3339 格式，例如 2026-07-15T14:30:00+08:00。
- 自然语言内容使用简体中文；专有名词可保留原文。
- 数组始终使用数组类型。单个值也不得缩写成字符串。
- 布尔值仅使用 true 或 false。
- 枚举值必须使用本规范定义的小写英文值。
- 金额、人数、比例、时长等量化值应同时写明数值、单位和口径。
- 不得通过字段缺失表达“不知道”“不适用”或“未提供”。

### 3.2 标识符

推荐前缀：

| 对象 | 格式 | 示例 |
|:--|:--|:--|
| 报告 | report_序号或日期_序号 | report_20260715_01 |
| 来源 | src_三位序号 | src_001 |
| 方法 | method_三位序号 | method_001 |
| 创意 | idea_三位序号 | idea_001 |
| 假设 | asm_三位序号 | asm_001 |
| 风险 | risk_三位序号 | risk_001 |
| 验证项 | val_三位序号 | val_001 |
| 关系 | rel_三位序号 | rel_001 |
| 未知项 | unk_三位序号 | unk_001 |
| 决策 | dec_三位序号 | dec_001 |
| 行动 | act_三位序号 | act_001 |

标识符在同一报告内必须唯一，发布后不得因排序变化而修改。

## 4. 顶层字段规则

### 4.1 report_meta

| 字段 | 类型 | 必填 | 规则 |
|:--|:--|:--|:--|
| id | string | 是 | 报告唯一标识 |
| title | string | 是 | 明确描述议题，不使用“头脑风暴报告”等泛化标题 |
| version | string | 是 | 使用语义化版本，例如 1.0.0 |
| language | string | 是 | 中文报告固定为 zh-CN |
| created_at | datetime | 是 | 首次创建时间 |
| updated_at | datetime | 是 | 最近更新时间 |
| owner | string 或 null | 是 | 报告负责人；未知时为 null |
| status | enum | 是 | 使用报告状态值 |

### 4.2 brief

| 字段 | 类型 | 必填 | 规则 |
|:--|:--|:--|:--|
| topic | string | 是 | 待探索的核心议题 |
| objective | string | 是 | 本次头脑风暴要产生的结果 |
| target_users | array[string] | 是 | 目标人群；未知时为空数组并登记未知项 |
| in_scope | array[string] | 是 | 明确包含的范围 |
| out_of_scope | array[string] | 是 | 明确排除的范围 |
| constraints | array[string] | 是 | 预算、时间、技术、合规等约束 |
| success_criteria | array[string] | 是 | 可观察或可量化的成功标准 |

### 4.3 synthesis

| 字段 | 类型 | 必填 | 规则 |
|:--|:--|:--|:--|
| themes | array[object] | 是 | 每项包含 name、idea_ids、insight |
| agreements | array[string] | 是 | 已形成的共识 |
| disagreements | array[string] | 是 | 尚未解决的分歧 |
| combination_opportunities | array[object] | 是 | 每项包含 idea_ids、rationale、proposed_result |

## 5. 来源定义

sources 中每个来源对象使用以下字段：

| 字段 | 类型 | 必填 | 规则 |
|:--|:--|:--|:--|
| id | string | 是 | 来源唯一标识 |
| type | enum | 是 | 来源类型 |
| title | string | 是 | 来源名称或简短描述 |
| locator | string 或 null | 是 | URL、文件路径、访谈编号、数据表名等定位信息 |
| author_or_owner | string 或 null | 是 | 作者、受访者角色或数据负责人 |
| observed_at | date 或 null | 是 | 内容产生、访问或观察日期 |
| reliability | enum | 是 | 来源可靠性等级 |
| notes | string 或 null | 是 | 偏差、限制、样本范围等说明 |

来源类型 type 的允许值：

- user_research：用户访谈、问卷、可用性测试。
- observation：现场观察、行为记录。
- data：业务数据、实验数据、统计结果。
- document：文档、报告、论文、制度材料。
- stakeholder_input：利益相关方意见。
- market_reference：市场案例、竞品或行业信息。
- expert_judgment：专家判断。
- workshop_output：工作坊中的原始便签、讨论或投票结果。
- assumption：尚无外部证据，仅为团队假设。
- other：无法归入以上类型，必须在 notes 中说明。

来源可靠性 reliability 的允许值：

- high：来源直接、可复核，且样本或方法与问题高度匹配。
- medium：有一定证据支持，但存在样本、时效或方法限制。
- low：间接信息、单一样本、主观判断或无法独立复核。
- unknown：尚未评估可靠性。

创意不得把 source_methods 当作完整证据说明。source_methods 只保存来源和方法的引用标识，详细信息分别在 sources 与 methods 中定义。

## 6. 方法定义

methods 中每个方法对象使用以下字段：

| 字段 | 类型 | 必填 | 规则 |
|:--|:--|:--|:--|
| id | string | 是 | 方法唯一标识 |
| name | string | 是 | 方法名称，例如 SCAMPER、逆向假设、六顶思考帽 |
| category | enum | 是 | divergent、convergent、research、validation 之一 |
| purpose | string | 是 | 本次使用该方法的目的 |
| input_source_ids | array[string] | 是 | 使用的来源 id；无外部输入时为空数组 |
| notes | string 或 null | 是 | 执行方式、参与者或限制 |

## 7. 创意字段规范

ideas 中每个创意必须包含以下全部字段，不得省略：

| 字段 | 类型 | 必填 | 规则 |
|:--|:--|:--|:--|
| id | string | 是 | 创意唯一标识 |
| name | string | 是 | 2 至 20 个汉字为宜，体现核心动作或价值 |
| summary | string | 是 | 1 至 3 句话说明“为谁、解决什么、怎么做” |
| source_methods | object | 是 | 包含 source_ids 和 method_ids 两个数组 |
| problem_or_need | object | 是 | 包含 statement、evidence_source_ids、severity |
| beneficiary | object | 是 | 包含 primary、secondary、context |
| core_mechanism | object | 是 | 包含 description、key_steps、required_capabilities |
| assumptions | array[object] | 是 | 关键假设清单；无已识别假设时为空数组 |
| expected_value | object | 是 | 包含 user_value、business_value、measurement |
| risks | array[object] | 是 | 风险清单；无已识别风险时为空数组 |
| validation | array[object] | 是 | 验证计划；搁置或淘汰时可为空数组 |
| next_action | object 或 null | 是 | 下一步；终态创意可为 null |
| relations | array[object] | 是 | 与其他创意的关系；无关系时为空数组 |
| uncertainty | object | 是 | 包含 level、unknown_ids、notes |
| status | enum | 是 | 创意当前状态 |

### 7.1 source_methods

```yaml
source_methods:
  source_ids:
    - src_001
  method_ids:
    - method_001
```

规则：

- source_ids 仅引用 sources 中存在的 id。
- method_ids 仅引用 methods 中存在的 id。
- 完全由团队自由发散产生且无外部来源时，source_ids 可为空数组，但 method_ids 不应为空。
- 若创意直接源于某项假设，应引用 type 为 assumption 的来源，同时在 assumptions 中登记该假设。

### 7.2 problem_or_need

```yaml
problem_or_need:
  statement: 新用户首次设置时不知道应优先完成哪些步骤，导致中途退出。
  evidence_source_ids:
    - src_001
  severity: high
```

severity 允许值为 low、medium、high、critical、unknown。

### 7.3 beneficiary

```yaml
beneficiary:
  primary:
    - 首次使用产品的新用户
  secondary:
    - 客服团队
  context: 用户在移动端完成首次设置的前十分钟。
```

primary 必须至少包含一个直接受益者。受益者未知时，填入“待识别受益者”，并创建对应未知项，不得凭空推断。

### 7.4 core_mechanism

```yaml
core_mechanism:
  description: 根据用户目标动态生成三步式设置路径，并在每一步完成后即时反馈进度。
  key_steps:
    - 询问用户的首要目标
    - 匹配最短设置路径
    - 展示步骤并保存进度
  required_capabilities:
    - 目标分类规则
    - 设置进度存储
```

机制描述应说明因果链路，不得只写功能名称或宣传口号。

### 7.5 assumptions

每个假设对象包含：

| 字段 | 类型 | 必填 | 规则 |
|:--|:--|:--|:--|
| id | string | 是 | 假设唯一标识 |
| statement | string | 是 | 可被证伪的陈述 |
| type | enum | 是 | 假设类型 |
| importance | enum | 是 | low、medium、high、critical |
| confidence | enum | 是 | low、medium、high、unknown |
| evidence_source_ids | array[string] | 是 | 支撑证据来源；没有证据时为空数组 |
| status | enum | 是 | 假设状态 |

假设类型 type 的允许值：

- desirability：用户是否需要或愿意采用。
- feasibility：技术、运营或资源上是否可实现。
- viability：商业、成本或组织上是否可持续。
- usability：用户是否能理解并顺利使用。
- compliance：是否符合安全、隐私、法律或行业要求。
- causal：机制是否会带来预期结果。

假设状态允许值：

- untested：尚未验证。
- testing：正在验证。
- supported：现有证据支持，但不表示永久成立。
- rejected：已被证据否定。
- inconclusive：已有验证但结论不足。

### 7.6 expected_value

```yaml
expected_value:
  user_value:
    - 降低首次设置的理解成本
    - 更快获得首次成功体验
  business_value:
    - 提高新用户激活率
    - 减少设置类客服咨询
  measurement:
    - metric: 首次设置完成率
      direction: increase
      target: 从当前基线提升 10%
      timeframe: 上线后 4 周
    - metric: 设置类客服咨询率
      direction: decrease
      target: 相对基线下降 15%
      timeframe: 上线后 4 周
```

measurement 中 direction 允许值为 increase、decrease、maintain、observe。基线未知时可在 target 中明确写“待建立基线”，并关联未知项。

### 7.7 risks

每个风险对象包含：

| 字段 | 类型 | 必填 | 规则 |
|:--|:--|:--|:--|
| id | string | 是 | 风险唯一标识 |
| description | string | 是 | 具体描述可能发生的负面事件 |
| category | enum | 是 | user、technical、operational、business、compliance、ethical、dependency、other |
| likelihood | enum | 是 | low、medium、high、unknown |
| impact | enum | 是 | low、medium、high、critical、unknown |
| mitigation | string 或 null | 是 | 已知缓解措施；暂无时为 null |
| owner | string 或 null | 是 | 风险负责人；未分配时为 null |
| status | enum | 是 | 风险状态 |

风险状态允许值：

- identified：已识别，尚未处理。
- monitoring：正在监控。
- mitigating：正在采取缓解措施。
- accepted：明确接受该风险。
- resolved：风险已消除或降至可接受水平。
- occurred：风险已经发生。

### 7.8 validation

每个验证对象包含：

| 字段 | 类型 | 必填 | 规则 |
|:--|:--|:--|:--|
| id | string | 是 | 验证项唯一标识 |
| target_assumption_ids | array[string] | 是 | 要验证的假设 id |
| method | string | 是 | 访谈、原型测试、数据分析、技术验证等 |
| procedure | string | 是 | 可复现的执行步骤 |
| success_criteria | array[string] | 是 | 预先定义的判定标准 |
| owner | string 或 null | 是 | 负责人 |
| due_date | date 或 null | 是 | 截止日期 |
| status | enum | 是 | 验证状态 |
| result | string 或 null | 是 | 结果；未完成时为 null |

验证状态允许值：

- planned：已计划。
- ready：条件已具备，可开始。
- running：正在执行。
- completed：已完成并记录结果。
- blocked：被依赖项阻塞。
- cancelled：已取消。

### 7.9 next_action

```yaml
next_action:
  id: act_001
  description: 制作三步式设置路径的可点击原型，并招募 5 名首次使用者测试。
  owner: 产品设计负责人
  due_date: 2026-07-22
  status: planned
```

行动状态允许值为 planned、in_progress、blocked、completed、cancelled。

active、candidate、validating 状态的创意原则上必须提供 next_action。adopted、parked、rejected、merged 状态可为 null，但相关原因必须在 decisions 中记录。

### 7.10 relations

每个关系对象包含：

| 字段 | 类型 | 必填 | 规则 |
|:--|:--|:--|:--|
| id | string | 是 | 关系唯一标识 |
| type | enum | 是 | 关系类型 |
| target_idea_id | string | 是 | 目标创意 id，不得指向自身 |
| description | string | 是 | 说明关系成立的具体原因 |
| strength | enum | 是 | weak、medium、strong、unknown |

关系类型 type 的允许值：

- depends_on：当前创意依赖目标创意。
- enables：当前创意为目标创意提供实现条件。
- complements：两者组合可增加价值。
- conflicts_with：两者存在资源、逻辑或体验冲突。
- alternative_to：两者解决相近问题，可互为替代。
- extends：当前创意扩展目标创意。
- derived_from：当前创意由目标创意演化而来。
- merges_into：当前创意已合并到目标创意。
- duplicates：两者实质重复。

方向性关系必须从当前创意指向 target_idea_id。对称关系 complements、conflicts_with、alternative_to、duplicates 建议在两端都登记，便于独立读取。

### 7.11 uncertainty

```yaml
uncertainty:
  level: high
  unknown_ids:
    - unk_001
  notes: 用户采用意愿和实现成本均缺少直接证据。
```

level 允许值为 low、medium、high、critical、unknown。

判定建议：

- low：关键问题均有较可靠证据，仅剩执行细节。
- medium：存在可控未知项，不影响是否继续探索。
- high：至少一个关键价值、可行性或因果假设未验证。
- critical：未知项可能使创意不可行、不合规或造成重大损失。
- unknown：尚未完成不确定性评估。

## 8. 未知项定义

unknowns 中每个未知项包含：

| 字段 | 类型 | 必填 | 规则 |
|:--|:--|:--|:--|
| id | string | 是 | 未知项唯一标识 |
| question | string | 是 | 用可回答的问题表达，不使用模糊标签 |
| scope | enum | 是 | report、idea、assumption、risk、metric |
| related_ids | array[string] | 是 | 关联对象 id |
| importance | enum | 是 | low、medium、high、critical |
| resolution_method | string 或 null | 是 | 计划如何获取答案 |
| owner | string 或 null | 是 | 负责人 |
| due_date | date 或 null | 是 | 截止日期 |
| status | enum | 是 | 未知项状态 |
| answer | string 或 null | 是 | 已解决时填写答案，否则为 null |
| evidence_source_ids | array[string] | 是 | 支撑答案的来源 id |

未知项状态允许值：

- open：尚未处理。
- investigating：正在调查。
- blocked：当前无法调查。
- resolved：已有足够答案。
- accepted_unknown：经明确决策暂不解决。
- obsolete：因范围或方案变化而不再相关。

未知项与假设的区别：

- 假设是暂时相信、且能够被证伪的陈述，例如“用户愿意花 2 分钟完成目标选择”。
- 未知项是尚无答案的问题，例如“用户可接受的目标选择时长是多少”。
- 同一主题可以同时存在假设和未知项，但必须分别记录并互相引用相关对象。

## 9. 状态值总表

### 9.1 报告状态

- draft：编辑中，内容可能不完整。
- in_review：等待或正在评审。
- approved：已通过评审，可作为当前有效版本。
- archived：已归档，不再更新。

### 9.2 创意状态

- candidate：已记录，尚未筛选。
- active：已进入进一步设计或讨论。
- validating：正在验证关键假设。
- adopted：已决定采纳并进入实施。
- parked：暂时搁置，未来可重新评估。
- rejected：明确淘汰。
- merged：已合并到另一创意。

状态变化应记录在 decisions 中，不应直接覆盖而不留决策依据。

### 9.3 决策状态

decisions 中的 decision 值允许：

- adopt：采纳。
- validate：先验证再决定。
- park：搁置。
- reject：淘汰。
- merge：合并。
- revisit：要求重新讨论。

## 10. 空值与缺失信息处理

1. 必填字段不得缺失。
2. 对象存在但暂无条目时，使用空数组 []，不得使用 null。例如 risks、relations、unknown_ids。
3. 可空标量使用 null，不使用空字符串、“无”“暂无”“N/A”或“-”。例如 owner、due_date、result。
4. 确认“不适用”时，也使用 null，并在相邻 notes、description 或决策记录中说明不适用原因。
5. 信息未知时使用 null 或 unknown 枚举，并创建 unknowns 项；不得虚构内容补齐。
6. 数值未知时不得填 0，因为 0 是有效数值。应使用 null，并在未知项中记录待确认问题。
7. 不确定枚举值优先使用 unknown；不得自行增加 maybe、tbd 等值。
8. 空对象 {} 不允许出现。对象若为必填，应提供其全部子字段。
9. 已完成状态通常不得保留关键结果为 null。例如 validation.status 为 completed 时 result 必须有值。
10. next_action 为 null 时，创意必须处于 adopted、parked、rejected 或 merged 之一，并在 decisions 中说明原因或后续承接位置。

## 11. 一致性校验规则

发布前至少检查以下规则：

1. 所有 id 在各自对象类型中唯一。
2. 所有引用 id 均真实存在。
3. relations.target_idea_id 不得等于当前创意 id。
4. merges_into 关系对应的创意状态应为 merged。
5. merged 决策必须给出目标创意 id。
6. rejected 或 parked 状态必须有对应 decisions 记录。
7. assumptions.status 为 supported 或 rejected 时，evidence_source_ids 不得为空。
8. validation.status 为 completed 时，result 不得为 null。
9. unknowns.status 为 resolved 时，answer 不得为 null，且通常应提供 evidence_source_ids。
10. next_action.status 为 completed 时，应更新创意状态或提供新的 next_action。
11. uncertainty.unknown_ids 中的每个 id 均应关联回该创意或其假设、风险、指标。
12. expected_value.measurement 的 target、timeframe 不得省略；未知时明确登记未知项。

## 12. 完整可复制 Markdown 示例

```markdown
# 新用户首次设置体验头脑风暴报告

## 报告信息

- 报告 ID：report_20260715_01
- 版本：1.0.0
- 语言：zh-CN
- 创建时间：2026-07-15T14:30:00+08:00
- 更新时间：2026-07-15T16:00:00+08:00
- 负责人：产品负责人
- 状态：in_review

## 议题简报

- 议题：降低新用户首次设置过程中的退出率
- 目标：形成可在两周内验证的体验改进创意
- 目标用户：首次使用产品的新用户
- 范围内：移动端首次设置、目标选择、进度反馈
- 范围外：注册登录、付费转化、桌面端改版
- 约束：验证预算不超过 5000 元；不得改变现有账号体系
- 成功标准：至少形成 2 个机制不同的创意；每个保留创意都有关键假设和验证计划

## 来源

### src_001 首次设置漏斗数据

- 类型：data
- 定位：analytics.onboarding_funnel_2026q2
- 作者或负责人：数据分析团队
- 观察日期：2026-07-10
- 可靠性：high
- 说明：包含 2026 年第二季度 18240 名新用户，未区分用户目标类型

### src_002 新用户访谈

- 类型：user_research
- 定位：research/interviews/onboarding-01-to-08
- 作者或负责人：用户研究团队
- 观察日期：2026-07-12
- 可靠性：medium
- 说明：共 8 名受访者，样本规模有限

## 方法

### method_001 SCAMPER

- 类别：divergent
- 目的：重新组合首次设置步骤并减少非必要操作
- 输入来源：src_001、src_002
- 说明：由产品、设计、研发共 6 人完成

## 创意

### idea_001 目标驱动三步设置

- 状态：validating
- 摘要：为首次使用产品的新用户提供按目标动态生成的三步设置路径，减少无关选项，并通过即时进度反馈帮助用户完成首次设置。
- 来源：src_001、src_002
- 方法：method_001
- 问题或需求：新用户不知道应优先完成哪些设置步骤，导致中途退出。
- 问题证据：src_001、src_002
- 严重程度：high
- 主要受益者：首次使用产品的新用户
- 次要受益者：客服团队
- 使用情境：用户在移动端完成首次设置的前十分钟
- 核心机制：根据用户首要目标匹配最短设置路径，并在每一步完成后反馈进度。
- 关键步骤：询问首要目标；匹配最短路径；展示步骤并保存进度
- 所需能力：目标分类规则；设置进度存储
- 预期用户价值：降低理解成本；更快获得首次成功体验
- 预期业务价值：提高激活率；减少设置类客服咨询
- 衡量方式：首次设置完成率相对当前基线提升 10%，上线后 4 周评估
- 下一步：act_001，制作可点击原型并招募 5 名首次使用者测试，负责人为产品设计负责人，截止 2026-07-22，状态 planned
- 不确定性：high
- 未知项：unk_001
- 不确定性说明：用户是否愿意先回答目标问题尚无直接证据

#### 假设

- asm_001：至少 60% 的新用户能在 30 秒内选出一个首要目标；类型 usability；重要性 high；信心 low；证据为空；状态 untested
- asm_002：缩短路径会提高首次设置完成率；类型 causal；重要性 critical；信心 medium；证据 src_001；状态 untested

#### 风险

- risk_001：目标分类错误可能向用户展示不相关步骤；类别 user；可能性 medium；影响 high；缓解措施为允许用户随时切换目标；负责人为产品负责人；状态 identified

#### 验证

- val_001：验证 asm_001；方法为可点击原型测试；步骤为让 5 名首次使用者独立选择目标并完成设置；成功标准为至少 4 人在 30 秒内选定目标，且至少 4 人无需提示完成三步；负责人为用户研究员；截止 2026-07-22；状态 planned；结果为空

#### 关系

- rel_001：complements，目标 idea_002；组合后可同时减少步骤并提供人工帮助；强度 medium

### idea_002 首次设置即时求助

- 状态：candidate
- 摘要：在首次设置的高退出步骤提供上下文求助入口，为遇到阻碍的新用户提供短说明或转接客服。
- 来源：src_001、src_002
- 方法：method_001
- 问题或需求：部分用户在专业术语较多的步骤无法继续。
- 问题证据：src_002
- 严重程度：medium
- 主要受益者：首次使用产品且缺少领域知识的新用户
- 次要受益者：客服团队
- 使用情境：用户在设置步骤停留超过 45 秒时
- 核心机制：检测长时间停留，在当前步骤展示与上下文匹配的短说明和求助入口。
- 关键步骤：检测停留；匹配帮助内容；展示说明或转接客服
- 所需能力：停留事件采集；帮助内容库；客服转接
- 预期用户价值：及时解除设置阻碍
- 预期业务价值：降低关键步骤退出率
- 衡量方式：高退出步骤的继续完成率待建立基线，上线后 4 周观察
- 下一步：act_002，统计各步骤停留时长与退出率，负责人为数据分析师，截止 2026-07-20，状态 planned
- 不确定性：medium
- 未知项：unk_002
- 不确定性说明：尚未确定停留多久代表用户遇到困难

#### 假设

- asm_003：停留超过特定时长可作为用户受阻的有效信号；类型 causal；重要性 high；信心 low；证据 src_002；状态 untested

#### 风险

- risk_002：过早弹出帮助会干扰正常阅读；类别 user；可能性 medium；影响 medium；缓解措施为先使用弱提示而非弹窗；负责人为空；状态 identified

#### 验证

- val_002：验证 asm_003；方法为行为数据分析；步骤为比较停留时长、返回操作与退出行为的相关性；成功标准为找到能覆盖多数退出且误触发率可接受的候选阈值；负责人为数据分析师；截止 2026-07-20；状态 planned；结果为空

#### 关系

- rel_002：complements，目标 idea_001；可作为三步设置路径中的异常处理机制；强度 medium

## 综合分析

### 主题

- 路径缩短：idea_001；洞察为用户需要更少且与目标相关的步骤
- 阻碍解除：idea_002；洞察为帮助应在具体阻碍发生时出现

### 共识

- 不应一次展示全部设置项
- 首轮验证优先测试用户理解和行为，不直接开发完整功能

### 分歧

- 是否应要求所有用户先选择目标

### 组合机会

- 组合 idea_001 与 idea_002，在三步设置路径中加入上下文求助机制；预期形成更短且具有容错能力的首次设置流程

## 决策

- dec_001：对 idea_001 选择 validate；理由为价值潜力高，但关键可用性假设尚未验证；决策日期 2026-07-15；决策人 产品负责人
- dec_002：对 idea_002 选择 validate；理由为需先确定停留时长能否识别受阻；决策日期 2026-07-15；决策人 产品负责人

## 未知项

- unk_001：用户可接受的目标选择时长是多少；范围 idea；关联 idea_001、asm_001；重要性 high；解决方法为原型测试；负责人 用户研究员；截止 2026-07-22；状态 investigating；答案为空；证据为空
- unk_002：哪个停留时长阈值能有效识别用户受阻；范围 metric；关联 idea_002、asm_003；重要性 high；解决方法为行为数据分析；负责人 数据分析师；截止 2026-07-20；状态 open；答案为空；证据为空

## 后续行动

- act_001：制作三步设置路径可点击原型；关联 idea_001；负责人 产品设计负责人；截止 2026-07-22；状态 planned
- act_002：分析各设置步骤的停留时长与退出率；关联 idea_002；负责人 数据分析师；截止 2026-07-20；状态 planned
```

## 13. 完整可复制 YAML 示例

以下代码块本身就是一个完整 YAML 文档，复制时不需要添加文档分隔符。

```yaml
report_meta:
  id: report_20260715_01
  title: 新用户首次设置体验头脑风暴
  version: 1.0.0
  language: zh-CN
  created_at: 2026-07-15T14:30:00+08:00
  updated_at: 2026-07-15T16:00:00+08:00
  owner: 产品负责人
  status: in_review

brief:
  topic: 降低新用户首次设置过程中的退出率
  objective: 形成可在两周内验证的体验改进创意
  target_users:
    - 首次使用产品的新用户
  in_scope:
    - 移动端首次设置
    - 目标选择
    - 进度反馈
  out_of_scope:
    - 注册登录
    - 付费转化
    - 桌面端改版
  constraints:
    - 验证预算不超过 5000 元
    - 不得改变现有账号体系
  success_criteria:
    - 至少形成 2 个机制不同的创意
    - 每个保留创意都有关键假设和验证计划

sources:
  - id: src_001
    type: data
    title: 首次设置漏斗数据
    locator: analytics.onboarding_funnel_2026q2
    author_or_owner: 数据分析团队
    observed_at: 2026-07-10
    reliability: high
    notes: 包含 2026 年第二季度 18240 名新用户，未区分用户目标类型。
  - id: src_002
    type: user_research
    title: 新用户访谈
    locator: research/interviews/onboarding-01-to-08
    author_or_owner: 用户研究团队
    observed_at: 2026-07-12
    reliability: medium
    notes: 共 8 名受访者，样本规模有限。

methods:
  - id: method_001
    name: SCAMPER
    category: divergent
    purpose: 重新组合首次设置步骤并减少非必要操作
    input_source_ids:
      - src_001
      - src_002
    notes: 由产品、设计、研发共 6 人完成。

ideas:
  - id: idea_001
    name: 目标驱动三步设置
    summary: 为首次使用产品的新用户提供按目标动态生成的三步设置路径，减少无关选项，并通过即时进度反馈帮助用户完成首次设置。
    source_methods:
      source_ids:
        - src_001
        - src_002
      method_ids:
        - method_001
    problem_or_need:
      statement: 新用户不知道应优先完成哪些设置步骤，导致中途退出。
      evidence_source_ids:
        - src_001
        - src_002
      severity: high
    beneficiary:
      primary:
        - 首次使用产品的新用户
      secondary:
        - 客服团队
      context: 用户在移动端完成首次设置的前十分钟。
    core_mechanism:
      description: 根据用户首要目标匹配最短设置路径，并在每一步完成后反馈进度。
      key_steps:
        - 询问用户的首要目标
        - 匹配最短设置路径
        - 展示步骤并保存进度
      required_capabilities:
        - 目标分类规则
        - 设置进度存储
    assumptions:
      - id: asm_001
        statement: 至少 60% 的新用户能在 30 秒内选出一个首要目标。
        type: usability
        importance: high
        confidence: low
        evidence_source_ids: []
        status: untested
      - id: asm_002
        statement: 缩短设置路径会提高首次设置完成率。
        type: causal
        importance: critical
        confidence: medium
        evidence_source_ids:
          - src_001
        status: untested
    expected_value:
      user_value:
        - 降低首次设置的理解成本
        - 更快获得首次成功体验
      business_value:
        - 提高新用户激活率
        - 减少设置类客服咨询
      measurement:
        - metric: 首次设置完成率
          direction: increase
          target: 相对当前基线提升 10%
          timeframe: 上线后 4 周
        - metric: 设置类客服咨询率
          direction: decrease
          target: 相对当前基线下降 15%
          timeframe: 上线后 4 周
    risks:
      - id: risk_001
        description: 目标分类错误可能向用户展示不相关步骤。
        category: user
        likelihood: medium
        impact: high
        mitigation: 允许用户随时切换目标并返回完整设置列表。
        owner: 产品负责人
        status: identified
    validation:
      - id: val_001
        target_assumption_ids:
          - asm_001
        method: 可点击原型测试
        procedure: 招募 5 名首次使用者，让其独立选择目标并完成设置，全程记录耗时、求助和失败点。
        success_criteria:
          - 至少 4 人在 30 秒内选定目标
          - 至少 4 人无需提示完成三步设置
        owner: 用户研究员
        due_date: 2026-07-22
        status: planned
        result: null
    next_action:
      id: act_001
      description: 制作三步式设置路径的可点击原型，并招募 5 名首次使用者测试。
      owner: 产品设计负责人
      due_date: 2026-07-22
      status: planned
    relations:
      - id: rel_001
        type: complements
        target_idea_id: idea_002
        description: 即时求助可作为三步设置路径中的异常处理机制。
        strength: medium
    uncertainty:
      level: high
      unknown_ids:
        - unk_001
      notes: 用户是否愿意先回答目标问题尚无直接证据。
    status: validating

  - id: idea_002
    name: 首次设置即时求助
    summary: 在首次设置的高退出步骤提供上下文求助入口，为遇到阻碍的新用户提供短说明或转接客服。
    source_methods:
      source_ids:
        - src_001
        - src_002
      method_ids:
        - method_001
    problem_or_need:
      statement: 部分用户在专业术语较多的步骤无法继续。
      evidence_source_ids:
        - src_002
      severity: medium
    beneficiary:
      primary:
        - 首次使用产品且缺少领域知识的新用户
      secondary:
        - 客服团队
      context: 用户在设置步骤停留超过候选阈值时。
    core_mechanism:
      description: 检测长时间停留，在当前步骤展示与上下文匹配的短说明和求助入口。
      key_steps:
        - 检测停留
        - 匹配帮助内容
        - 展示说明或转接客服
      required_capabilities:
        - 停留事件采集
        - 帮助内容库
        - 客服转接
    assumptions:
      - id: asm_003
        statement: 停留超过特定时长可作为用户受阻的有效信号。
        type: causal
        importance: high
        confidence: low
        evidence_source_ids:
          - src_002
        status: untested
    expected_value:
      user_value:
        - 及时解除首次设置阻碍
      business_value:
        - 降低关键步骤退出率
      measurement:
        - metric: 高退出步骤的继续完成率
          direction: increase
          target: 待建立基线后确定目标值
          timeframe: 上线后 4 周
    risks:
      - id: risk_002
        description: 过早弹出帮助会干扰用户正常阅读。
        category: user
        likelihood: medium
        impact: medium
        mitigation: 先使用弱提示而非弹窗。
        owner: null
        status: identified
    validation:
      - id: val_002
        target_assumption_ids:
          - asm_003
        method: 行为数据分析
        procedure: 比较各步骤停留时长、返回操作、求助行为与退出行为的相关性。
        success_criteria:
          - 找到至少一个能覆盖多数退出行为的候选阈值
          - 候选阈值的预计误触发率处于团队可接受范围
        owner: 数据分析师
        due_date: 2026-07-20
        status: planned
        result: null
    next_action:
      id: act_002
      description: 统计各设置步骤的停留时长与退出率。
      owner: 数据分析师
      due_date: 2026-07-20
      status: planned
    relations:
      - id: rel_002
        type: complements
        target_idea_id: idea_001
        description: 可作为三步设置路径中的异常处理机制。
        strength: medium
    uncertainty:
      level: medium
      unknown_ids:
        - unk_002
      notes: 尚未确定停留多久代表用户遇到困难。
    status: candidate

synthesis:
  themes:
    - name: 路径缩短
      idea_ids:
        - idea_001
      insight: 用户需要更少且与目标相关的步骤。
    - name: 阻碍解除
      idea_ids:
        - idea_002
      insight: 帮助应在具体阻碍发生时出现。
  agreements:
    - 不应一次展示全部设置项。
    - 首轮验证优先测试用户理解和行为，不直接开发完整功能。
  disagreements:
    - 是否应要求所有用户先选择目标。
  combination_opportunities:
    - idea_ids:
        - idea_001
        - idea_002
      rationale: 三步设置负责缩短主路径，即时求助负责处理异常和困惑。
      proposed_result: 形成更短且具有容错能力的首次设置流程。

decisions:
  - id: dec_001
    idea_ids:
      - idea_001
    decision: validate
    rationale: 价值潜力高，但关键可用性假设尚未验证。
    decided_at: 2026-07-15
    decided_by: 产品负责人
    target_idea_id: null
  - id: dec_002
    idea_ids:
      - idea_002
    decision: validate
    rationale: 需要先确定停留时长能否识别用户受阻。
    decided_at: 2026-07-15
    decided_by: 产品负责人
    target_idea_id: null

unknowns:
  - id: unk_001
    question: 用户可接受的目标选择时长是多少？
    scope: idea
    related_ids:
      - idea_001
      - asm_001
    importance: high
    resolution_method: 通过可点击原型测试记录选择耗时和主观反馈。
    owner: 用户研究员
    due_date: 2026-07-22
    status: investigating
    answer: null
    evidence_source_ids: []
  - id: unk_002
    question: 哪个停留时长阈值能有效识别用户受阻？
    scope: metric
    related_ids:
      - idea_002
      - asm_003
    importance: high
    resolution_method: 分析停留时长与退出、返回、求助行为的相关性。
    owner: 数据分析师
    due_date: 2026-07-20
    status: open
    answer: null
    evidence_source_ids: []

next_steps:
  - id: act_001
    description: 制作三步设置路径可点击原型。
    related_idea_ids:
      - idea_001
    owner: 产品设计负责人
    due_date: 2026-07-22
    status: planned
  - id: act_002
    description: 分析各设置步骤的停留时长与退出率。
    related_idea_ids:
      - idea_002
    owner: 数据分析师
    due_date: 2026-07-20
    status: planned
```
