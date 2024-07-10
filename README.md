# Smart-Contract-Management-
A decentralized application designed for charity donations, leveraging Ethereum blockchain technology. It features a Solidity smart contract to facilitate donation management,
complemented by a React-based frontend for seamless interaction with the contract.

# Description 
## 1. Smart Contract (chardonation.sol):
The  Solidity smart contract, manages the following key functionalities:
* defineGoal: Enables the contract owner to establish a fundraising goal.
* contribute: Allows users to contribute Ether towards the charity campaign.
* extractFunds: Permits the contract owner to withdraw collected funds once the campaign concludes.

## 2. Frontend (index.js):**
The frontend, a React application (index.js), interfaces with the smart contract via Web3.js. It offers the following capabilities:
* Connect to MetaMask: Facilitates connection of users' MetaMask wallets.
* View Account Information: Presents the user's Ethereum account details and total contributions.
* Contribute: Enables users to donate to the charity campaign.
* Extract Funds: Permits the contract owner to withdraw accumulated funds.

# Getting Started 

To begin with this project, follow these steps:

**Prerequisites:**
- Ensure Node.js is installed on your machine and verify it's the correct version and install the MetaMask extension.

**Installation:**
1. Clone the starter repository:
   ```bash
   git clone https://github.com/MetacrafterChris/SCM-Starter.git
   cd your-repository
   ```
2. Install dependencies:
   ```bash
   npm install
   ```
3. Update dependencies:
   ```bash
   npm outdated && npm update
   ```
4. Fix vulnerabilities:
   ```bash
   npm audit fix
   ```
5. Open two additional terminals in your VS Code:
   - In the second terminal, start the local blockchain node:
     ```bash
     npx hardhat node
     ```
   - In the third terminal, deploy the contract:
     ```bash
     npx hardhat run --network localhost scripts/deploy.js
     ```
6. Back in the first terminal, launch the frontend:
   ```bash
   npm run dev
   ```

**Interact with the contract:**
- After deployment, interact with the contract by:
 * Connect Wallet: Link your MetaMask wallet to the application.
 * View Account Information: Displays your Ethereum account details and current balance.
 * Contribute: Use the contribute function to donate Ether to the charity campaign.
 * Extract Funds: If you're the contract owner, utilize the extractFunds function to withdraw collected funds.
 * Set Goal: If you're the contract owner, use the defineGoal function to establish a fundraising goal.


# AUTHOR


# License
This project is licensed under the author License - see the LICENSE.md file for details. 

