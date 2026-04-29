# 安装脚本

## 前置条件

- OpenCode ≥ 1.14.29
- OhMyOpenAgent 插件
- Magic Context 插件

## 安装

```powershell
# 1. 复制配置文件
$configDir = "$env:USERPROFILE\.config\opencode"
New-Item -ItemType Directory -Force -Path $configDir | Out-Null
Copy-Item ".\opencode.json" $configDir\
Copy-Item ".\oh-my-openagent.json" $configDir\
Copy-Item ".\magic-context.jsonc" $configDir\
Copy-Item ".\tui.json" $configDir\
Write-Output "✅ 配置文件已复制到 $configDir"

# 2. 安装 skill
$skillDir = "$env:USERPROFILE\.agents\skills\chinese-first-think"
New-Item -ItemType Directory -Force -Path $skillDir | Out-Null
Copy-Item ".\skills\chinese-first-think\SKILL.md" $skillDir\
Write-Output "✅ Skill 已安装到 $skillDir"

# 3. 汉化 Magic Context（可选）
$mc = Get-ChildItem "$env:USERPROFILE\.cache\opencode\packages\@cortexkit" -Directory | Select-Object -Last 1
if ($mc) {
    Copy-Item ".\patches\magic-context-tui\sidebar-content.tsx" "$mc\node_modules\@cortexkit\opencode-magic-context\src\tui\slots\"
    Copy-Item ".\patches\magic-context-tui\index.tsx" "$mc\node_modules\@cortexkit\opencode-magic-context\src\tui\"
    Write-Output "✅ Magic Context 已汉化"
} else {
    Write-Output "⚠️ Magic Context 插件未安装，跳过汉化"
}

Write-Output "`n🎉 安装完成，请重启 OpenCode"
```

## 验证

在新会话中输入任意消息，检查输出是否为中文思维总结开头。
