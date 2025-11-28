# Netlib.re 单/多账号登录保活脚本

## 项目简介

本项目用于自动登录 [Netlib.re](https://www.netlib.re/) 网站，实现账号保活。适用于需要每隔一段时间（如 30 天）登录一次的网站场景。支持多账号循环登录、登录失败判定、延迟和网页加载等待，防止被风控。支持 GitHub Actions 自动运行（无头模式），成功登录后停留 5 秒，用于保活或刷新 Cookie。仅用于登录保活，不涉及敏感操作。

详细图文教程请查看[Netlib免费域名保活方法](https://blog.voocv.com/post/Netlib)
  
   **⚠️随着Fork数量增加，为了避免风控，强烈建议Fork后
   自行修改`.github/workflows/keepalive.yml`的第五行`- cron: "0 0 1,31 * *"`，其中的1和31，修改为`1-31`任意两个数字**

   
   * 写多账号表示支持多账号，不代表不支持单个账号，问的我一脸懵，单账号也能用!

## 功能说明

1. **多账号支持**：通过单个环境变量配置多个账号，保证安全。可在 GitHub Actions 中循环登录。

2. **GitHub Actions 自动运行**：

   * 支持定时任务（如每月 1 号和 31 号）
   * 支持手动触发
3. **TG 推送运行结果**：
   * 可选配置

## 使用方式（Fork 部署）

1. **Fork 仓库**

   * 点击 GitHub 仓库页面右上角 `Fork` 将本项目复制到自己的账户。

2. **配置 Secrets**

   * 在仓库 `Settings` → `Secrets and variables` → `Actions` → `New repository secret` 中为每个账号添加用户名和密码，例如：
   * 格式如下（每个账号用 `;` 分隔，每个账号用户名和密码用 `,` 分隔）：、

***单个账号示例：***

`Name` 填入 `SITE_ACCOUNTS`

`Secret` 填入
```
user,password
```

***多账号示例：***

`Name` 填入 `SITE_ACCOUNTS`

`Secret` 填入
```
user1,password1;user2,password2;user3,password3
```

* 可根据需要增加任意数量账号。

3. TG推送通知（可选）

   * 在仓库 `Settings` → `Secrets and variables` → `Actions` → `New repository secret` 中添加两个变量

变量一：

`Name` 填入 `TELEGRAM_BOT_TOKEN` ：TG BOT 的 TOKEN

`Secret` 填入 `TG BOT 的 TOKEN` 

变量二：

`Name` 填入 `TELEGRAM_CHAT_ID` ：接收信息的用户ID

`Secret` 填入 `接收信息的用户ID` 

* 获取方式不用教了吧，不会搜一下就行

5. **修改登录脚本（可选）**

   * 默认脚本已支持从 `SITE_ACCOUNTS` 环境变量读取账号信息，无需修改。
   * 若需要，本地测试可直接修改 `login.py` 文件的 `accounts` 列表。

6. **安装依赖（本地运行）**

```bash
pip install --upgrade pip
pip install playwright
python -m playwright install chromium
```

6. **本地运行**

```bash
# Linux/macOS
eport SITE_ACCOUNTS="user1,password1;user2,password2"
# Windows PowerShell
set SITE_ACCOUNTS=user1,password1;user2,password2
python login.py
```

* 脚本会自动循环登录每个账号
* 每个账号操作步骤间隔 2 秒
* 终端打印每个账号登录结果 ✅ 或 ❌

7. **GitHub Actions 自动运行**

* `.github/workflows/keepalive.yml` 已包含 workflow 配置
* 默认自动执行：每月 1 号和 31 号
* 手动触发：Actions 页面点击 Run workflow
* Actions 日志显示每个账号的登录结果

## 日志示例

```
🚀 开始登录账号: user1
✅ 账号 user1 登录成功
🚀 开始登录账号: user2
❌ 账号 user2 登录失败: Invalid credentials.
```

## 注意事项

1. 确保账号密码正确，否则登录会判定失败。
2. 本项目仅用于登录保活，不支持操作其他敏感功能。
3. 如果网站增加防刷机制或修改页面元素，脚本可能需要更新。
4. GitHub Actions 免费账户有执行时长限制，适合少量账号保活。

## 项目结构

```
netlib-keepalive/
├─ login.py               # 多账号登录脚本
├─ .github/
│  └─ workflows/
│     └─ keepalive.yml   # GitHub Actions 工作流
└─ README.md              # 项目介绍与使用说明
```
## 许可证

MIT License
