# 详细配置说明

## 配置文件结构

```
$env:USERPROFILE\.config\opencode\
├── opencode.json            # 主配置（provider, agent, plugin）
├── oh-my-openagent.json     # OhMyOpenAgent 插件配置
├── magic-context.jsonc      # Magic Context 插件配置
├── tui.json                 # TUI 侧边栏插件注册

$env:USERPROFILE\.agents\skills\
└── chinese-first-think\
    └── SKILL.md             # 中文先行 skill
```

## 锚定指令详解

### `opencode.json: agent.build.prompt`

```json
{
  "agent": {
    "build": {
      "prompt": "你必须使用中文语言思维完成所有任务。在收到用户第一条消息后、执行任何操作或开始推理前，你必须先用中文写一段简短的总结，说明用户需要你做什么、你计划用什么步骤完成、你还缺什么信息。这段中文是你第一个输出，完成这段输出后才能执行具体任务。"
    }
  }
}
```

**为什么使用 `agent.build.prompt` 而不是 `instructions` 数组？**

查看源码 `session/llm.ts:128-137`：

```typescript
system.push(
  (input.agent.prompt ? [input.agent.prompt] : SystemPrompt.provider(input.model)),
  ...input.system,
  ...(input.user.system ? [input.user.system] : []),
)
```

`agent.prompt` 排在 system prompt 数组的 **首位**（position [0]），优先级最高。`instructions` 通过 `input.system` 注入，排在后面。

### `oh-my-openagent.json: prompt_append`

```json
{
  "agents": {
    "sisyphus": {
      "prompt_append": "你必须使用中文语言思维完成所有任务..."
    }
  }
}
```

OhMyOpenAgent 通过 plugin hook 将 `prompt_append` 注入 `input.system`，与 `agent.build.prompt` 形成双重锚定。

## Magic Context 汉化

插件源码路径（缓存版本）：
```
%USERPROFILE%\.cache\opencode\packages\@cortexkit\opencode-magic-context@latest\
node_modules\@cortexkit\opencode-magic-context\src\tui\
├── slots\sidebar-content.tsx    # 侧边栏组件
└── index.tsx                    # 状态对话框组件
```

TUI 插件通过 `package.json` 的 `exports["./tui"]` 从源码 `src/tui/index.tsx` 直接加载（不是 dist 编译产物），因此本地 patch 即生效。

汉化内容包括：
- **sidear-content.tsx**：历史记录、压缩块、事实、记忆库、对话等标签
- **index.tsx**：Magic Context 状态对话框的所有标签、按钮、Toast 消息
- 相对时间格式：just now→刚刚, Xm ago→X分钟前, Xh ago→X小时前, Xd ago→X天前
