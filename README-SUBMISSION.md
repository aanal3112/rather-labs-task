# App Testing Guide & Changelog

This document provides step-by-step instructions on how to set up, configure, and test the application, along with a summary of recent changes.

---

## User Information
- **Name:** **Aanal Shah**
- **Contact:** **+917818067447**
- **Email:** **shah.aanal31@gmail.com**

---

## How to Test the App

### Prerequisites
* **MetaMask** extension installed in your web browser.

### Step 1: Install Dependencies
If you haven't already, install the required dependencies for both the frontend and the smart contracts:
```bash
npm install
npm install --prefix contracts
```

### Step 2: Start the Local Hardhat Node
Open a new terminal window (**Terminal 1**) and start the local blockchain network:
```bash
npm run contracts:node
```
*Leave this terminal running in the background.*

### Step 3: Deploy the Smart Contracts
Open a second terminal window (**Terminal 2**) and run the deployment script:
```bash
npm run contracts:deploy
```
This script will output the deployed contract addresses. Copy these addresses and paste them into a `.env.local` file in your root directory:
```env
NEXT_PUBLIC_QUEST_ESCROW_ADDRESS=0x...
NEXT_PUBLIC_MOCK_USDC_ADDRESS=0x...
```

### Step 4: Start the Next.js Development Server
Open a third terminal window (**Terminal 3**) and launch the frontend application:
```bash
npm run dev
```
Once started, open your browser and navigate to [http://localhost:3000](http://localhost:3000).

---

## MetaMask Configuration

To interact with the local blockchain, you must add the Hardhat network to MetaMask and import the pre-funded test accounts.

### 1. Add Custom RPC Network
In MetaMask, navigate to Networks -> Add Network -> Add a network manually, and fill in the following details:
* **Network Name:** `Hardhat`
* **RPC URL:** `http://127.0.0.1:8545`
* **Chain ID:** `31337`
* **Currency Symbol:** `ETH`

### 2. Import Test Accounts
The Hardhat node initialized in **Step 2** prints a list of available accounts and private keys on startup. Import the private keys for the first two accounts into MetaMask:
* **Account [0]:** Quest Owner / Poster (referred to as **Account A**)
* **Account [1]:** Worker / Contributor (referred to as **Account B**)

---

## UI Walkthrough Checklist

Follow this sequence of steps to verify the end-to-end functionality of the platform:

| Step | Account | Target Page / Location | Action Required | Expected Result |
| :--- | :--- | :--- | :--- | :--- |
| **1** | **A** (Account 0) | Home / Header | Click **Connect Wallet** | Header updates to display Account A's public address. |
| **2** | **A** (Account 0) | `/quests/create` | Fill out Title, Description, Reward, and Deadline, then submit | Contract interaction prompt appears; transaction posts successfully. |
| **3** | **A** (Account 0) | `/quests` | Navigate to the public listings page | The newly created quest appears dynamically with an `Open` status. |
| **4** | **B** (Account 1) | `/quests/[id]` | Switch MetaMask to Account 1, view the specific quest page, and click **Accept** | Quest status updates to reflect active engagement by a worker. |
| **5** | **B** (Account 1) | `/quests/[id]` | Click **Submit deliverable** and input an IPFS URI (e.g., `ipfs://QmTest`) | Deliverable link is recorded on-chain or via backend hooks. |
| **6** | **A** (Account 0) | `/quests/[id]` | Switch MetaMask back to Account 0 and click **Approve & Pay** | Funds are released from escrow; status changes to `Completed`. |
| **7** | **B** (Account 1) | MetaMask Wallet | Check the asset balances for Account 1 | Account 1's ETH balance should increase by approximately **97%** of the designated reward amount (accounting for gas/fees). |

---

## Unit Testing

If you wish to test the smart contract backend logic independently without running the UI development server, execute the following commands:

```bash
npm run test
# OR
cd contracts && npx hardhat test
```

---

## Changelog

The following files have received updates and structural changes in this version:

* `contracts/contracts/QuestEscrow.sol` — Smart contract backend implementation controlling funds routing, state changes, and fulfillment verification.
* `lib/hooks/useQuestEscrow.ts` — Frontend React hook facilitating Web3 connectivity, state synchronized mutations, and contract state queries.