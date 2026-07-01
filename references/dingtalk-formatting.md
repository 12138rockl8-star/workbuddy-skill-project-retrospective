# 钉钉文档格式化与美化指南

本指南介绍如何通过 Markdown 格式生成美观的钉钉文档，以及如何使用 dws CLI 创建和编辑文档。

## 一、dws CLI 文档操作命令

### 创建文档

```bash
# 方式1：直接传内容（适合短文档）
dws doc create \
  --name "文档标题" \
  --content "# 标题\n\n正文内容" \
  --content-format markdown \
  --format json

# 方式2：从文件读取（适合长文档，推荐）
dws doc create \
  --name "文档标题" \
  --content-file /path/to/content.md \
  --content-format markdown \
  --format json
```

### 追加内容

```bash
# 向已有文档追加内容
dws doc update \
  --node <DOC_ID或URL> \
  --content-file /path/to/append.md \
  --content-format markdown \
  --mode append \
  --format json
```

### 覆盖内容（谨慎使用）

```bash
# 完全替换文档内容
dws doc update \
  --node <DOC_ID或URL> \
  --content-file /path/to/content.md \
  --content-format markdown \
  --mode overwrite \
  --format json
```

### 块级编辑

```bash
# 查看文档块结构
dws doc block list --node <DOC_ID> --format json

# 插入段落
dws doc block insert --node <DOC_ID> --text "段落内容"

# 插入标题
dws doc block insert --node <DOC_ID> --heading "二级标题" --level 2

# 更新块内容
dws doc block update --node <DOC_ID> --block-id <BLOCK_ID> --text "新内容"
```

## 二、Markdown 格式在钉钉中的渲染

### 标题层级

```markdown
# 一级标题（文档标题，大号加粗）
## 二级标题（章节标题，如"一、KPI达成情况"）
### 三级标题（子章节，如"（一）整体数据概览"）
#### 四级标题（小节，如"优质案例分析"）
```

### 表格

钉钉支持标准 Markdown 表格，会自动渲染为美观的表格：

```markdown
| 指标 | 目标值 | 实际达成 | 达成率 |
|------|--------|---------|--------|
| 播放量 | 100w | 120w | 120% |
| 涨粉 | 5w | 6.2w | 124% |
```

### 引用块

用于重点提示或复盘逻辑陈述：

```markdown
> **复盘逻辑**：肯定前期声量破圈成绩 → 科学解释无效留资原因 → 给出下半年精准转化方案
```

### 分隔线

用于大章节之间的分隔：

```markdown
---
```

### 列表

```markdown
<!-- 无序列表 -->
- 内容运营：发布19条视频
- 投流策略：DOU+投放3.4万元
- 社群运营：拉新587人次

<!-- 有序列表 -->
1. 社群拉新：设置阶梯制福利激励
2. UGC征集：群内话题发起+福利引导
3. 品牌联动：皮肤设计、周边名征集
```

### 加粗与行内代码

```markdown
总播放量 **100w**，环比增长 **23%**
关键指标 `CPE 0.08` 对比上月降低0.06
```

## 三、长文档分批写入策略

当报告内容较长时，分批写入避免超限：

### 步骤1：创建文档并写入前半部分

将报告拆分为多个 Markdown 文件：
- `part1.md`：标题 + KPI达成情况 + 核心运营解读
- `part2.md`：策略调整 + 问题优化 + 下阶段规划 + 总结展望

```bash
# 创建文档并写入第一部分
dws doc create \
  --name "【Q3】项目复盘报告" \
  --content-file /tmp/part1.md \
  --content-format markdown \
  --format json
# 从返回中提取 nodeId
```

### 步骤2：追加后续部分

```bash
# 追加第二部分
dws doc update \
  --node <nodeId> \
  --content-file /tmp/part2.md \
  --content-format markdown \
  --mode append \
  --format json
```

### 步骤3：如需更多部分，继续追加

```bash
dws doc update \
  --node <nodeId> \
  --content-file /tmp/part3.md \
  --content-format markdown \
  --mode append \
  --format json
```

## 四、Markdown 换行注意事项

**关键**：写入钉钉文档时，Markdown 中的换行必须是**真实的换行符**（Unicode U+000A），而不是字面量字符串 `\n`（反斜杠加字母n）。

- 正确：文件中包含真实的换行
- 错误：文件中包含字面量 `\n` 字符串

如果通过程序生成 Markdown 文件，确保：
1. 使用真实换行符写入文件
2. 不要将 `\n` 作为字符串字面量写入
3. 使用 Write 工具或文件写入时，内容中的换行应使用实际换行

## 五、钉钉文档美化最佳实践

### 1. 文档标题规范
```
【{周期}】【{项目名称}】复盘报告
```
示例：`【25年Q3】【蚂蚁森林官抖】复盘报告`

### 2. 文档头部信息块
在标题后紧跟元信息：
```markdown
**复盘周期**：2025.07.01 - 2025.09.30
**整理人**：张三
**适用对象**：项目组全体成员
```

### 3. 复盘逻辑引言
用引用块突出复盘逻辑：
```markdown
> **复盘逻辑**：肯定前期成绩 → 科学解释问题 → 给出优化方案
```

### 4. 数据概览表格
使用表格呈现核心KPI，一目了然：
```markdown
| 核心指标 | 目标 | 实际 | 达成率 | 环比 |
|---------|------|------|--------|------|
| 播放量 | 800w | 800w | 100% | +23% |
| 涨粉 | 8w | 8.2w | 102% | +95% |
```

### 5. 案例分析格式
每个案例使用统一格式：
```markdown
#### 优质案例：《案例名称》

| 指标 | 数据 | 对比条均 |
|------|------|---------|
| 完播率 | **26.2%** | 7.9% |
| 5s完播率 | **51.8%** | 29.4% |

**分析&归因**：原因分析
**Learning**：可复用经验
```

### 6. 前后对比表格
```markdown
| 维度 | 优化前 | 优化后 |
|------|--------|--------|
| 反馈处理 | 看见没动作 | 第一时间回复 |
| 需求理解 | "太丑"不知怎么改 | 明确拆解维度 |
| 客户信任 | 质疑不专业 | 有方法能落地 |
```

### 7. 问题-归因-优化表
```markdown
| 问题 | 归因分析 | 优化方向 |
|------|---------|---------|
| 完播率下滑 | 传播内容占比过大 | 控制时长，优化开头 |
| 进房率下降 | 发布频率太高 | 降低条数，提升质量 |
```

### 8. 行动计划表
```markdown
| 时间 | 内容 | 目标 | 负责人 |
|------|------|------|--------|
| 10月第1周 | 栏目调整 | 常规:传播=6:4 | PM |
| 10月第2周 | 热点机制 | 每周1次热点提报 | 内容组 |
```

### 9. emoji使用建议
适度使用，不要每行都有：
- 章节标题：不需要emoji
- 关键步骤：📍
- 正面结果：✅
- 负面问题：❌
- 数据相关：📊
- 警告提醒：⚠️
- 时间节点：📅
- 执行启动：🚀

### 10. 分隔线使用
在大章节之间使用 `---` 分隔，增强结构感：
```markdown
## 一、KPI达成情况

内容...

---

## 二、核心运营解读

内容...
```
