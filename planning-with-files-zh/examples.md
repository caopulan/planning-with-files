# 示例：文件化规划的实际使用

## 目录约定

每个任务开始前：
1. 创建 `agent_planning_with_files_cache/`
2. 如有 `.gitignore`，加入 `agent_planning_with_files_cache/`
3. 在该目录下创建任务子目录 `<YYYYMMDD>-<HHMM>-<short-task>`

以下示例会注明各自的任务目录（TASK_DIR）。

## 示例 1：研究任务

**用户请求：** “研究晨间运动的益处并写一份摘要”

**任务目录（TASK_DIR）：** `agent_planning_with_files_cache/20250103-0930-morning-exercise`

### 循环 1：创建计划
```bash
写入 TASK_DIR/task_plan.md
```

```markdown
# 任务计划：晨间运动益处研究

## 目标
撰写晨间运动益处的研究摘要。

## 阶段
- [ ] 阶段 1：创建此计划 ✓
- [ ] 阶段 2：搜索并收集来源
- [ ] 阶段 3：综合结论
- [ ] 阶段 4：交付摘要

## 关键问题
1. 有哪些身体健康益处？
2. 有哪些心理健康益处？
3. 有哪些科学研究支持？

## 状态
**当前处于阶段 1** - 正在创建计划
```

### 循环 2：研究
```bash
读取 TASK_DIR/task_plan.md           # 刷新目标
WebSearch "晨间运动益处"
写入 TASK_DIR/notes.md              # 保存发现
编辑 TASK_DIR/task_plan.md           # 标记阶段 2 完成
```

### 循环 3：综合
```bash
读取 TASK_DIR/task_plan.md           # 刷新目标
读取 TASK_DIR/notes.md               # 获取发现
写入 TASK_DIR/morning_exercise_summary.md
编辑 TASK_DIR/task_plan.md           # 标记阶段 3 完成
```

### 循环 4：交付
```bash
读取 TASK_DIR/task_plan.md           # 确认完成
交付 TASK_DIR/morning_exercise_summary.md
```

---

## 示例 2：修复 Bug 任务

**用户请求：** “修复认证模块中的登录 Bug”

**任务目录（TASK_DIR）：** `agent_planning_with_files_cache/20250103-1015-fix-login-bug`

### task_plan.md
```markdown
# 任务计划：修复登录 Bug

## 目标
定位并修复阻止成功登录的缺陷。

## 阶段
- [x] 阶段 1：理解缺陷报告 ✓
- [x] 阶段 2：定位相关代码 ✓
- [ ] 阶段 3：定位根因（当前）
- [ ] 阶段 4：实现修复
- [ ] 阶段 5：测试与验证

## 关键问题
1. 出现了什么错误信息？
2. 哪个文件处理认证？
3. 最近有哪些改动？

## 已做决策
- 认证处理器位于 src/auth/login.ts
- 错误发生在 validateToken() 函数

## 遇到的错误
- [初始] TypeError: Cannot read property 'token' of undefined
  → 根因：user 对象未正确 await

## 状态
**当前处于阶段 3** - 已找到根因，准备修复
```

---

## 示例 3：功能开发

**用户请求：** “在设置页添加深色模式开关”

**任务目录（TASK_DIR）：** `agent_planning_with_files_cache/20250103-1100-dark-mode-toggle`

### 三文件模式实战

**task_plan.md：**
```markdown
# 任务计划：深色模式开关

## 目标
在设置中添加可用的深色模式开关。

## 阶段
- [x] 阶段 1：研究现有主题系统 ✓
- [x] 阶段 2：设计实现方案 ✓
- [ ] 阶段 3：实现开关组件（当前）
- [ ] 阶段 4：添加主题切换逻辑
- [ ] 阶段 5：测试与润色

## 已做决策
- 主题使用 CSS 自定义属性
- 偏好存入 localStorage
- 开关组件在 SettingsPage.tsx

## 状态
**当前处于阶段 3** - 正在构建开关组件
```

**notes.md：**
```markdown
# 笔记：深色模式实现

## 现有主题系统
- 位置：src/styles/theme.ts
- 使用：CSS 自定义属性
- 当前主题：仅 light

## 需要修改的文件
1. src/styles/theme.ts - 添加深色主题颜色
2. src/components/SettingsPage.tsx - 添加开关
3. src/hooks/useTheme.ts - 创建新 hook
4. src/App.tsx - 用 ThemeProvider 包裹

## 颜色决策
- 深色背景：#1a1a2e
- 深色表面：#16213e
- 深色文字：#eaeaea
```

**dark_mode_implementation.md：**（交付物）
```markdown
# 深色模式实现

## 已完成的更改

### 1. 新增深色主题颜色
文件：src/styles/theme.ts
...

### 2. 创建 useTheme hook
文件：src/hooks/useTheme.ts
...
```

---

## 示例 4：错误恢复模式

示例中的计划文件位于 `TASK_DIR/task_plan.md`。

当出现失败时，不要隐藏：

### 之前（错误）
```
操作：读取 config.json
错误：未找到文件
操作：读取 config.json  # 静默重试
操作：读取 config.json  # 再次重试
```

### 之后（正确）
```
操作：读取 config.json
错误：未找到文件

# 更新 TASK_DIR/task_plan.md：
## 遇到的错误
- config.json 未找到 → 将创建默认 config

操作：写入 config.json（默认配置）
操作：读取 config.json
成功！
```

---

## 先读再决定模式

**重大决策前始终阅读计划：**

```
[已经发生了很多工具调用...]
[上下文越来越长...]
[原始目标可能已被遗忘...]

→ 读取 TASK_DIR/task_plan.md          # 把目标带回注意力！
→ 现在再做决定                         # 目标在上下文中是最新的
```

这就是 Manus 能在 ~50 次工具调用后仍不迷失的原因。计划文件充当“目标刷新”的机制。
