# opencode- — 中文语言锚定配置方案

让 LLM 编程助手（OpenCode / Claude Code 等 CLI 工具）**稳定使用中文思维与输出**的完整配置方案。

## 核心原理

**语言锚定（Language Anchoring）**：利用自回归语言模型的因果依赖特性，通过**控制首个主动输出为中文**来锚定整场对话的语言模式。

```
纯 prompt 约束         → ~70% 效果    （被动指令，可被覆盖）
+ 工具描述汉化         → ~85%
+ 上下文注释汉化       → ~95%+
+ 锚定指令             → 近 100%      （强制首个输出为中文）
```

关键论文参考：
- [Contextual Inertia](https://arxiv.org/abs/2603.04783) — 多轮交互中的推理惯性
- [Prefix Anchoring](https://arxiv.org/abs/2603.17070) — 强制前缀改善跨语言能力

> 本方案的核心思路源于 [笑望九州](https://space.bilibili.com/3546502245485766) 的 B 站专栏文章《机械提示词的终结——从中国哲学看 AI 协作》  
> 详见：[*Bilibili 原文*](https://www.bilibili.com/opus/1196486891254317072)

## 方案组成

| 组件 | 文件 | 作用 |
|---|---|---|
| **锚定指令** | `opencode.json` → `agent.build.prompt` | 注入 system prompt 首位，声明中文思维协议 |
| **prompt_append** | `oh-my-openagent.json` → `prompt_append` | OhMyOpenAgent 级别补充锚定 |
| **中文先行 skill** | `skills/chinese-first-think/SKILL.md` | 标准化 skill 定义，可复用 |
| **Magic Context 汉化** | `patches/magic-context-tui/` | 侧边栏 + 状态对话框中文界面 |

## 安装

### 前置依赖

- [OpenCode](https://opencode.ai) ≥ 1.14.29
- [OhMyOpenAgent](https://github.com/code-yeongyu/oh-my-openagent) 插件
- [Magic Context](https://github.com/cortexkit/opencode-magic-context) 插件

### 步骤

1. **复制配置文件**

```powershell
copy opencode.json $env:USERPROFILE\.config\opencode\
copy oh-my-openagent.json $env:USERPROFILE\.config\opencode\
copy magic-context.jsonc $env:USERPROFILE\.config\opencode\
copy tui.json $env:USERPROFILE\.config\opencode\
```

2. **安装 skill**

```powershell
mkdir -Force $env:USERPROFILE\.agents\skills\chinese-first-think
copy skills\chinese-first-think\SKILL.md $env:USERPROFILE\.agents\skills\chinese-first-think\
```

3. **汉化 Magic Context**

```powershell
# 找到插件缓存路径
$mc = Get-ChildItem $env:USERPROFILE\.cache\opencode\packages\@cortexkit -Directory | Select-Object -Last 1
copy patches\magic-context-tui\sidebar-content.tsx "$mc\node_modules\@cortexkit\opencode-magic-context\src\tui\slots\"
copy patches\magic-context-tui\index.tsx "$mc\node_modules\@cortexkit\opencode-magic-context\src\tui\"
```

4. **重启 OpenCode** 生效

## 验证

在新会话中输入任意消息，如果模型回复以中文思维总结开始，则锚定已生效：

```
【理解总结】
用户让我做：[...]
我的计划：[...]
还缺的信息：[...]
```
