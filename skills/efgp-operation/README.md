# efgp-operation

鼎新 EFGP 流程管理系统（<https://efgpcn.digiwin.com/NaNaWeb/>）的浏览器自动化 Skill，基于 playwright-cli 驱动真实浏览器完成门户操作。

## 功能范围

| 功能 | 说明 |
|---|---|
| 自动登录 | 利用浏览器密码管理器自动填充账密，一键点"登入" |
| 会议室预订 | 当前最完整的流程：会议申请/异动单填写、选房（按楼层+座位数规则）、Zoom 视讯账号订阅、发起流程 |
| 请假 / 查考勤 / 补考勤 | 未验证，可以自由尝试 |

## 触发方式

当对话中出现以下说法时，Agent 会自动加载本 Skill：

- EFGP、EasyFlow、efgpcn.digiwin.com
- 订会议室、会议申请、视讯会议、Zoom 账号（如"帮我订个会议室"）
- 请假、考勤、补考勤（如"查一下考勤"）

## 安装

### 1. 安装 playwright-cli（前置依赖）

本 Skill 的所有浏览器操作都依赖 `playwright-cli` 命令行工具。已安装（`playwright-cli --version` 能输出版本号）可跳过本步。

**前提条件**：已安装 Node.js（自带 npm）。

```powershell
# 全局安装
npm install -g @playwright/cli

# 验证安装
playwright-cli --version
```

> 首次运行如提示缺少浏览器二进制，执行 `playwright-cli install-browser` 或按提示安装；若全局命令不可用，可用 `npx playwright-cli` 替代。

### 2. 安装本 Skill

**方式一：手动复制（本地直接使用）**

将本 `efgp-operation/` 目录（含 `SKILL.md`）复制到以下位置之一：

- **项目级**（仅当前项目可用）：`<项目>\.agents\skills\efgp-operation\`
- **用户级**（跨项目可用）：`%USERPROFILE%\.agents\skills\efgp-operation\`

DSH 会监视这些目录，复制后即刻自动发现，无需重启。

**方式二：skills CLI 一键安装（推荐，本 Skill 已发布到 GitHub）**

[skills](https://skills.sh/) CLI 是通用的 agent skill 安装器，复制执行以下命令即可（全局安装，跨项目可用）：

```powershell
npx skills add hehuahong/digi-efgp --skill efgp-operation -g -a cline -y --copy
```

| 参数 | 含义 |
|---|---|
| `hehuahong/digi-efgp` | GitHub 仓库简写（也支持完整 URL），skills CLI 从这里下载 skill 包 |
| `--skill efgp-operation`（`-s`） | 只安装仓库中指定名称的 skill；`*` 表示安装仓库内全部 skill |
| `-g`（`--global`） | 装到用户级目录（`%USERPROFILE%\.agents\skills`，跨项目可用）；省略则装到当前项目的 `.agents\skills`（项目级） |
| `-a cline`（`--agent`） | 指定目标 agent，决定安装目录。cline、warp、zed 等 agent 的目录约定恰好是 `.agents\skills`，与 DSH 一致，所以装完 DSH 即可发现；**不要用 `-a claude-code`**（会装到 `.claude\skills`，DSH 看不到） |
| `-y`（`--yes`） | 跳过交互确认 |
| `--copy` | 复制文件而非符号链接。默认是 symlink，Windows 上常需管理员权限/开发者模式，建议加上此参数 |

## 首次使用配置

首次使用需要一次性配置，详见 SKILL.md 的"首次使用"章节：

1. 向 Agent 提供**办公楼层**和**常规参会人数**，生成个性化配置 `.agents\efgp\efgp-personalization.md`。
2. 在有头浏览器中手动登录 EFGP 一次，并在浏览器提示时选择**保存密码**——之后的会话即可自动填充账密、一键登录。

## 目录与文件说明

| 路径 | 用途 |
|---|---|
| `.agents\skills\efgp-operation\SKILL.md` | 本 Skill 的完整执行指令（Agent 加载使用） |
| `.agents\efgp\efgp-personalization.md` | 个性化配置：办公楼层、常规参会人数、选房规则、历史预订参考 |
| `.agents\efgp\.efgp-profile\` | 浏览器用户数据目录，保存登录态与浏览器记住的密码 |
| `.playwright-cli\` | 过程产物（页面快照 yaml、截图 png），任务成功后清空 |

## 安全与隐私

- Skill **不记录账号和密码**；密码只保存在浏览器自己的密码管理器（`.efgp-profile\` 目录）中。
- `.agents\efgp\`（含凭证与用户偏好）和 `.playwright-cli\`（快照可能含姓名、工号等页面敏感信息）**都必须加入 `.gitignore`**，不得提交版本库。
- 过程快照仅用于操作过程，任务成功后即删除，Skill 本身不留存。
