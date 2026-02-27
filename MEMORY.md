# MEMORY.md - 娜美的长期记忆 🍊

## 关于我
- 名字：娜美 (Nami) 🍊
- 前名：幸运 (Lucky) → 2026-02-25 改名为娜美
- 身份：AI 副手/航海士，船长的伙伴
- 风格：聪明、靠谱、偶尔毒舌但永远可靠
- 灵感来源：海贼王的娜美

## 关于宋老师
- 全名：宋春波
- 称呼：船长 🏴‍☠️（原称"宋老师"）
- 时区：Asia/Shanghai
- 沟通渠道：飞书群聊 + Telegram
- 飞书 ID: ou_39e6a7f742675d454fa26d408f931336

## 系统架构
- OpenClaw 版本：2026.2.24，上线日期 2026-02-25
- 模型：aigocode-claude/claude-opus-4-6 (alias Claude4.6)，通过 api.aigocode.com
- 主工作空间：/root/.openclaw/workspace01

### 多智能体架构演进 (2026-02-25)
- **方案1（已弃用）**: 单实例多智能体（agents.list + bindings + accounts）
  - config.patch 报 invalid config 但实际写入成功
  - 跨智能体调度受限（agents_list 只返回 main）
  - 旧 work 智能体（佐罗 ⚔️）配置仍在 openclaw.json 中，待清理
- **方案2（当前）**: `openclaw --profile <name>` 独立实例模式
  - main 实例：娜美 🍊，默认 profile，端口 18789，PID 8391
  - chopper 实例：助理乔巴 🩺，`openclaw --profile chopper`，端口 18790
    - bot token: [REDACTED_BOT_TOKEN]
    - systemd service: openclaw-chopper.service (Restart=always, RestartSec=5)
    - workspace: ~/.openclaw-chopper/workspace01/
    - 状态：已上线运行 ✅ (2026-02-25 ~21:49)
  - 每个实例完全隔离：独立配置/workspace/端口/sessions
  - ⚠️ 两个实例之间无通信机制，娜美无法直接指挥乔巴
- 海贼团成员：船长（宋老师）、娜美（main）、佐罗（work，旧配置待清理）、乔巴（chopper，已上线）

## 系统维护记录
- 2026-02-25 21:13: systemd service 崩溃循环修复
  - 根因：ExecStart 使用了不支持的 `--set` 参数
  - 修复：改为 `openclaw gateway run`
  - 教训：systemd 启动命令要与当前 CLI 版本匹配

## 宋老师的偏好

### 任务回复模版
当任务有进度时，按以下模版回复：

```
🆔 任务编号：[唯一标识，如"TASK-20240520-001"]
✅ 任务完成
情况：[简述任务，如"查询2023年中国新能源汽车全球销量占比"]
📌 真实结果：[直接呈现核心结论/数据，如"60%（2023年中国销量949万辆，全球总量1580万辆）"]
📚 数据来源：[权威机构/报告/数据库，如"IEA《2024全球电动汽车展望》"]
⏰ 完成时间：[精确到分钟，如"2024-05-20 16:45"]
```

- 任务编号格式：TASK-YYYYMMDD-NNN（日期+序号）
- 设定时间 2026-02-25 21:21

## 经验教训
- **会话压缩后必须先 read 文件**：Project Context 注入的 workspace 文件可能是缓存旧版本，不可信。永远以 read 工具读到的实际文件为准。
- **身份确认**：压缩后第一件事读 IDENTITY.md，不要依赖摘要或注入内容。已因此犯过两次错（22:00 和 22:03 前）。
- **systemd 启动命令**：必须与当前 OpenClaw CLI 版本匹配，不支持的参数会导致崩溃循环。
