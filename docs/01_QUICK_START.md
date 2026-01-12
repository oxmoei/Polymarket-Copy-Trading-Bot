# 快速开始指南

最快让机器人运行起来的方法（5 分钟）。

## 目录

1. [前置条件检查清单](#1-前置条件检查清单)
2. [5 分钟设置](#2-5-分钟设置)
3. [Windows 用户](#3-windows-用户)
4. [常见首次使用问题](#4-常见首次使用问题)
5. [需要更多帮助？](#5-需要更多帮助)
6. [安全提醒](#6-安全提醒)

---

## 1. 前置条件检查清单

- [ ] 已安装 Rust (https://rustup.rs/)
- [ ] 已创建 Polymarket 账户
- [ ] MetaMask 钱包在 Polygon 上有 USDC
- [ ] Alchemy 账户（免费）- https://www.alchemy.com/

## 2. 5 分钟设置

### 2.1 步骤 1：设置环境（1 分钟）

```bash
# 复制示例配置
cp .env.example .env  # Linux/macOS
# 或
copy .env.example .env  # Windows

# 编辑 .env 文件 - 填写这 4 个值：
# - PRIVATE_KEY (从 MetaMask：账户详情 → 导出私钥)
# - FUNDER_ADDRESS (您的钱包地址)
# - TARGET_WHALE_ADDRESS (要复制的鲸鱼 - 从 Polymarket 排行榜)
# - ALCHEMY_API_KEY (从 https://www.alchemy.com/)
```

### 2.2 步骤 2：验证配置（30 秒）

```bash
cargo run --release --bin validate_setup
```

修复它报告的任何错误。

### 2.3 步骤 3：在模拟模式下测试（1 分钟）

```bash
# 在 .env 中设置：
# ENABLE_TRADING=false
# MOCK_TRADING=true

cargo run --release
```

观察连接和交易模拟消息。

### 2.4 步骤 4：真实运行（准备就绪时）

```bash
# 在 .env 中设置：
# ENABLE_TRADING=true
# MOCK_TRADING=false

cargo run --release
# 或双击 run.bat (Windows)
```

## 3. Windows 用户

设置好 `.env` 后，只需双击 `run.bat`！

## 4. 常见首次使用问题

**"rustc not found"** → 从 https://rustup.rs/ 安装 Rust 并重启终端

**".env file not found"** → 将 `.env.example` 复制为 `.env`

**"PRIVATE_KEY required"** → 打开 `.env`，填写您的私钥（如果存在 `0x` 前缀则删除）

**"API key required"** → 从 https://www.alchemy.com/ 获取免费密钥，添加到 `.env`

## 5. 需要更多帮助？

- **详细设置：** 查看 [02_SETUP_GUIDE.md](02_SETUP_GUIDE.md)
- **配置选项：** 查看 [03_CONFIGURATION.md](03_CONFIGURATION.md)
- **遇到问题？** 查看 [06_TROUBLESHOOTING.md](06_TROUBLESHOOTING.md)
- **工作原理：** 查看 [04_FEATURES.md](04_FEATURES.md)
- **策略逻辑：** 查看 [05_STRATEGY.md](05_STRATEGY.md)

## 6. 安全提醒

⚠️ **在使用真实资金运行之前：**
- 首先在模拟模式下测试（`MOCK_TRADING=true`）
- 从小额开始
- 监控您的仓位
- 了解风险

✅ **您的 `.env` 文件包含机密信息** - 永远不要分享它或提交到 git！
