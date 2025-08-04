# ⛓️ ChainOath: 去中心化链上契约平台

**ChainOath** 是一个完全去中心化的 DApp，它允许用户通过钱包交互来创建、签署和验证链上“承诺协议”。无论是个人习惯养成、团队目标达成，还是朋友间的趣味约定，所有契约的生命周期都由智能合约和 IPFS 驱动，确保了过程的**可验证性、不可篡改性和完全公开性**。

---

## 🧭 一、项目愿景与核心理念

在数字时代，信任的建立往往依赖于中心化的第三方机构。ChainOath 旨在打破这一模式，通过区块链技术，将“承诺”这一社会行为赋予密码学保障。我们相信，当承诺的履行过程变得透明、自动且不可干预时，人与人之间的协作将达到新的高度。

- **去中心化信任**：代码即法律，智能合约是唯一的中介。
- **用户主权**：用户通过钱包完全掌控自己的数据和资产。
- **透明与公平**：所有规则和执行过程都在链上公开，对所有参与者一视同仁。

---

## 🧱 二、技术架构与核心组件

ChainOath 采用纯 DApp 架构，前端直接与区块链和去中心化存储交互，无任何中心化后端服务器。这保证了平台的抗审查性和高可用性。

| 模块 | 技术选型 | 选型理由 |
| :--- | :--- | :--- |
| **钱包交互** | `WalletConnect`, `MetaMask` | 覆盖最广泛的用户群体，提供无缝的 Web3 体验。 |
| **前端框架** | `React` + `Vite` + `TypeScript` | 现代、高效的开发体验，类型安全保证代码质量。 |
| **UI 库** | `Tailwind CSS` | 原子化 CSS，快速构建美观、响应式的界面。 |
| **状态管理** | `Zustand` | 轻量、简洁的全局状态管理方案，对 React Hooks 友好。 |
| **区块链交互** | `Wagmi` + `Viem` + `Ethers.js` | `Wagmi` 提供了强大的 React Hooks，`Viem` 轻量高效，`Ethers.js` 作为备用和某些复杂场景的补充。 |
| **智能合约** | `Solidity` + `Hardhat` | `Solidity` 是以太坊生态标准，`Hardhat` 提供强大的开发、测试和部署环境。 |
| **去中心化存储** | `IPFS` (通过 `Web3.Storage` 或 `Pinata`) | 存储誓约详情等大数据，降低链上存储成本，实现内容寻址。 |
| **目标网络** | `Ethereum Testnet` (Sepolia) | 主流测试网络，拥有丰富的工具和生态支持。 |
| **NFT 奖励 (可选)** | `ERC-721` (通过 `OpenZeppelin`) | 标准化的 NFT 接口，可作为成就证明或特殊奖励。 |

---

## 📋 三、页面结构与核心功能

应用的核心用户旅程围绕着“创建”、“签署”、“履行”和“验证”四个环节展开。

### 1️⃣ **首页 (`/`) - 发现与入口**

- **目的**：吸引用户，清晰传达产品价值，并提供核心操作的入口。
- **核心元素**：
    - **醒目的 Logo 和 Slogan**：例如 “ChainOath: Code Your Promise, Trust the Chain.”
    - **项目简介**：用动画或图文形式生动介绍平台如何运作。
    - **`Connect Wallet` 按钮**：页面右上角常驻，使用 `Wagmi` 的 `useConnect` Hook 实现。
    - **核心操作按钮**：
        - `[ 创建我的誓约 ]` -> `/create`
        - `[ 查看我的誓约 ]` -> `/my-oaths` (需连接钱包)

### 2️⃣ **创建誓约页 (`/create`) - 定义承诺**

- **目的**：引导用户清晰、完整地定义一个链上契约。
- **表单结构**：
    - **誓约标题** (必填): `string`
    - **誓约详情** (可选): `string` (Markdown/富文本), 上传至 IPFS。
    - **参与人地址** (至少一个): `address[]`
    - **打卡周期**: `select` (每日 / 每周)
    - **持续时间**: `number` (天)
    - **个人押金**: `number` (ETH)
    - **公共奖励池**: `number` (ETH, 可选)
- **提交流程**：
    1.  用户填写表单，点击 `[ 发起誓约 ]`。
    2.  若包含誓约详情，前端将其上传至 IPFS，获取 `CID`。
    3.  构造交易，调用合约的 `createOath()` 方法，并附带所有参数和押金。
    4.  交易成功后，跳转至该誓约的详情页 (`/oath/:id`)，并提示用户分享链接给其他参与者。

### 3️⃣ **我的誓约页 (`/my-oaths`) - 管理与追踪**

- **目的**：展示用户所有相关（创建或参与）的誓约，并提供交互入口。
- **核心元素**：
    - **`Oath Card` 列表**：每个卡片展示誓约的核心信息（标题、状态、进度、起止时间）。
    - **状态标签**：用不同颜色区分 `[ 待签署 ]`, `[ 进行中 ]`, `[ 已完成 ]`, `[ 已违约 ]`。
    - **交互按钮**：
        - `[ 查看详情 ]` -> `/oath/:id`
        - `[ 签名加入 ]` (仅对未签名的参与者显示)
        - `[ 我要打卡 ]` (在打卡周期内可用)
        - `[ 领取奖励 ]` (在完成后可用)

### 4️⃣ **誓约详情页 (`/oath/:id`) - 透明与验证**

- **目的**：提供单个誓约的所有详细信息，作为信任的最终来源。
- **核心元素**：
    - **合约基础信息**：创建者、所有参与人、当前状态、起止时间、押金/奖励池金额。
    - **誓约内容**：从 IPFS 读取并展示誓约详情。
    - **参与人状态**：列表展示每个参与人的签名状态和打卡记录。
    - **时间线/进度条**：可视化展示整个誓约的生命周期和每个人的打卡进度。
    - **交互按钮**：根据用户身份和合约状态，动态显示 `[ 签名 ]`, `[ 打卡 ]`, `[ 领取奖励 ]` 等操作按钮。

---

## ⛓️ 四、智能合约设计（核心逻辑）

合约是整个平台信任的基石，其设计需兼顾安全性、可扩展性和 Gas 效率。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

struct Oath {
    string title;               // 誓约标题
    string ipfsCID;             // 存储在 IPFS 的内容哈希
    address creator;            // 创建者
    address[] participants;     // 所有参与者
    mapping(address => bool) hasSigned; // 签名状态
    mapping(address => uint256[]) checkIns; // 打卡时间戳记录
    uint256 interval;           // 打卡周期 (秒)
    uint256 startTime;          // 开始时间 (所有人都签名后)
    uint256 duration;           // 持续时间 (秒)
    uint256 deposit;            // 单人押金
    uint256 rewardPool;         // 公共奖励池
    State state;                // 合约状态
}

enum State { Pending, Active, Completed, Breached }

// ... 核心函数接口 ...

function createOath(
    string calldata title,
    string calldata ipfsCID,
    address[] calldata participants,
    uint256 interval,
    uint256 duration
) external payable;

function signOath(uint256 oathId) external;

function checkIn(uint256 oathId) external;

function claimReward(uint256 oathId) external;

function getOath(uint256 oathId) public view returns (Oath memory);
```

---

## 🧪 五、开发、测试与部署流程

我们遵循专业的软件开发生命周期，确保代码质量和应用稳定性。

1.  **本地开发**：
    - 启动 Hardhat 本地网络: `npx hardhat node`
    - 部署合约到本地: `npx hardhat run scripts/deploy.ts --network localhost`
    - 启动前端开发服务器: `npm run dev`
2.  **单元测试**：
    - 使用 `Hardhat` + `Chai` + `Ethers.js` 对合约进行全面的功能和安全测试。
    - `npx hardhat test`
3.  **前端测试**：
    - 使用 `React Testing Library` + `Vitest` 对核心组件进行单元和集成测试。
    - 使用 `Mock Wagmi client` 模拟钱包交互和合约调用。
4.  **部署**：
    - 将合约部署到 `Sepolia` 测试网。
    - 前端应用通过 `Vercel` 或 `Netlify` 部署，实现 CI/CD。

---

## ✅ 六、未来可拓展方向

- **社交功能**：允许用户评论、点赞或“见证”他人的誓约。
- **模板市场**：用户可以创建和分享常用的誓约模板。
- **DAO 治理**：引入治理代币，由社区决定平台的发展方向和参数。
- **多链支持**：将 ChainOath 扩展到其他 EVM 兼容链，如 Polygon, Arbitrum 等。
