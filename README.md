# 大麦助手 (Damai Helper)

大麦助手是一款基于 Selenium 的大麦网（damai.cn）自动抢票工具，支持命令行和图形界面两种使用方式。

> **免责声明**：本项目仅供学习交流，请遵守大麦网用户协议及相关法律法规，切勿用于商业用途。

---

## 目录

- [环境要求](#环境要求)
- [安装步骤](#安装步骤)
- [配置文件说明](#配置文件说明)
- [使用方式](#使用方式)
  - [方式一：Windows 一键启动（推荐）](#方式一windows-一键启动推荐)
  - [方式二：图形界面（GUI）](#方式二图形界面gui)
  - [方式三：命令行运行](#方式三命令行运行)
- [抢票流程详解](#抢票流程详解)
- [常见问题](#常见问题)

---

## 环境要求

| 项目 | 要求 |
|------|------|
| 操作系统 | Windows（推荐）/ macOS / Linux |
| Python | 3.8 及以上（推荐 3.12） |
| 浏览器 | Google Chrome |
| ChromeDriver | 与已安装的 Chrome 版本匹配 |

### 获取 ChromeDriver

1. 查看本机 Chrome 版本：在地址栏输入 `chrome://version/`
2. 前往 [ChromeDriver 下载页](https://googlechromelabs.github.io/chrome-for-testing/) 下载与 Chrome 版本一致的驱动
3. 将 `chromedriver.exe`（Windows）或 `chromedriver`（macOS/Linux）放置到项目根目录，或记录其完整路径

---

## 安装步骤

### 1. 克隆或下载项目

```bash
git clone https://github.com/geniucker-dev/damaihelper.git
cd damaihelper
```

### 2. 安装 Python 依赖

```bash
pip install -r requirements.txt
```

> **提示**：建议使用虚拟环境（venv 或 conda）隔离依赖，避免版本冲突。

使用 conda：

```bash
conda create --name damai python=3.12 -y
conda activate damai
pip install -r requirements.txt
```

---

## 配置文件说明

命令行版本读取项目**根目录**下的 `config.json` 文件。可参照 `config/config.json` 进行配置，将其复制到根目录：

```bash
cp config/config.json ./config.json   # macOS / Linux
copy config\config.json config.json   # Windows
```

下表说明各字段含义：

| 字段 | 类型 | 说明 | 示例 |
|------|------|------|------|
| `date` | 数组（整数） | 期望日期的**序号**优先级列表（1 为第一个日期）。按顺序尝试，取第一个可用日期 | `[1, 2, 3]` |
| `sess` | 数组（整数） | 场次的**序号**优先级列表（1 为第一场）。跳过"无票"场次，取第一个有票场次 | `[1, 2]` |
| `price` | 数组（整数） | 票价档次的**序号**优先级列表（1 为第一个档次）。跳过缺货档次，取第一个有货档次 | `[1, 2, 3]` |
| `real_name` | 数组（整数） | 实名制观演人的序号列表 | `[1, 2]` |
| `nick_name` | 字符串 | 大麦网账号昵称（用于验证登录状态） | `"张三"` |
| `ticket_num` | 整数 | 购买票数 | `2` |
| `viewer_person` | 数组（整数） | 观影人序号优先级列表（1 为第一位观影人）。按列表顺序选择 | `[1, 2]` |
| `driver_path` | 字符串 | ChromeDriver 的**完整路径** | `"C:\\path\\to\\chromedriver.exe"` |
| `damai_url` | 字符串 | 大麦网首页地址（用于扫码登录） | `"https://www.damai.cn/"` |
| `target_url` | 字符串 | 目标演出详情页的手机版链接（`m.damai.cn` 域名） | `"https://m.damai.cn/damai/detail/item.html?itemId=714001339730"` |

### 配置示例

```json
{
    "date": [1, 2],
    "sess": [1, 2],
    "price": [1, 2, 3],
    "real_name": [1],
    "nick_name": "你的昵称",
    "ticket_num": 2,
    "viewer_person": [1, 2],
    "driver_path": "C:\\Users\\你的用户名\\Downloads\\chromedriver.exe",
    "damai_url": "https://www.damai.cn/",
    "target_url": "https://m.damai.cn/damai/detail/item.html?itemId=目标演出ID"
}
```

> **如何获取 `target_url`**：在大麦网找到目标演出，将网址中 `www.damai.cn` 改为 `m.damai.cn`，保留后面的路径和参数即可。

---

## 使用方式

### 方式一：Windows 一键启动（推荐）

双击项目根目录下的 **`win一件运行.bat`**（即"一键运行"），脚本会自动：

1. 创建名为 `joker` 的 conda 虚拟环境（Python 3.12）
2. 激活虚拟环境
3. 安装 `requirements.txt` 中的依赖
4. 启动图形界面（GUI）

> **前提**：系统中已安装 [Anaconda](https://www.anaconda.com/) 或 [Miniconda](https://docs.conda.io/en/latest/miniconda.html)。

---

### 方式二：图形界面（GUI）

运行 GUI 程序：

```bash
python GUI.py
```

GUI 启动后界面包含以下标签页：

| 标签页 | 功能 |
|--------|------|
| 全局设置 | 日志等级、时区、NTP 服务器、Dashboard 地址 |
| 账户配置 | 平台选择、手机号/密码、目标演出链接、日期/场次/票价优先级、代理设置 |
| 策略设置 | 开抢时间、预热阶段、AI 决策、最大重试次数 |
| 监控设置 | 库存监控开关、轮询间隔、触发条件 |
| 通知设置 | Telegram Bot 和邮件通知配置 |
| 插件扩展 | 自定义插件路径 |
| 依赖管理 | 模拟安装依赖（演示用） |

**操作步骤：**

1. 点击菜单 **文件 → 加载示例配置** 可加载 `config/demo_config.json` 作为参考
2. 在各标签页填写实际配置（重点填写"账户配置"中的演出链接和观影人信息）
3. 点击菜单 **文件 → 保存配置** 将配置保存为 `config.json`
4. 勾选"启用自动抢票"，点击 **开始抢票** 启动任务

---

### 方式三：命令行运行

确保根目录已有正确的 `config.json`，然后执行：

```bash
python ticket_script.py
```

---

## 抢票流程详解

程序运行后按以下流程执行：

```
启动
  │
  ├─ 检查 cookies.pkl 是否存在
  │     │
  │     ├─ 不存在 → 打开浏览器，访问大麦网首页
  │     │             → 点击登录按钮
  │     │             → 等待用户扫码完成登录
  │     │             → 保存 Cookie 到 cookies.pkl
  │     │             → 重启浏览器
  │     │
  │     └─ 存在 → 直接加载已保存的 Cookie
  │
  ├─ 打开目标演出详情页（手机版）
  │
  └─ 循环抢票
        │
        ├─ 点击"立即购买"/"立即预订"按钮
        ├─ 选择日期（按 date 列表优先级）
        ├─ 选择场次（按 sess 列表优先级，跳过无票场次）
        ├─ 选择票价（按 price 列表优先级，跳过缺货档次）
        ├─ 设置购票数量（ticket_num）
        ├─ 跳转到订单确认页
        ├─ 选择观影人（按 viewer_person 列表）
        ├─ 提交订单
        └─ 等待跳转到支付宝支付页面
              → 跳转成功后需**手动完成支付**
```

### 注意事项

- **首次运行**会打开浏览器要求**扫码登录**，登录成功后 Cookie 保存到 `cookies.pkl`，后续运行自动复用。
- 若 Cookie 过期（登录状态失效），删除 `cookies.pkl` 后重新运行即可重新扫码。
- 到达支付页面后，程序会暂停并等待用户输入，需**手动完成支付**，支付宝页面不会自动跳转。
- 若长时间未跳转到支付宝页面，可在控制台输入 `1` 确认成功，或等待程序自动重试。
- `date`、`sess`、`price`、`viewer_person` 均为**从 1 开始**的序号，对应页面上选项从左到右/从上到下的顺序。

---

## 常见问题

**Q: 提示 `chromedriver` 版本不匹配怎么办？**

A: 前往 [ChromeDriver 下载页](https://googlechromelabs.github.io/chrome-for-testing/) 下载与本机 Chrome 版本完全一致的 ChromeDriver，替换项目根目录中的 `chromedriver.exe`，并更新 `config.json` 中的 `driver_path`。

---

**Q: Cookie 失效后如何重新登录？**

A: 删除项目根目录下的 `cookies.pkl` 文件，重新运行脚本，会自动打开浏览器要求扫码登录。

---

**Q: 如何找到目标演出的 `itemId`？**

A: 在大麦网找到目标演出页面，URL 中会包含类似 `itemId=714001339730` 的参数，将完整的手机版链接（`m.damai.cn/damai/detail/item.html?itemId=...`）填入 `target_url` 即可。

---

**Q: `viewer_person` 怎么填？**

A: 在大麦网"我的"→"实名信息"中查看已添加的观演人列表，按从上到下的顺序，第一位序号为 `1`，第二位为 `2`，以此类推。`viewer_person: [1, 2]` 表示选择第 1 和第 2 位观演人。

---

**Q: 运行后显示"页面刷新出错"或找不到购票按钮？**

A: 可能是大麦网页面结构发生变化，或演出尚未开始售票。请确认：
- `target_url` 为 `m.damai.cn` 的手机版链接（非 `www.damai.cn`）
- 演出确实在售或即将开售
- Chrome 和 ChromeDriver 版本匹配

---

**Q: Windows 双击 `.bat` 文件提示找不到 conda？**

A: 请先安装 [Anaconda](https://www.anaconda.com/) 或 [Miniconda](https://docs.conda.io/en/latest/miniconda.html)，并确保安装时勾选了"添加到系统 PATH"选项。或直接在已激活 conda 环境的命令行中手动执行 `python GUI.py`。
