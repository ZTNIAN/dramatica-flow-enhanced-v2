# Dramatica-Flow Enhanced — 项目交接文档 V2

> 最后更新：2026-04-17 10:20
> **下次把本文件发给 AI，它就能读懂整个项目，包括V1→V2改了什么、怎么继续迭代V3。**

---

## 一、这是什么？

**Dramatica-Flow Enhanced** 是一个 AI 自动写小说系统。你给它一句话设定，它帮你：

1. 构建完整世界观（角色/势力/地点/规则）
2. 生成三幕结构大纲 + 逐章规划
3. 一章一章自动写（每章2000字）
4. 写完自动审计（9维度打分 + 17条红线）
5. 不合格自动修订，最多返工3轮

**核心卖点：** 它不是"AI写字机器"，而是"AI理解故事"——有因果链、信息边界、伏笔管理、情感弧线。

---

## 二、项目地址

| 地址 | 说明 |
|------|------|
| **原版仓库** | https://github.com/ydsgangge-ux/dramatica-flow — 叙事逻辑强，但缺乏前期规划和质量管控 |
| **增强版V1仓库** | https://github.com/ZTNIAN/dramatica-flow-enhanced — V1版，12个增强点完成但有6项未接入 |
| **增强版V2仓库（当前）** | https://github.com/ZTNIAN/dramatica-flow-enhanced-v2 — V2版，修复P0/P1问题 + 知识库扩充 |
| **OpenMOSS知识库来源** | https://github.com/uluckyXH/OpenMOSS — 多Agent协作平台，提供写作规则/示例/市场数据 |- gz包：https://litter.catbox.moe/xyzwkq.gz
- 7z包：https://litter.catbox.moe/2bmxac.7z

---

## 三、V1 和 V2 的区别

### V1 做了什么

V1 在原版基础上完成了12个增强点：
- 9维度加权评分 + 17条红线一票否决
- 禁止词汇清单 + 正则扫描
- 知识库目录 + 去AI味规则
- 45条写作风格约束
- Show Don't Tell 转换表
- 修改前后对比示例库
- 返工上限3次 + 监控
- 动态分层规划器
- 巡查Agent
- 质量统计仪表盘
- 知识库查询激励

### V1 的问题（交接文档里列了12项未完成）

| # | 问题 | 严重度 |
|---|------|--------|
| 1 | 质量仪表盘写了但没接入管线 | P0 |
| 2 | 对比示例库写了但没注入Writer prompt | P0 |
| 3 | 写作技巧库写了但没注入Architect prompt | P0 |
| 4 | 知识库查询激励写了但没调用 | P1 |
| 5 | 动态规划器写了但没接入管线 | P1 |
| 6 | 番茄小说市场数据没引入 | P1 |
| 7 | LLM调用失败的重试策略不完善 | P1 |
| 8 | Web UI没集成新功能 | P2 |
| 9 | server.py没更新 | P2 |
| 10 | 没有测试覆盖 | P2 |
| 11 | setup.py没更新 | P2 |
| 12 | 示例库太小 | P2 |

### V2 修了什么

V2 修复了 P0 和 P1 中最核心的7项：

| 改动 | 文件 | 效果 |
|------|------|------|
| 质量仪表盘接入管线 | `core/pipeline.py` | 每章写完自动记录评分、维度分数、返工次数，保存到 `quality_dashboard.json` |
| 对比示例库注入Writer | `core/agents/__init__.py` | 写手写章时自动看到"好vs坏"的对比示例，知道怎么改 |
| 知识库注入Architect | `core/agents/__init__.py` | 建筑师规划蓝图时参考写作技巧和去AI味规则 |
| LLM重试增强 | `core/llm/__init__.py` | 智能判断异常类型 + 指数退避 + 日志记录，不再盲目重试 |
| 动态规划器接入管线 | `core/pipeline.py` | 每章写完自动更新规划进度，低分时提示调整 |
| 写作技巧库扩充 | `core/knowledge_base/writing_techniques.md` | 从61行→265行，新增章末钩子/情绪阶梯/伏笔技巧等 |
| 番茄小说数据引入 | `core/knowledge_base/fanqie-data/` | 6份市场报告，供MarketAnalyzerAgent参考 |
| 写作示例引入 | `core/knowledge_base/examples/` | 6个正面示例 + 1个反面示例 |

---

## 四、V2 是怎么迭代的

### 迭代过程记录

1. **用户发来交接文档和操作手册**（V1的两个md文件 + OpenMOSS压缩包链接）
2. **AI下载并阅读所有文档**，理解项目全貌
3. **AI分析V1的12项未完成问题**，按优先级排序
4. **AI在服务器上修改代码**（约25分钟）：
   - 修改 `core/agents/__init__.py` — 添加知识库加载机制 + 注入prompt
   - 修改 `core/pipeline.py` — 接入质量仪表盘 + 动态规划器
   - 修改 `core/llm/__init__.py` — 增强重试机制
   - 扩充 `core/knowledge_base/writing_techniques.md`
   - 引入 `core/knowledge_base/fanqie-data/`（6份市场报告）
   - 引入 `core/knowledge_base/examples/`（7个写作示例）
5. **用户给GitHub Token**，AI通过GitHub API推送代码到新仓库
6. **AI生成交接文档**（本文件）

### 关键：代码在云端服务器上修改

AI 不是在你本地改代码的，而是在它的云服务器上改完，然后推到你的 GitHub。

**每次迭代的流程是：**
```
你发消息给AI（带交接文档 + Token）
    ↓
AI读文档 → 理解项目 → 在服务器上改代码
    ↓
AI把代码推到你的GitHub
    ↓
你本地 git pull 拉取最新代码
    ↓
测试运行
```

---

## 五、本地部署指南

### 首次部署

```bash
# 1. 克隆项目
git clone https://github.com/ZTNIAN/dramatica-flow-enhanced-v2.git
cd dramatica-flow-enhanced-v2

# 2. 创建虚拟环境
python3 -m venv .venv
source .venv/bin/activate    # Linux/Mac
# .venv\Scripts\activate     # Windows

# 3. 安装依赖
pip install -e .

# 4. 配置API Key
cp .env.example .env
# 编辑 .env，填入你的 DeepSeek API Key
```

### .env 配置说明

```env
LLM_PROVIDER=deepseek           # 用 deepseek 或 ollama（本地免费）
DEEPSEEK_API_KEY=你的key         # 去 https://platform.deepseek.com 申请
DEEPSEEK_BASE_URL=https://api.deepseek.com/v1
DEEPSEEK_MODEL=deepseek-chat
DEFAULT_WORDS_PER_CHAPTER=2000   # 每章字数
DEFAULT_TEMPERATURE=0.7          # 写作温度（越高越随机）
AUDITOR_TEMPERATURE=0.0          # 审计温度（0=最客观）
BOOKS_DIR=./books                # 书籍数据目录
```

### 启动方式

```bash
# 方式A：命令行
source .venv/bin/activate
df --help          # 查看所有命令
df doctor          # 检查API连接

# 方式B：Web UI
uvicorn core.server:app --reload --host 0.0.0.0 --port 8766
# 浏览器打开 http://127.0.0.1:8766/
```

### 日常使用流程

```bash
# 1. 市场分析（可选）
df market 科幻 --premise "你的设定"

# 2. 世界观构建（必做）
df worldbuild "废灵根少年觉醒上古传承逆袭" --genre 玄幻

# 3. 大纲规划（必做）
df outline --book 生成的书名

# 4. 开始写作
df write 书名        # CLI写一章
# 或用Web UI点按钮

# 5. 查看状态
df status 书名

# 6. 导出
df export 书名
```

---

## 六、文件结构速查

```
dramatica-flow-enhanced-v2/
├── cli/main.py                     # CLI入口，13个命令
├── core/
│   ├── agents/__init__.py          # 9个Agent（核心，最大文件）
│   ├── pipeline.py                 # 写作管线
│   ├── llm/__init__.py             # LLM抽象层
│   ├── narrative/__init__.py       # 叙事引擎
│   ├── state/__init__.py           # 状态管理
│   ├── types/                      # 数据类型
│   ├── validators/__init__.py      # 写后验证器（17类规则）
│   ├── server.py                   # FastAPI服务器
│   ├── quality_dashboard.py        # 质量仪表盘
│   ├── dynamic_planner.py          # 动态规划器
│   ├── kb_incentive.py             # 知识库查询激励
│   └── knowledge_base/
│       ├── anti_ai_rules.md        # 去AI味规则
│       ├── writing_techniques.md   # 写作技巧库
│       ├── before_after_examples.md # 对比示例
│       ├── fanqie-data/            # 番茄小说市场数据
│       └── examples/               # 好/坏写作示例
├── dramatica_flow_web_ui.html      # Web UI
├── pyproject.toml                  # 项目配置
├── .env                            # API Key配置（不提交git）
├── CHANGELOG-V2.md                 # V2改动日志
└── PROJECT_HANDOFF.md              # 本文件
```

---

## 七、如何迭代 V3

### V3 应该做什么（按优先级）

| 优先级 | 任务 | 说明 |
|--------|------|------|
| P1 | Web UI 添加世界观构建页面 | 表单输入设定，点击生成，可视化展示结果 |
| P1 | Web UI 添加大纲规划页面 | 展示三幕结构 + 章纲表格 + 张力曲线图 |
| P1 | 完善错误处理 | LLM超时/格式错误时的降级策略 |
| P2 | 知识库查询激励接入 | Agent实际调用 kb_incentive 记录查询 |
| P2 | 番茄数据注入MarketAnalyzer | 把市场报告注入到市场分析Agent的prompt |
| P2 | 动态规划器更深度集成 | 根据审计分数自动调整后续章纲张力曲线 |
| P2 | 添加单元测试 | 覆盖 validators 和新增Agent的基本功能 |
| P2 | server.py更新 | 暴露新Agent的API端点 |

### V3 迭代流程

**第1步：准备交接材料**
- 把本文件 `PROJECT_HANDOFF.md` 发给AI
- 如果有新的问题清单，也一起发
- 如果有新的参考资料（比如V2运行后的审计报告），也发

**第2步：给 GitHub Token**
- 去 https://github.com/settings/tokens → Generate new token (classic)
- 勾选 `repo` 权限
- 把 token 发给AI（格式：`ghp_xxxxx`）
- **AI推完代码后立刻 revoke 这个 token！**

**第3步：AI 在云端改代码**
- AI 读交接文档 → 理解V2状态 → 确定V3要做什么
- AI 在服务器上修改代码
- AI 推送到你的 GitHub
- AI 更新交接文档（PROJECT_HANDOFF.md）

**第4步：你本地拉取**
```bash
cd dramatica-flow-enhanced-v2
git pull origin main
# 测试新功能
```

### ⚠️ 安全提醒

1. **GitHub Token 每次用完必须 revoke** — 聊天记录里有明文 token，不安全
2. **DeepSeek API Key 不要发在聊天里** — 用 `.env` 文件配置
3. **.env 文件不要提交到 git** — `.gitignore` 已排除

---

## 八、踩坑记录

### 坑1：heredoc写中文文件会损坏
```bash
# ❌ 不要用
cat > file << 'EOF' 中文内容 EOF

# ✅ 用这个
python3 -c "with open('file','w') as f: f.write('中文内容')"
```

### 坑2：sed无法匹配中文字符
```bash
# ❌ 不要用
sed -i 's/中文/替换/' file

# ✅ 用这个
python3 -c "import pathlib; p=pathlib.Path('file'); p.write_text(p.read_text().replace('中文','替换'))"
```

### 坑3：Python虚拟环境
```bash
# 如果 pip install -e . 报 externally-managed-environment
python3 -m venv .venv
source .venv/bin/activate
pip install -e .
```

### 坑4：catbox文件链接72小时过期
交接文档里的压缩包链接会过期，需要重新上传：
```bash
curl -F "reqtype=fileupload" -F "time=72h" -F "fileToUpload=@文件路径" https://litterbox.catbox.moe/resources/internals/api.php
```

### 坑5：GitHub推送TLS连接失败
如果 `git push` 报 `GnuTLS recv error`，用 GitHub API 代替：
- 用 Contents API 逐文件上传（脚本在交接过程中已验证可用）

---

## 九、技术栈

| 组件 | 技术 |
|------|------|
| 语言 | Python 3.11+ |
| 后端 | FastAPI |
| CLI | Typer |
| 数据存储 | 文件系统（JSON + Markdown） |
| LLM | DeepSeek API（默认）/ Ollama（本地免费） |
| 前端 | 单文件 HTML |
| 校验 | Pydantic v2 |

---

## 十、Agent 体系（9个Agent）

| Agent | 职责 | 触发时机 |
|-------|------|---------|
| WorldBuilderAgent | 从一句话生成世界观 | `df worldbuild` |
| OutlinePlannerAgent | 生成三幕大纲+章纲 | `df outline` |
| MarketAnalyzerAgent | 市场分析 | `df market` |
| ArchitectAgent | 规划单章蓝图（核心冲突/伏笔/情感弧） | 每章写前 |
| WriterAgent | 生成章节正文（2000字） | 每章写手 |
| PatrolAgent | 快速扫描（P0/P1/P2） | 写后立即 |
| AuditorAgent | 9维加权审计（≥95分通过） | 巡查后 |
| ReviserAgent | 修订正文 | 审计不通过时 |
| SummaryAgent | 生成章节摘要 | 写完后 |

---

## 十一、写作管线流程

```
[世界构建] 一句话设定 → WorldBuilder → 世界观JSON
    ↓
[大纲规划] 世界观 → OutlinePlanner → 三幕结构 + 章纲
    ↓
[单章循环]（每章重复）
    ├── 快照备份
    ├── 建筑师：规划蓝图（注入写作技巧+去AI规则）
    ├── 写手：生成正文（注入对比示例库）+ 结算表
    ├── 验证器：零LLM硬规则扫描
    ├── 巡查者：P0/P1/P2快速扫描
    ├── 审计员：9维度加权评分（≥95分+单项≥85+无红线）
    │   └── 不通过 → 修订者修正 → 再审（最多3轮）
    ├── 保存最终稿
    ├── 因果链提取
    ├── 摘要生成
    ├── 状态结算
    ├── 质量仪表盘记录（V2新增）
    └── 动态规划器更新（V2新增）
```

---

## 十二、OpenMOSS 知识库说明

V2 引入了 OpenMOSS 的知识库内容，存放在 `core/knowledge_base/` 下：

| 文件 | 内容 | 谁在用 |
|------|------|--------|
| `anti_ai_rules.md` | 去AI味规则、禁止词汇、45特征润色系统 | Writer + Architect |
| `writing_techniques.md` | 开篇钩子/五感描写/对话技巧/节奏控制/伏笔技巧 | Architect |
| `before_after_examples.md` | 7组修改前后对比示例 | Writer |
| `examples/good/` | 6个正面写作示例 | 参考用 |
| `examples/bad/` | 1个反面示例（AI味严重） | 参考用 |
| `fanqie-data/` | 6份番茄小说市场调研报告 | MarketAnalyzer |

**OpenMOSS 完整知识库**（本次未全部引入，V3可继续扩充）：
- `knowledge-base/rules/` — 审查标准、红线清单、去AI指南、工作流
- `knowledge-base/references/` — 写作技巧、题材指南、市场数据
- `knowledge-base/agent-specific/` — 各Agent专属技能和检查清单
- `prompts/role/` — 25个专业Agent的提示词

---

*本文档由AI自动生成。下次迭代时，把本文件发给AI即可快速理解整个项目。*
