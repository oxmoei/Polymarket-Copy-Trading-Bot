# Polymarket 跟单交易机器人

一个基于 Rust 的高性能自动化交易机器人，可实时复制 Polymarket 成功交易者（鲸鱼）的交易。

---

## 目录

1. [要求](#1-要求)
2. [快速开始（适用于初学者）](#2-快速开始适用于初学者)
3. [文档/教程](#3-文档/教程)
4. [安全注意事项](#4-安全注意事项)
5. [工作原理](#5-工作原理)
6. [功能](#6-功能)
7. [高级用法](#7-高级用法)
8. [输出文件](#8-输出文件)
9. [获取帮助](#9-获取帮助)
10. [免责声明](#10-免责声明)

---
## 1. 要求

### 1.1 必需

1. **Polymarket 账户** - 在 https://polymarket.com 注册
2. **Web3 钱包** - 推荐使用 MetaMask（在 Polygon 上准备一些 USDC/USDC.e）
3. **RPC 提供商 API 密钥** - 从 [Alchemy](https://www.alchemy.com/) 或 [Chainstack](https://chainstack.com/) 获取免费套餐
4. **鲸鱼地址** - 您要复制的交易者（40 字符十六进制地址）

### 1.2 推荐

- **一些编程知识** - 不是必需的，但有助于故障排除
- **充足的资金** - 机器人默认使用鲸鱼交易规模的 2%（可配置）

---

## 2. 快速开始（适用于初学者）
**支持 Windows、Linux 、WSL 和 macOS**

### 1️⃣ 克隆项目
（确保你已安装 `git`，如果未安装请参考➡️[安装git教程](./安装git教程.md)）

```
# 克隆仓库
git clone https://github.com/oxmoei/Polymarket-Copy-Trading-Bot.git

# 进入项目目录
cd Polymarket-Copy-Trading-Bot

```

### 2️⃣ 快速安装依赖

一键检查并安装缺失的前置依赖。

#### 📌 Linux / macOS / WSL 用户

```bash
# 在项目根目录执行
./install.sh
```

#### 📌 Windows 用户

```powershell
# 以管理员身份运行 PowerShell，然后在项目根目录执行
Set-ExecutionPolicy Bypass -Scope CurrentUser
.\install.ps1
```

### 3️⃣ 配置环境变量

#### 📌 Linux / macOS / WSL 用户
```bash
# 复制示例环境文件并编辑设置
cp .env.example .env && nano .env # 编辑完成按 Ctrl+O 保存，Ctrl+X 退出
```

#### 📌 Windows 用户
```powershell
# 复制示例环境文件
Copy-Item .env.example .env

# 编辑设置
notepad .env  # 编辑完成保存、关闭
```

填写所需的值（详细信息请参阅[配置指南](docs/03_CONFIGURATION.md)）：
   - `PRIVATE_KEY` - 您的钱包私钥（请保密！）
   - `FUNDER_ADDRESS` - 您的钱包地址（与私钥对应的钱包）
   - `TARGET_WHALE_ADDRESS` - 您要复制的鲸鱼地址（40 字符十六进制，不带 0x）
   - `ALCHEMY_API_KEY` - 从 https://www.alchemy.com/ 获取（或使用 CHAINSTACK_API_KEY）

可选：调整交易设置（请参阅[配置指南](docs/03_CONFIGURATION.md)）

### 4️⃣ 验证您的配置

在运行机器人之前，请验证您的设置是否正确：

```
cargo run --release --bin validate_setup
```

这将检查所有必需的设置是否正确，如果出现问题，会提供有用的错误消息。

### 5️⃣ 测试模式（建议先使用）

在测试模式下运行，查看机器人会做什么，而不会实际交易：

```
# 在 .env 文件中设置 MOCK_TRADING=true，然后：
cargo run --release
```

### 6️⃣ 运行机器人

#### 📌 Linux / macOS / WSL 用户
```bash
# 在 .env 中启用交易（ENABLE_TRADING=true, MOCK_TRADING=false）
cargo run --release
```

#### 📌 Windows 用户
```powershell
# 在 .env 中启用交易（ENABLE_TRADING=true, MOCK_TRADING=false）
.\run.bat
```

---

## 3. 文档/教程

- **[01. 快速开始指南](docs/01_QUICK_START.md)** - 5 分钟设置指南
- **[02. 完整设置指南](docs/02_SETUP_GUIDE.md)** - 详细的分步说明
- **[03. 配置指南](docs/03_CONFIGURATION.md)** - 所有设置说明
- **[04. 功能概述](docs/04_FEATURES.md)** - 机器人的功能和运作方式
- **[05. 交易策略](docs/05_STRATEGY.md)** - 完整的策略逻辑和决策过程
- **[06. 故障排除](docs/06_TROUBLESHOOTING.md)** - 常见问题和解决方案

---

## 4. 安全注意事项

⚠️ **重要提示：**
- 永远不要与任何人分享您的 `PRIVATE_KEY`
- 永远不要将您的 `.env` 文件提交到 git（它已在 `.gitignore` 中）
- 先用小额资金测试
- 首先使用 `MOCK_TRADING=true` 验证一切正常

---

## 5. 工作原理

1. **监控** 来自目标鲸鱼的交易区块链事件（通过 WebSocket 实时监控）
2. **分析** 每笔交易（规模、价格、市场条件）使用多层风险检查
3. **计算** 仓位规模（默认 2%，带分层乘数）和价格（鲸鱼价格 + 缓冲）
4. **执行** 使用优化的订单类型（FAK/GTD）进行按比例复制的交易
5. **重试** 失败的订单，使用智能重新提交逻辑（最多 4-5 次尝试）
6. **保护** 您免受风险保护（断路器）和安全功能的影响
7. **记录** 所有内容到 CSV 文件以供分析

**策略亮点：**
- **2% 仓位缩放：** 在保持有意义仓位的同时降低风险
- **分层执行：** 针对大额（4000+）、中等（2000-3999）和小额（<2000）交易的不同策略
- **多层风险管理：** 4 层安全检查防止危险交易
- **智能定价：** 价格缓冲优化成交率（大额交易更高，小额交易无缓冲）
- **特定运动调整：** 网球和足球市场的额外缓冲

有关功能详细信息，请参阅[功能概述](docs/04_FEATURES.md)，有关完整交易逻辑，请参阅[策略指南](docs/05_STRATEGY.md)。

---

## 6. 功能

- ✅ 实时交易复制
- ✅ 智能仓位管理（默认 2%，可配置）
- ✅ 风险管理断路器
- ✅ 失败时自动重新提交订单
- ✅ 市场缓存系统，实现快速查找
- ✅ 所有交易的 CSV 日志记录
- ✅ 实时市场检测
- ✅ 基于交易规模的分层执行

---

## 7. 高级用法

### 7.1 运行不同模式

```bash
# 标准模式（监控已确认的区块）
cargo run --release

# 内存池模式（更快，但可靠性较低）
cargo run --release --bin mempool_monitor

# 仅监控您自己的成交（不交易）
cargo run --release --bin trade_monitor

# 验证配置
cargo run --release --bin validate_setup
```

### 7.2 构建生产版本

```bash
# 优化的发布版本构建
cargo build --release

# 二进制文件位于：target/release/pm_bot.exe (Windows)
#                        target/release/pm_bot (macOS/Linux)
```

---

## 8. 输出文件

- `matches_optimized.csv` - 所有检测到和执行的交易
- `.clob_creds.json` - 自动生成的 API 凭据（请勿修改）
- `.clob_market_cache.json` - 市场数据缓存（自动更新）

---

## 9. 获取帮助

1. 查看[故障排除指南](docs/06_TROUBLESHOOTING.md)
2. 运行配置验证器：`cargo run --release --bin validate_setup`
3. 对照 `.env.example` 检查您的 `.env` 文件
4. 查看控制台输出中的错误消息
5. 查看[策略指南](docs/05_STRATEGY.md)以了解机器人逻辑

---

## 10. 免责声明

此机器人按原样提供。交易涉及金融风险。请自行决定使用。在使用真实资金之前请充分测试。作者不对任何损失负责。

☕ **请我喝杯咖啡 (EVM):** `0xd9c5d6111983ea3692f1d29bec4ac7d6f723217a`


