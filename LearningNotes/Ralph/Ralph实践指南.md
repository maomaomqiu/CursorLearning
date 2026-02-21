# Ralph for Claude Code - 实践指南

> 实战教程：从零开始使用 Ralph 进行自主开发

## 目录

1. [第一次使用 Ralph](#第一次使用-ralph)
2. [实战案例：创建 Todo App](#实战案例创建-todo-app)
3. [实战案例：重构现有项目](#实战案例重构现有项目)
4. [进阶技巧](#进阶技巧)
5. [团队协作](#团队协作)
6. [问题诊断](#问题诊断)

---

## 第一次使用 Ralph

### 步骤 1：安装和验证

```bash
# 1. 克隆并安装
git clone https://github.com/frankbria/ralph-claude-code.git
cd ralph-claude-code
./install.sh

# 2. 验证安装
which ralph                      # 应该输出：~/.local/bin/ralph
ralph --help                     # 查看帮助信息
ralph --status                   # 检查系统状态

# 3. 检查依赖
which tmux                       # 终端复用器
which jq                         # JSON 处理器
which claude                     # Claude Code CLI
which git                        # 版本控制
```

### 步骤 2：选择合适的初始化方式

#### 场景 A：你有一个现有项目（推荐新手）

```bash
cd ~/projects/my-existing-app
ralph-enable
```

这会：
1. 检测项目类型（TypeScript/Python/等）
2. 询问任务来源（beads/GitHub Issues/PRD）
3. 生成 `.ralph/` 目录和配置文件
4. 创建初始任务列表

#### 场景 B：你想创建全新项目

```bash
ralph-setup my-awesome-app
cd my-awesome-app
```

这会创建一个空白项目结构，你需要：
1. 编辑 `.ralph/PROMPT.md` 定义项目目标
2. 编辑 `.ralph/fix_plan.md` 列出初始任务

#### 场景 C：你有一个 PRD/需求文档

```bash
ralph-import ~/documents/project-requirements.md my-app
cd my-app
```

这会自动将 PRD 转换为 Ralph 格式。

### 步骤 3：理解生成的文件

```bash
cd .ralph
ls -la
```

你会看到：
- `PROMPT.md` - **必须审查和编辑**
- `fix_plan.md` - **必须审查和编辑**
- `AGENT.md` - 通常不需要编辑
- `specs/` - 可选：添加详细规范
- `logs/` - 自动生成的日志

### 步骤 4：自定义 PROMPT.md

```bash
# 编辑主提示文件
vim .ralph/PROMPT.md
```

确保包含：
- ✅ 清晰的项目目标
- ✅ 技术栈说明
- ✅ 代码规范和约定
- ✅ 明确的边界（做什么/不做什么）

### 步骤 5：设置任务优先级

```bash
# 编辑任务计划
vim .ralph/fix_plan.md
```

使用清晰的结构：
```markdown
## 高优先级 🔴
- [ ] 必须先完成的核心任务

## 中优先级 🟡
- [ ] 重要但不紧急的任务

## 低优先级 🟢
- [ ] 可以稍后完成的任务
```

### 步骤 6：启动 Ralph！

```bash
# 推荐：使用监控模式
ralph --monitor

# 这会：
# 1. 启动 tmux 会话
# 2. 左侧显示 Ralph 循环
# 3. 右侧显示实时监控
```

### 步骤 7：观察和学习

在 Ralph 运行时：
- 观察日志输出
- 注意任务完成情况
- 检查生成的代码
- 学习 Ralph 的工作模式

---

## 实战案例：创建 Todo App

### 场景描述
创建一个全栈 Todo 应用，使用 Next.js + TypeScript + PostgreSQL。

### 步骤 1：初始化项目

```bash
ralph-setup todo-app-fullstack
cd todo-app-fullstack
```

### 步骤 2：编写 PROMPT.md

```bash
cat > .ralph/PROMPT.md << 'EOF'
# Full-Stack Todo Application

## 项目目标
创建一个现代化的全栈 Todo 应用，具有以下特性：
- 用户认证（注册/登录）
- CRUD 操作（创建/读取/更新/删除任务）
- 任务分类和标签
- 响应式设计
- 完整的测试覆盖

## 技术栈
- **前端**：Next.js 14, TypeScript, Tailwind CSS
- **后端**：Next.js API Routes
- **数据库**：PostgreSQL with Prisma ORM
- **认证**：NextAuth.js
- **测试**：Jest, React Testing Library

## 开发原则
1. 先实现核心功能，再添加增强功能
2. 每个功能都要有单元测试
3. 代码要有类型安全（TypeScript strict mode）
4. API 路由要有错误处理
5. 组件要可复用

## 架构要求
- 使用 App Router（Next.js 14）
- 组件目录结构：`src/components/[category]/[ComponentName].tsx`
- API 路由：`src/app/api/[resource]/route.ts`
- 数据库模型：`prisma/schema.prisma`
- 工具函数：`src/lib/[utility].ts`

## 不应该做的事
- ❌ 不要使用 Pages Router（使用 App Router）
- ❌ 不要跳过错误处理
- ❌ 不要创建未测试的代码
- ❌ 不要使用 any 类型

## 参考 fix_plan.md 获取具体任务
EOF
```

### 步骤 3：编写 fix_plan.md

```bash
cat > .ralph/fix_plan.md << 'EOF'
# Todo App 开发计划

## 阶段 1：项目基础 🔴
- [ ] 初始化 Next.js 14 项目（TypeScript + Tailwind）
- [ ] 配置 ESLint 和 Prettier
- [ ] 设置 Prisma with PostgreSQL
- [ ] 创建基础目录结构
- [ ] 配置环境变量（.env.example）

## 阶段 2：数据库和认证 🔴
- [ ] 设计数据库模型（User, Todo, Tag）
- [ ] 创建 Prisma migrations
- [ ] 配置 NextAuth.js
- [ ] 实现注册 API endpoint
- [ ] 实现登录 API endpoint
- [ ] 创建认证中间件

## 阶段 3：Todo CRUD API 🔴
- [ ] POST /api/todos - 创建任务
- [ ] GET /api/todos - 列出任务
- [ ] GET /api/todos/[id] - 获取单个任务
- [ ] PUT /api/todos/[id] - 更新任务
- [ ] DELETE /api/todos/[id] - 删除任务
- [ ] 为所有 API 添加单元测试

## 阶段 4：前端组件 🟡
- [ ] 创建布局组件（Layout, Header, Sidebar）
- [ ] 创建认证表单（LoginForm, RegisterForm）
- [ ] 创建 TodoList 组件
- [ ] 创建 TodoItem 组件
- [ ] 创建 TodoForm（创建/编辑）
- [ ] 创建 TagManager 组件

## 阶段 5：功能集成 🟡
- [ ] 实现任务创建流程
- [ ] 实现任务编辑流程
- [ ] 实现任务删除（带确认）
- [ ] 实现任务标记完成
- [ ] 实现任务过滤（全部/进行中/已完成）
- [ ] 实现标签功能

## 阶段 6：测试和优化 🟢
- [ ] 前端组件测试（React Testing Library）
- [ ] API 集成测试
- [ ] E2E 测试（Playwright）
- [ ] 性能优化（React.memo, useMemo）
- [ ] 添加加载状态和错误处理
- [ ] 响应式设计优化

## 阶段 7：文档和部署 🟢
- [ ] 编写 README.md
- [ ] 添加 API 文档
- [ ] 配置 Vercel 部署
- [ ] 设置 CI/CD（GitHub Actions）
EOF
```

### 步骤 4：配置 .ralphrc

```bash
cat > .ralphrc << 'EOF'
PROJECT_NAME="todo-app-fullstack"
PROJECT_TYPE="typescript"

# 前端开发需要较多 npm 命令
MAX_CALLS_PER_HOUR=150
CLAUDE_TIMEOUT_MINUTES=20

ALLOWED_TOOLS="Write,Read,Edit,Bash(git *),Bash(npm *),Bash(npx *),Bash(prisma *)"

SESSION_CONTINUITY=true
SESSION_EXPIRY_HOURS=24

# 适当放宽阈值，因为全栈项目较复杂
CB_NO_PROGRESS_THRESHOLD=5
CB_SAME_ERROR_THRESHOLD=7
EOF
```

### 步骤 5：启动 Ralph

```bash
# 使用监控模式启动
ralph --monitor

# 或者使用详细模式（用于学习）
ralph --verbose --live --monitor
```

### 步骤 6：监控进度

在另一个终端：
```bash
# 查看实时日志
tail -f .ralph/logs/ralph.log

# 检查任务进度
cat .ralph/fix_plan.md | grep -E "^\- \[(x| )\]"

# 查看生成的文件
find src -type f -name "*.ts" -o -name "*.tsx"
```

### 步骤 7：中间干预（如果需要）

```bash
# 如果 Ralph 偏离方向，可以：
# 1. Ctrl+C 停止 Ralph
# 2. 编辑 .ralph/PROMPT.md 或 fix_plan.md
# 3. 重新启动
ralph --reset-session --monitor
```

---

## 实战案例：重构现有项目

### 场景描述
你有一个现有的 React 项目，想要：
1. 升级到 TypeScript
2. 添加测试
3. 重构为现代架构

### 步骤 1：备份现有项目

```bash
cd ~/projects/legacy-react-app
git checkout -b refactor-with-ralph
git commit -am "Checkpoint before Ralph refactor"
```

### 步骤 2：启用 Ralph

```bash
ralph-enable --force
```

### 步骤 3：定义重构目标

```bash
cat > .ralph/PROMPT.md << 'EOF'
# Legacy React App Refactoring

## 重构目标
逐步将现有 JavaScript React 应用重构为 TypeScript，并添加测试。

## 当前状态
- React 17 with JavaScript
- 无类型检查
- 无测试
- 混乱的文件结构

## 目标状态
- React 18 with TypeScript
- 100% 类型覆盖
- >= 80% 测试覆盖
- 清晰的目录结构

## 重构策略
1. **渐进式迁移**：一次迁移一个模块
2. **保持可用性**：确保每次提交后应用仍可运行
3. **测试先行**：为每个重构的模块添加测试
4. **重构顺序**：
   - 工具函数 → 数据模型 → UI 组件
   - 叶子组件 → 容器组件 → 页面组件

## 迁移原则
- 使用 TypeScript strict mode
- 为所有组件添加 PropTypes 或 TypeScript 接口
- 使用现代 React hooks（避免 class components）
- 拆分大型组件为小型可复用组件

## 不要做的事
- ❌ 不要一次性重写整个应用
- ❌ 不要删除现有功能
- ❌ 不要改变 API 合约
- ❌ 不要跳过测试
EOF
```

### 步骤 4：制定详细计划

```bash
cat > .ralph/fix_plan.md << 'EOF'
# 重构计划

## 阶段 1：环境准备 🔴
- [ ] 添加 TypeScript 依赖和配置
- [ ] 配置 tsconfig.json（strict mode）
- [ ] 添加测试框架（Jest + Testing Library）
- [ ] 配置 ESLint for TypeScript
- [ ] 更新 package.json scripts

## 阶段 2：核心工具函数迁移 🔴
- [ ] src/utils/api.js → api.ts
- [ ] src/utils/validation.js → validation.ts
- [ ] src/utils/formatting.js → formatting.ts
- [ ] 为工具函数添加单元测试
- [ ] 验证现有代码仍正常工作

## 阶段 3：数据模型和类型定义 🔴
- [ ] 创建 src/types/user.ts
- [ ] 创建 src/types/product.ts
- [ ] 创建 src/types/api.ts
- [ ] 定义共享接口和类型别名

## 阶段 4：叶子组件迁移 🟡
- [ ] Button.jsx → Button.tsx
- [ ] Input.jsx → Input.tsx
- [ ] Card.jsx → Card.tsx
- [ ] Modal.jsx → Modal.tsx
- [ ] 为每个组件添加测试

## 阶段 5：容器组件迁移 🟡
- [ ] UserList.jsx → UserList.tsx
- [ ] ProductGrid.jsx → ProductGrid.tsx
- [ ] ShoppingCart.jsx → ShoppingCart.tsx
- [ ] 添加组件集成测试

## 阶段 6：页面组件迁移 🟡
- [ ] HomePage.jsx → HomePage.tsx
- [ ] ProductPage.jsx → ProductPage.tsx
- [ ] CheckoutPage.jsx → CheckoutPage.tsx
- [ ] 添加页面级测试

## 阶段 7：状态管理迁移 🟢
- [ ] 迁移 Redux store 到 TypeScript
- [ ] 类型化 actions 和 reducers
- [ ] 添加 store 测试

## 阶段 8：最终清理 🟢
- [ ] 删除所有 .jsx 文件
- [ ] 更新所有导入路径
- [ ] 运行完整测试套件
- [ ] 更新文档
EOF
```

### 步骤 5：配置工具权限

```bash
cat > .ralphrc << 'EOF'
PROJECT_NAME="legacy-react-app"
PROJECT_TYPE="typescript"

# 重构项目需要更多调用和时间
MAX_CALLS_PER_HOUR=200
CLAUDE_TIMEOUT_MINUTES=30

ALLOWED_TOOLS="Write,Read,Edit,Bash(git *),Bash(npm *),Bash(npx *)"

# 启用会话连续性以保持上下文
SESSION_CONTINUITY=true

# 重构项目可能会有反复，放宽阈值
CB_NO_PROGRESS_THRESHOLD=7
CB_SAME_ERROR_THRESHOLD=10
CB_COOLDOWN_MINUTES=15
EOF
```

### 步骤 6：分批执行

```bash
# 不要一次性运行所有任务
# 而是分阶段运行

# 第一次：环境准备和工具函数
ralph --monitor --calls 50

# 检查结果并提交
git add .
git commit -m "refactor: setup TypeScript and migrate utils"

# 第二次：数据模型和叶子组件
ralph --monitor --calls 50

# 继续这个循环...
```

---

## 进阶技巧

### 技巧 1：使用 specs/ 目录

对于复杂功能，在 `.ralph/specs/` 中添加详细规范：

```bash
mkdir -p .ralph/specs

cat > .ralph/specs/authentication.md << 'EOF'
# 认证系统规范

## 用户注册
- Email 验证（必须是有效邮箱格式）
- 密码强度：至少 8 字符，包含大小写字母和数字
- 成功后发送验证邮件

## 登录流程
- 支持邮箱/密码登录
- 支持 OAuth（Google, GitHub）
- 记住我选项（30 天）
- 失败 5 次后锁定账户 15 分钟

## JWT Token
- Access token 有效期：15 分钟
- Refresh token 有效期：7 天
- 存储在 httpOnly cookie 中
EOF
```

### 技巧 2：使用 stdlib/ 存储可重用模式

```bash
mkdir -p .ralph/specs/stdlib

cat > .ralph/specs/stdlib/error-handling.md << 'EOF'
# 错误处理标准

## API 错误响应格式
```typescript
interface ApiError {
  code: string;        // ERROR_CODE_NAME
  message: string;     // 用户友好消息
  details?: any;       // 详细错误信息（开发环境）
  timestamp: string;   // ISO 8601 格式
}
```

## 错误处理模式
```typescript
try {
  // 业务逻辑
} catch (error) {
  if (error instanceof ValidationError) {
    return res.status(400).json({
      code: 'VALIDATION_ERROR',
      message: error.message
    });
  }
  // ... 其他错误类型
  throw error; // 未知错误向上抛出
}
```
EOF
```

### 技巧 3：动态调整任务优先级

```bash
# 在 Ralph 运行过程中，你可以：
# 1. Ctrl+C 暂停 Ralph
# 2. 编辑 fix_plan.md，调整任务顺序或添加新任务
# 3. 重新启动（保持会话）
ralph --monitor
```

### 技巧 4：使用多个 PROMPT 文件

```bash
# 为不同阶段创建不同的 prompt
cp .ralph/PROMPT.md .ralph/PROMPT-development.md
cp .ralph/PROMPT.md .ralph/PROMPT-testing.md
cp .ralph/PROMPT.md .ralph/PROMPT-optimization.md

# 使用特定 prompt
ralph --prompt .ralph/PROMPT-testing.md --monitor
```

### 技巧 5：自定义退出条件

```bash
# 在 .ralphrc 中调整阈值
cat >> .ralphrc << 'EOF'

# 更激进的退出（快速原型）
MAX_CONSECUTIVE_TEST_LOOPS=2
MAX_CONSECUTIVE_DONE_SIGNALS=1

# 或更保守的退出（深度工作）
MAX_CONSECUTIVE_TEST_LOOPS=5
MAX_CONSECUTIVE_DONE_SIGNALS=3
EOF
```

---

## 团队协作

### 场景 1：多人共享 Ralph 项目

```bash
# 1. 将 Ralph 配置提交到 Git
git add .ralph/ .ralphrc
git commit -m "docs: add Ralph configuration"
git push

# 2. 团队成员克隆后：
git clone <repo-url>
cd <repo>

# 3. 确保每个人都安装了 Ralph
ralph --status

# 4. 各自运行自己的 Ralph 实例
ralph --monitor
```

### 场景 2：协调任务分配

在 `fix_plan.md` 中使用标签：

```markdown
## 高优先级
- [ ] @alice 实现用户认证
- [ ] @bob 设计数据库模型
- [ ] @charlie 创建 API 端点

## 进行中
- [~] @alice 正在实现登录功能
```

### 场景 3：代码审查 + Ralph

```bash
# 1. 创建功能分支
git checkout -b feature/user-auth

# 2. 让 Ralph 实现功能
ralph --monitor --calls 50

# 3. 审查 Ralph 的更改
git diff main...feature/user-auth

# 4. 如果需要调整：
#    - 编辑 PROMPT.md 添加约束
#    - 或手动修改代码
#    - 重新运行 Ralph

# 5. 创建 PR
git push origin feature/user-auth
gh pr create --title "feat: implement user authentication"
```

---

## 问题诊断

### 问题 1：Ralph 一直在修复相同的错误

**症状**：
```
Loop 5: TypeError: Cannot read property 'x' of undefined
Loop 6: TypeError: Cannot read property 'x' of undefined
Loop 7: TypeError: Cannot read property 'x' of undefined
```

**诊断**：
```bash
# 1. 查看断路器状态
ralph --circuit-status

# 2. 查看错误历史
grep -A 5 "TypeError" .ralph/logs/ralph.log | tail -30
```

**解决**：
```bash
# 方案 1：在 PROMPT.md 中添加具体指导
cat >> .ralph/PROMPT.md << 'EOF'

## 当前问题
TypeError: Cannot read property 'x' of undefined

### 解决方案
- 在访问嵌套属性前使用可选链：`obj?.x`
- 或添加空值检查：`if (obj && obj.x)`
EOF

# 方案 2：重置断路器并明确任务
ralph --reset-circuit --monitor
```

### 问题 2：Ralph 完成任务太快但质量不高

**症状**：
- 所有任务都标记完成
- 但代码缺少测试
- 或实现很粗糙

**解决**：
```bash
# 1. 在 PROMPT.md 中提高质量标准
cat >> .ralph/PROMPT.md << 'EOF'

## 质量标准（严格执行）
- ✅ 每个函数都必须有单元测试
- ✅ 测试覆盖率必须 >= 80%
- ✅ 所有 API 端点必须有错误处理
- ✅ 代码必须通过 ESLint 检查
- ✅ 提交前必须运行 `npm test` 并通过

## 完成的定义（Definition of Done）
一个任务只有在满足以下条件时才算完成：
1. 代码实现正确且符合需求
2. 有对应的单元测试且通过
3. 代码通过 linting
4. 更新了相关文档
5. 在 fix_plan.md 中标记为 [x]
EOF

# 2. 在 fix_plan.md 中添加质量检查任务
cat >> .ralph/fix_plan.md << 'EOF'

## 质量保证检查
- [ ] 运行完整测试套件并确保通过
- [ ] 检查测试覆盖率 >= 80%
- [ ] 运行 ESLint 并修复所有问题
- [ ] 审查所有 TODO 注释并处理
EOF
```

### 问题 3：Ralph 说已完成但实际还有很多工作

**症状**：
```
Loop 8: EXIT_SIGNAL: true
但 fix_plan.md 中还有很多 [ ] 未完成
```

**诊断**：
```bash
# 查看响应分析
cat .ralph/.response_analysis | jq

# 查看未完成任务
grep "^\- \[ \]" .ralph/fix_plan.md
```

**解决**：
```bash
# 1. 在 PROMPT.md 中添加明确指令
cat >> .ralph/PROMPT.md << 'EOF'

## 退出条件
❌ 不要在以下情况下设置 EXIT_SIGNAL: true：
- fix_plan.md 中还有未完成的任务
- 存在 TODO 或 FIXME 注释
- 测试未通过
- 有编译错误或警告

✅ 只有在以下情况下才设置 EXIT_SIGNAL: true：
- fix_plan.md 中所有任务都标记为 [x]
- 所有测试通过
- 代码质量检查通过
- 项目可以成功构建和运行
EOF

# 2. 重启 Ralph
ralph --reset-session --monitor
```

### 问题 4：Ralph 修改了不应该修改的文件

**症状**：
Ralph 改动了配置文件、node_modules 或其他敏感文件

**解决**：
```bash
# 1. 恢复意外更改
git checkout -- config.production.js

# 2. 在 PROMPT.md 中添加保护规则
cat >> .ralph/PROMPT.md << 'EOF'

## 禁止修改的文件和目录
以下文件和目录严格禁止修改：
- ❌ `node_modules/`（依赖包）
- ❌ `.git/`（Git 元数据）
- ❌ `config.production.js`（生产配置）
- ❌ `.env.production`（生产环境变量）
- ❌ `package-lock.json`（除非明确需要更新依赖）
- ❌ `dist/` 或 `build/`（构建输出）

## 如果需要修改配置
- 必须先询问并获得确认
- 修改前做好备份
- 在提交信息中明确说明原因
EOF

# 3. 限制工具权限（.ralphrc）
# 移除不需要的 Bash 权限
ALLOWED_TOOLS="Write,Read,Edit,Bash(git add *),Bash(git commit *),Bash(npm test)"
```

---

## 高级工作流

### 工作流 1：TDD 开发模式

```bash
# 1. 编写测试优先的 PROMPT
cat > .ralph/PROMPT.md << 'EOF'
# TDD 开发模式

## 开发流程（严格遵守）
1. 📝 写失败的测试
2. ✅ 写最少代码让测试通过
3. 🔄 重构代码
4. 重复

## 示例周期
```
1. 写测试：test/user.test.ts - 测试用户注册
2. 运行测试 - 应该失败（红色）
3. 实现功能：src/user.ts - 实现注册逻辑
4. 运行测试 - 应该通过（绿色）
5. 重构代码 - 提高代码质量
6. 再次运行测试 - 确保仍然通过
```
EOF

# 2. fix_plan.md 按 TDD 组织
cat > .ralph/fix_plan.md << 'EOF'
## 用户认证功能

### 1. 注册功能
- [ ] 写测试：用户可以注册（邮箱 + 密码）
- [ ] 实现：注册 API 端点
- [ ] 验证：测试通过
- [ ] 重构：优化代码

### 2. 登录功能
- [ ] 写测试：用户可以登录
- [ ] 实现：登录 API 端点
- [ ] 验证：测试通过
- [ ] 重构：提取共享逻辑
EOF
```

### 工作流 2：增量交付

```bash
# 1. 将大项目分解为小的可交付增量
cat > .ralph/fix_plan.md << 'EOF'
# 增量交付计划

## Sprint 1: MVP（最小可行产品）
- [ ] 用户可以注册
- [ ] 用户可以登录
- [ ] 用户可以创建 Todo
- [ ] 用户可以查看 Todo 列表

## Sprint 2: 基础功能
- [ ] 用户可以编辑 Todo
- [ ] 用户可以删除 Todo
- [ ] 用户可以标记 Todo 完成
- [ ] 添加基础样式

## Sprint 3: 增强功能
- [ ] Todo 分类
- [ ] Todo 标签
- [ ] 搜索和过滤
- [ ] 响应式设计
EOF

# 2. 分批运行 Ralph
ralph --monitor --calls 50  # Sprint 1

# 测试和部署 Sprint 1
npm test
git tag v0.1.0

# 继续 Sprint 2
ralph --monitor --calls 50
```

---

## 总结

### 关键要点

1. **精心设计 PROMPT.md** - 清晰的指令带来更好的结果
2. **合理安排 fix_plan.md** - 任务分解越细，执行越准确
3. **适时干预** - Ralph 不是完全自主的，需要人工监督
4. **质量优先** - 在 PROMPT.md 中明确质量标准
5. **渐进式工作** - 分批运行，每次完成一个阶段
6. **充分测试** - 每次 Ralph 循环后验证结果

### 学习路径

```
第 1 天：安装和简单项目
    ↓
第 2-3 天：实战小型应用（Todo App）
    ↓
第 4-5 天：重构现有项目
    ↓
第 6-7 天：探索高级特性（specs/、自定义配置）
    ↓
第 2 周+：在真实项目中使用 Ralph
```

### 下一步

- 阅读 [Ralph项目理解文档.md](Ralph项目理解文档.md) 深入理解架构
- 查看 [Ralph快速参考.md](Ralph快速参考.md) 速查命令
- 加入社区：https://github.com/frankbria/ralph-claude-code/discussions

---

**记住**：Ralph 是你的 AI 编程助手，不是替代品。最佳实践是人机协作，你提供方向和判断，Ralph 执行重复性工作。

祝你使用 Ralph 愉快！🚀
