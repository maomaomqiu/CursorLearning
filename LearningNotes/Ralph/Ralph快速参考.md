# Ralph for Claude Code - 快速参考

> 快速查阅表，包含最常用的命令和配置

## 安装命令

```bash
# 全局安装（仅需一次）
git clone https://github.com/frankbria/ralph-claude-code.git
cd ralph-claude-code
./install.sh

# 验证安装
which ralph

# 卸载
./uninstall.sh
```

## 项目初始化

```bash
# 方案 1：在现有项目中启用 Ralph（最常用）
cd my-project
ralph-enable
ralph --monitor

# 方案 2：创建新项目
ralph-setup my-new-project
cd my-new-project
ralph --monitor

# 方案 3：导入 PRD 文档
ralph-import requirements.md my-project
cd my-project
ralph --monitor
```

## 常用命令

### 启动和监控

```bash
# 推荐方式：集成监控
ralph --monitor

# 基本启动
ralph

# 单独启动监控（需要另一个终端）
ralph-monitor

# 检查状态
ralph --status

# 详细模式
ralph --verbose --live
```

### 会话管理

```bash
# 重置会话
ralph --reset-session

# 禁用会话连续性
ralph --no-continue

# 恢复会话后继续
ralph --monitor
```

### 断路器管理

```bash
# 查看断路器状态
ralph --circuit-status

# 重置断路器
ralph --reset-circuit

# 自动重置并启动
ralph --auto-reset-circuit --monitor
```

### 自定义配置

```bash
# 自定义调用限制
ralph --calls 50

# 自定义超时（分钟）
ralph --timeout 30

# 自定义提示文件
ralph --prompt custom-prompt.md

# 组合使用
ralph --monitor --calls 50 --timeout 30 --verbose
```

## 配置文件 (.ralphrc)

```bash
# 项目基本信息
PROJECT_NAME="my-project"
PROJECT_TYPE="typescript"  # typescript/python/rust/go

# 循环控制
MAX_CALLS_PER_HOUR=100
CLAUDE_TIMEOUT_MINUTES=15
CLAUDE_OUTPUT_FORMAT="json"  # json/text

# 工具权限（根据需要调整）
ALLOWED_TOOLS="Write,Read,Edit,Bash(git *),Bash(npm *),Bash(pytest)"

# 会话管理
SESSION_CONTINUITY=true
SESSION_EXPIRY_HOURS=24

# 断路器阈值
CB_NO_PROGRESS_THRESHOLD=3      # 无进展循环次数
CB_SAME_ERROR_THRESHOLD=5       # 相同错误次数
CB_COOLDOWN_MINUTES=30          # 冷却时间（分钟）
CB_AUTO_RESET=false             # 启动时自动重置
```

## 项目结构

```
my-project/
├── .ralph/
│   ├── PROMPT.md          # 编辑：项目目标和指令
│   ├── fix_plan.md        # 编辑：任务列表
│   ├── AGENT.md           # 自动维护：构建命令
│   ├── specs/             # 编辑：详细规范
│   └── logs/              # 自动生成：执行日志
├── .ralphrc               # 编辑：项目配置
└── src/                   # 你的源代码
```

## PROMPT.md 模板

```markdown
# 项目名称

## 项目目标
[描述项目的最终目标和价值]

## 技术栈
- 语言：TypeScript
- 框架：Next.js
- 数据库：PostgreSQL
- 其他：...

## 开发原则
1. 代码质量优先
2. 充分测试
3. 清晰的文档
4. 遵循最佳实践

## 当前任务
参见 fix_plan.md 中的优先任务列表

## 不应该做的事
- 不要修改核心配置文件
- 不要删除现有测试
- 不要引入未经批准的依赖
```

## fix_plan.md 模板

```markdown
# 任务计划

## 高优先级 🔴
- [ ] 任务 1：实现核心功能
- [ ] 任务 2：添加单元测试
- [ ] 任务 3：更新文档

## 中优先级 🟡
- [ ] 任务 4：优化性能
- [ ] 任务 5：改进错误处理

## 低优先级 🟢
- [ ] 任务 6：代码重构
- [ ] 任务 7：添加注释

## 已完成 ✅
- [x] 任务 0：项目初始化
```

## tmux 快捷键

```bash
# 会话管理
tmux list-sessions        # 列出所有会话
tmux attach -t <name>     # 重新连接会话

# 在 tmux 内
Ctrl+B 然后 D            # 分离会话（保持运行）
Ctrl+B 然后 ←/→          # 切换窗格
Ctrl+B 然后 [            # 进入滚动模式（q 退出）
Ctrl+B 然后 c            # 创建新窗口
Ctrl+B 然后 n/p          # 下一个/上一个窗口
```

## 日志和调试

```bash
# 查看主日志
tail -f .ralph/logs/ralph.log

# 查看实时输出
tail -f .ralph/live.log

# 查看状态
cat .ralph/status.json | jq

# 查看会话信息
cat .ralph/.ralph_session | jq

# 查看响应分析
cat .ralph/.response_analysis | jq

# 搜索错误
grep -i error .ralph/logs/ralph.log
```

## 常见工具权限模式

```bash
# 开发模式（宽松）
ALLOWED_TOOLS="Write,Read,Edit,Bash(*)"

# 生产模式（严格）
ALLOWED_TOOLS="Write,Read,Edit"

# 前端开发
ALLOWED_TOOLS="Write,Read,Edit,Bash(npm *),Bash(git *)"

# 后端开发（Node.js）
ALLOWED_TOOLS="Write,Read,Edit,Bash(npm *),Bash(node *),Bash(git *)"

# Python 开发
ALLOWED_TOOLS="Write,Read,Edit,Bash(pip *),Bash(pytest),Bash(python *),Bash(git *)"

# 全栈开发
ALLOWED_TOOLS="Write,Read,Edit,Bash(npm *),Bash(pip *),Bash(pytest),Bash(git *)"
```

## 退出条件快查

| 条件 | 说明 | 可配置 |
|------|------|--------|
| completion_indicators >= 2 + EXIT_SIGNAL: true | 自然语言完成信号 + Claude 确认 | 否 |
| fix_plan.md 全部完成 | 所有任务都标记为完成 | 通过编辑 fix_plan.md |
| 连续 done 信号 >= 2 | Claude 连续说"完成" | MAX_CONSECUTIVE_DONE_SIGNALS |
| 连续测试循环 >= 3 | 太多测试导向的循环 | MAX_CONSECUTIVE_TEST_LOOPS |
| 5 小时 API 限制 | Claude API 使用限制 | 否（系统限制） |
| 断路器 OPEN | 检测到问题（无进展/错误） | CB_*_THRESHOLD |

## 断路器状态

| 状态 | 说明 | 行为 |
|------|------|------|
| CLOSED | 正常运行 | 继续执行 |
| HALF_OPEN | 监控恢复 | 谨慎执行，检查进展 |
| OPEN | 停止执行 | 需要手动或等待自动恢复 |

## 错误处理流程

```bash
# 1. 查看错误日志
tail -n 50 .ralph/logs/ralph.log | grep -i error

# 2. 检查断路器状态
ralph --circuit-status

# 3. 查看会话状态
cat .ralph/.ralph_session | jq

# 4. 尝试解决
#    - 如果是权限问题：编辑 .ralphrc 的 ALLOWED_TOOLS
#    - 如果是断路器：ralph --reset-circuit
#    - 如果是会话：ralph --reset-session

# 5. 重新启动
ralph --monitor
```

## 环境变量（高级）

```bash
# 在启动前设置环境变量
export CB_NO_PROGRESS_THRESHOLD=5
export CB_COOLDOWN_MINUTES=15
export CLAUDE_TIMEOUT_MINUTES=20
ralph --monitor

# 或在 .ralphrc 中永久设置
echo "CB_NO_PROGRESS_THRESHOLD=5" >> .ralphrc
```

## 测试命令

```bash
# 运行所有测试
npm test

# 运行单元测试
npm run test:unit

# 运行集成测试
npm run test:integration

# 运行特定测试文件
bats tests/unit/test_cli_parsing.bats

# 详细输出
bats -t tests/unit/test_cli_parsing.bats
```

## 快速故障排除

| 问题 | 可能原因 | 解决方案 |
|------|---------|---------|
| 过早退出 | 退出阈值太低 | 调整 .ralphrc 阈值 |
| 卡在错误 | 断路器打开 | `ralph --reset-circuit` |
| 权限被拒 | ALLOWED_TOOLS 不足 | 编辑 .ralphrc 添加工具 |
| 会话过期 | 超过 24 小时 | `ralph --reset-session` |
| timeout 未找到 | macOS 缺少 coreutils | `brew install coreutils` |
| tmux 连接失败 | 会话已存在 | `tmux attach -t <name>` |

## 性能优化建议

```bash
# 快速迭代（开发阶段）
ralph --calls 20 --timeout 5 --monitor

# 深度工作（完善阶段）
ralph --calls 100 --timeout 30 --monitor

# 调试模式（问题排查）
ralph --verbose --live --timeout 10

# 无人值守（长时间运行）
ralph --auto-reset-circuit --calls 200 --timeout 60 --monitor
```

## 迁移和升级

```bash
# 从旧结构迁移到 .ralph/ 文件夹
cd old-project
ralph-migrate

# 更新 Ralph 本身
cd ralph-claude-code
git pull origin main
./install.sh
```

## 有用的别名

```bash
# 添加到 ~/.bashrc 或 ~/.zshrc
alias rm='ralph --monitor'
alias rs='ralph --status'
alias rr='ralph --reset-circuit --monitor'
alias rv='ralph --verbose --live'
alias rl='tail -f .ralph/logs/ralph.log'
```

## 资源链接

- GitHub：https://github.com/frankbria/ralph-claude-code
- Issues：https://github.com/frankbria/ralph-claude-code/issues
- Claude Code：https://claude.ai/code
- Ralph 技术：https://ghuntley.com/ralph/

---

**提示**：将此文档保存在项目的 `.ralph/docs/` 目录中，方便随时查阅！
