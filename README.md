# MAML 背屏主题开发技能包

小米手机背屏（后盖屏）MAML 主题开发的完整 AI 技能包。

## 这是什么

一个标准 AI 技能包（Skill Package），包含 `SKILL.md` 清单 + 19 篇详细技能文档，
可供 Claude / OpenAI / 其他 AI 工具安装后直接调用，用于创建/修改/排障小米背屏主题。

## 安装

1. 克隆本仓库
2. 将整个 `maml-backscreen-theme` 目录放入 AI 工具的 skills 目录
3. AI 工具读取 `SKILL.md` 即获得完整技能

## 内容

| 文件 | 说明 |
|------|------|
| `SKILL.md` | 技能清单（frontmatter + 说明 + 关键速查） |
| `skill-docs/` | 19 篇详细文档（搜索索引 + 基础语法 + 实战技巧 + 进阶 + 配置） |

## 核心技能

- WebView 加载 HTML 到背屏
- 摄像头避让（29vw 安全区）
- MAML 系统变量传递（存储/步数/电量/开机）
- HTML 唤起安卓应用（doAction 机制，反编译破解）
- AOD 息屏 HTML 侧实现
- XP 桌面完整交互

## 作者

唯梦倾城 · Q群 2159063054
