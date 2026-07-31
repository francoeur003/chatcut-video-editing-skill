# ChatCut Video Editing Skill

一个可公开复用的 Codex Skill：用 ChatCut 把本地或项目内的多段口播素材做成**可继续编辑**的时间线，完成素材串联、气口压缩、口癖/重录清理、字幕、轻特效、画面验收和按需导出。

## 能做什么

- 多素材导入、去重与顺序串联
- 基于文字稿剪口播，不靠盲切时间码
- 压缩长停顿并保留自然呼吸
- 中文字幕与字幕安全区检查
- 克制的推近、震动、Punch Zoom 等轻特效
- 时间线结构回读 + 合成画面验收
- 用户明确要求后导出 MP4

## 安装

前提：你的 Codex 环境已安装并登录 ChatCut 插件/MCP。

```bash
git clone https://github.com/francoeur003/chatcut-video-editing-skill.git
mkdir -p ~/.codex/skills
cp -R chatcut-video-editing-skill/chatcut-video-editing ~/.codex/skills/
```

重启或刷新 Codex 后，可以直接说：

```text
Use $chatcut-video-editing to assemble these clips, tighten pauses, add Chinese captions and light effects, verify the result, and export when I ask.
```

中文示例：

```text
用 $chatcut-video-editing 把这几段口播按顺序串起来，剪掉长气口和失败重录，上中文字幕，加一点轻特效，最后导出 1080p MP4。
```

## 设计原则

- 优先交付可编辑的 ChatCut 时间线，不用本地扁平视频替代工程。
- 先定结构和口播节奏，再做字幕、特效和导出。
- 成功调用工具不等于验收完成；必须回读结构并检查合成画面。
- 不擅自增加音乐、B-roll、生成素材或付费步骤。
- 不在仓库内保存项目 ID、用户路径、令牌或媒体文件。

## 目录

```text
chatcut-video-editing/
├── SKILL.md
├── agents/openai.yaml
└── references/
    ├── workflow.md
    └── qa-checklist.md
```

## License

MIT
