# 完整设置指南

本指南将引导您从零开始设置 Polymarket 跟单交易机器人，即使您没有编程经验。

## 目录

1. [前置条件](#1-前置条件)
2. [安装 Rust](#2-安装-rust)
3. [设置您的钱包](#3-设置您的钱包)
4. [获取 API 密钥](#4-获取-api-密钥)
5. [查找鲸鱼地址](#5-查找鲸鱼地址)
6. [配置机器人](#6-配置机器人)
7. [测试您的设置](#7-测试您的设置)
8. [运行机器人](#8-运行机器人)
9. [下一步](#9-下一步)
10. [安全清单](#10-安全清单)
11. [需要帮助？](#11-需要帮助)

---

## 1. 前置条件

在开始之前，请确保您有：

- 运行 Windows、macOS 或 Linux 的计算机
- 互联网连接
- 文本编辑器（记事本、VS Code 或任何文本编辑器）
- 基本的计算机技能（打开文件、复制文本）

---

## 2. 设置您的钱包

### 2.1 选项 1：创建新钱包（推荐用于测试）

1. 安装 [MetaMask](https://metamask.io/) 浏览器扩展
2. 创建新钱包（或使用现有钱包）
3. **添加 Polygon 网络：**
   - 点击网络下拉菜单（左上角）
   - 点击"添加网络"
   - 填写：
     - 网络名称：`Polygon Mainnet`
     - RPC URL：`https://polygon-rpc.com`
     - 链 ID：`137`
     - 货币符号：`MATIC`
     - 区块浏览器：`https://polygonscan.com`

4. **获取您的私钥：**
   - 点击账户图标（右上角）
   - 点击"账户详情"
   - 点击"导出私钥"
   - 输入您的密码
   - **复制私钥**（这是您的 `PRIVATE_KEY` - 请保密！）
   - 如果存在 `0x` 前缀则删除

5. **获取您的地址：**
   - 您的地址显示在账户名称下方
   - 看起来像：`0x1234...5678`
   - 复制此地址（这是您的 `FUNDER_ADDRESS`）

6. **为钱包充值：**
   - 您需要在 Polygon 上有 USDC 或 USDC.e
   - 从以太坊桥接到 Polygon，或在交易所购买
   - 测试推荐最低金额：$50-100

### 2.2 选项 2：使用现有钱包

如果您已经在 Polygon 上有资金的钱包：
1. 导出您的私钥（见上文）
2. 获取您的钱包地址
3. 确保您有 USDC/USDC.e 用于交易

---

## 3. 获取 API 密钥

机器人需要连接到 Polygon 区块链的 WebSocket。您将使用 Alchemy 或 Chainstack。

### 3.1 选项 1：Alchemy（推荐）

1. 访问 https://www.alchemy.com/
2. 点击"Create App"或"Sign Up"
3. 填写：
   - 应用名称：`Polymarket Bot`
   - 链：`Polygon`
   - 网络：`Polygon Mainnet`
4. 创建后，点击您的应用
5. 找到"API Key"部分
6. 复制 API 密钥（这是您的 `ALCHEMY_API_KEY`）

**免费套餐包括：** 每月 300M 计算单位（对这个机器人来说绰绰有余）

### 3.2 选项 2：Chainstack（替代方案）

1. 访问 https://chainstack.com/
2. 注册免费账户
3. 创建新项目
4. 添加 Polygon Mainnet 节点
5. 获取您的 WebSocket URL
6. 从 URL 中提取 API 密钥（这是您的 `CHAINSTACK_API_KEY`）

---

## 4. 查找鲸鱼地址

"鲸鱼"是您想要复制的成功交易者。以下是查找方法：

### 4.1 方法 1：Polymarket 排行榜

1. 访问 https://polymarket.com/leaderboard
2. 寻找高胜率和利润的交易者
3. 点击交易者的个人资料
4. 找到他们的钱包地址（通常在个人资料中可见）
5. 复制地址（40 个字符，如果存在 `0x` 前缀则删除）

### 4.2 方法 2：分析最近的获胜者

1. 前往 Polymarket 市场
2. 查看"已结算"市场
3. 找到有大额支付的市场
4. 点击获胜仓位
5. 记录持有获胜仓位的钱包地址
6. 研究这些地址以找到持续获胜者

### 4.3 方法 3：社交媒体/社区

- 查看 Polymarket Discord/Telegram
- 寻找分享地址的交易者
- 在复制之前验证他们的交易记录

**重要提示：** 在复制之前，始终验证鲸鱼的表现。过去的表现不能保证未来的结果。

---

## 5. 配置机器人

### 5.1 步骤 1：克隆/下载仓库

如果您有 git：
```bash
git clone https://github.com/oxmoei/Polymarket-Copy-Trading-Bot
cd Polymarket-Copy-Trading-Bot
```

或下载并解压 ZIP 文件。

### 5.2 步骤 2：安装依赖

**Windows (PowerShell):**
```powershell
Set-ExecutionPolicy Bypass -Scope CurrentUser
.\install.ps1
```

**macOS/Linux/WSL:**
```bash
./install.sh
```

### 5.3 步骤 3：创建您的 .env 文件

**Windows (PowerShell):**
```powershell
Copy-Item .env.example .env
```

**macOS/Linux:**
```bash
cp .env.example .env
```

**或手动：**
1. 复制 `.env.example`
2. 将副本重命名为 `.env`（Linux/macOS 上无扩展名）

### 5.4 步骤 4：编辑 .env 文件

在任何文本编辑器中打开 `.env`。您会看到类似这样的内容：

```env
PRIVATE_KEY=your_private_key_here
FUNDER_ADDRESS=your_wallet_address_here
TARGET_WHALE_ADDRESS=target_whale_address_here
ALCHEMY_API_KEY=your_alchemy_api_key_here
```

替换每个值：

1. **PRIVATE_KEY**: 粘贴您的钱包私钥（无 `0x` 前缀）
2. **FUNDER_ADDRESS**: 粘贴您的钱包地址（可以有 `0x` 或没有）
3. **TARGET_WHALE_ADDRESS**: 粘贴鲸鱼地址（无 `0x` 前缀）
4. **ALCHEMY_API_KEY**: 粘贴您的 Alchemy API 密钥

**示例（不要使用这些 - 它们是假的）：**
```env
PRIVATE_KEY=0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef
FUNDER_ADDRESS=0x1234567890123456789012345678901234567890
TARGET_WHALE_ADDRESS=204f72f35326db932158cba6adff0b9a1da95e14
ALCHEMY_API_KEY=abc123xyz789
```

### 5.5 步骤 5：设置初始交易模式

对于首次运行，设置：
```env
ENABLE_TRADING=false
MOCK_TRADING=true
```

这可以让您看到机器人会做什么，而不会实际交易。

---

## 6. 测试您的设置

### 6.1 步骤 1：验证配置

运行配置检查器：
```bash
cargo run --release --bin validate_setup
```

**它检查的内容：**
- 所有必需值都已设置
- 地址格式正确
- API 密钥格式有效
- 私钥格式正确

**在继续之前修复它报告的任何错误。**

### 6.2 步骤 2：构建机器人

```bash
cargo build --release
```

第一次构建需要 5-10 分钟（下载依赖项）。

### 6.3 步骤 3：测试运行（模拟模式）

确保您的 `.env` 有：
```env
ENABLE_TRADING=false
MOCK_TRADING=true
```

然后运行：
```bash
cargo run --release
```

**预期结果：**
- 机器人连接到区块链
- 您看到连接消息
- 当鲸鱼交易时，您看到模拟交易消息
- 不会下实际订单

**如果您看到错误：**
- 查看[故障排除指南](06_TROUBLESHOOTING.md)
- 验证您的 API 密钥是否正确
- 确保您的地址格式正确

---

## 7. 运行机器人

### 7.1 步骤 1：启用交易

一旦您测试过并确信一切正常：

1. 编辑 `.env`
2. 设置：
   ```env
   ENABLE_TRADING=true
   MOCK_TRADING=false
   ```

3. 保存文件

### 7.2 步骤 2：运行机器人

```bash
cargo run --release
```

**Windows 用户：** 您也可以使用 `run.bat`（设置后双击）。

### 7.3 步骤 3：监控输出

您会看到类似这样的消息：
```
🚀 Starting trader. Trading: true, Mock: false
🔌 Connected. Subscribing...
⚡ [B:12345] BUY_FILL | $100 | 200 OK | ...
```

**每条消息的含义：**
- `[B:12345]` = 检测到交易的区块号
- `BUY_FILL` = 交易类型（BUY 或 SELL）
- `$100` = 鲸鱼交易的美元价值
- `200 OK` = 您的订单已成功提交
- 后面的数字 = 您的成交详情

### 7.4 步骤 4：检查结果

- **实时：** 实时查看控制台输出
- **CSV 日志：** 检查 `matches_optimized.csv` 查看所有交易
- **Polymarket：** 在 Polymarket 网站上检查您的仓位

---

## 8. 下一步

- 阅读[功能指南](04_FEATURES.md)了解机器人的功能
- 根据需要调整[配置](03_CONFIGURATION.md)设置
- 如果遇到问题，查看[故障排除](06_TROUBLESHOOTING.md)

---

## 9. 安全清单

在使用真实资金运行之前：

- [ ] 在模拟模式下成功测试
- [ ] 验证所有地址正确
- [ ] 钱包中有足够的资金
- [ ] 了解涉及的风险
- [ ] 从小额测试开始
- [ ] 有停止机器人的方法（Ctrl+C）
- [ ] 安全备份您的 `.env` 文件

---

## 10. 需要帮助？

1. 查看[故障排除指南](06_TROUBLESHOOTING.md)
2. 使用 `validate_setup` 验证您的配置
3. 仔细查看错误消息
4. 确保所有前置条件都已安装
