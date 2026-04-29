# zh-anchor — 中文语言锚定配置方案

让 LLM 编程助手稳定使用中文思维与输出的配置方案。

## 原理

利用自回归语言模型的**首个主动输出**锚定整场对话语言。详见 [B 站原文](https://www.bilibili.com/opus/1196486891254317072)。

## 用法

将配置文件放入 OpenCode 配置目录：

```powershell
copy opencode.json $env:USERPROFILE\.config\opencode\
copy oh-my-openagent.json $env:USERPROFILE\.config\opencode\
```

安装 skill（可选）：

```powershell
mkdir -Force $env:USERPROFILE\.agents\skills\chinese-first-think
copy skills\chinese-first-think\SKILL.md $env:USERPROFILE\.agents\skills\chinese-first-think\
```

重启 OpenCode。

## 验证

新会话中输入任意消息，模型应以中文总结开头。

## 致谢

核心思路源于 [笑望九州](https://space.bilibili.com/3546502245485766) 的 B 站专栏
