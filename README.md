# Smart-Contract-Management-
A decentralized application designed for charity donations, leveraging Ethereum blockchain technology. It features a Solidity smart contract to facilitate donation management,
complemented by a React-based frontend for seamless interaction with the contract.

# Description 
## 1. Smart Contract (Assessment.sol):
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

# SMART CONTRACT - SOLIDITY CODE 
      // SPDX-License-Identifier: UNLICENSED
      pragma solidity ^0.8.9;
   
      contract CharityDonation {
          // State variables
          address payable public contractOwner;  
           // Address of the contract owner (who deployed the contract)
          uint256 public totalContribution;      
           // Total amount of contributions received
          uint256 public contributionGoal;       
           // Goal amount that contributors are aiming to reach
      
          // Events
          event ContributionReceived(address indexed contributor, uint256 amount); 
          // Event emitted when a contribution is received
          event FundsExtracted(uint256 amount);   
          // Event emitted when funds are withdrawn by the contract owner
          event GoalDefined(uint256 goalAmount);  
          // Event emitted when the contribution goal is defined
      
          // Constructor - executed once upon contract deployment
          constructor() {
              contractOwner = payable(msg.sender); 
              // Set the contract owner to the address deploying the contract
          }
   
       // Function to define the contribution goal
       function defineGoal(uint256 _goalAmount) external {
           require(msg.sender == contractOwner, "Only the owner can set the goal."); 
           // Only the owner can define the goal
           require(_goalAmount > 0, "Goal amount must be greater than zero."); 
           // Goal amount must be positive
   
           contributionGoal = _goalAmount; 
           // Set the contribution goal
   
           emit GoalDefined(_goalAmount); 
           // Emit an event indicating the goal has been defined
       }
   
   
   
       // Function for contributors to send ETH and contribute
       function contribute() external payable {
           require(msg.value > 0, "Contribution must be greater than zero."); 
           // Contribution must be positive
   
           totalContribution += msg.value;
           // Increase the total contribution amount
   
           emit ContributionReceived(msg.sender, msg.value); 
           // Emit an event indicating a contribution has been received
       }
   
       // Function for the contract owner to withdraw funds
       function extractFunds() external {
           require(msg.sender == contractOwner, "Only the owner can withdraw funds.");
            // Only the owner can withdraw funds
           require(totalContribution > 0, "No funds available for withdrawal.");
            // Ensure there are funds to withdraw
   
           uint256 amount = totalContribution; 
           // Store the total contribution amount
           totalContribution = 0; 
           // Reset the total contribution amount to zero
   
           (bool success, ) = contractOwner.call{value: amount}(""); 
           // Transfer the funds to the contract owner
           require(success, "Withdrawal failed."); 
           // Ensure the transfer was successful
   
           emit FundsExtracted(amount); 
           // Emit an event indicating funds have been withdrawn
       }
      }



# FRONTEND 

      import { useState, useEffect } from "react";  // Importing necessary hooks from React
      import { ethers } from "ethers";  // Importing ethers library for Ethereum interactions
      import CharityDonationABI from "../artifacts/contracts/Assessment.sol/CharityDonation.json";  // Importing ABI of the smart contract
      
      export default function HomePage() {
        const [account, setAccount] = useState(null);  // State variable to store current Ethereum account
        const [contract, setContract] = useState(null);  // State variable to store instance of the smart contract
        const [balance, setBalance] = useState("0");  // State variable to store balance of contributions
      
        const contractAddress = "0x5fbdb2315678afecb367f032d93f642f64180aa3";  // Address of the deployed smart contract
        const CharityDonation_ABI = CharityDonationABI.abi;  // ABI (Application Binary Interface) of the smart contract
      
     useEffect(() => {
       // Function to initialize contract and account information
       const initialize = async () => {
         try {
           if (window.ethereum) {
             const provider = new ethers.providers.Web3Provider(window.ethereum);  // Create Web3 provider using MetaMask
             const signer = provider.getSigner();  // Get the signer (account) from the provider
             const contractInstance = new ethers.Contract(contractAddress, CharityDonation_ABI, signer);  // Create contract instance
             setContract(contractInstance);  // Set the contract instance in state
     
             const accounts = await provider.listAccounts();  // Retrieve list of accounts connected to MetaMask
             if (accounts.length > 0) {
               setAccount(accounts[0]);  // Set the first account as the current account
             }
     
             const balance = await contractInstance.totalContribution();  // Retrieve total contribution balance from the contract
             setBalance(ethers.utils.formatEther(balance));  // Format the balance to Ether and update state
           }
         } catch (error) {
           console.error("Error initializing:", error);  // Log any errors that occur during initialization
         }
       };
     
       initialize();  // Call the initialization function when component mounts
     }, []);  // Empty dependency array ensures useEffect runs only once on component mount
   
     // Function to connect to MetaMask wallet
     const connectWallet = async () => {
       try {
         await window.ethereum.request({ method: "eth_requestAccounts" });  // Request access to user's MetaMask accounts
         const provider = new ethers.providers.Web3Provider(window.ethereum);  // Create Web3 provider
         const accounts = await provider.listAccounts();  // Retrieve updated list of accounts
         if (accounts.length > 0) {
           setAccount(accounts[0]);  // Set the first account as the current account
         }
       } catch (error) {
         console.error("Error connecting to MetaMask:", error);  // Log any errors that occur during connection
       }
     };
     
     // Function to contribute ETH to the smart contract
     const contribute = async (amount) => {
       if (contract) {
         try {
           const tx = await contract.contribute({
             value: ethers.utils.parseEther(amount.toString())  // Convert amount to wei and send with the transaction
           });
           await tx.wait();  // Wait for transaction to be mined
           const newBalance = await contract.totalContribution();  // Retrieve updated total contribution balance
           setBalance(ethers.utils.formatEther(newBalance));  // Format the new balance to Ether and update state
         } catch (error) {
           console.error("Error contributing:", error);  // Log any errors that occur during contribution
         }
       }
     };
   
     // Function to extract funds from the smart contract (only accessible to contract owner)
     const extractFunds = async () => {
       if (contract) {
         try {
           const tx = await contract.extractFunds();  // Call the smart contract function to withdraw funds
           await tx.wait();  // Wait for transaction to be mined
           const newBalance = await contract.totalContribution();  // Retrieve updated total contribution balance
           setBalance(ethers.utils.formatEther(newBalance));  // Format the new balance to Ether and update state
         } catch (error) {
           console.error("Error extracting funds:", error);  // Log any errors that occur during fund extraction
         }
       }
     };
   
     // JSX rendering for the UI
     return (
       <main className="container">
         <header>
           <h1>Charity Donation</h1>
         </header>
         {!account && (
           <button className="connect-button" onClick={connectWallet}>Connect to MetaMask</button>
         )}
         {account && (
           <div className="account-info">
             <p>Your Account: {account}</p>
             <p>Total Contribution: {balance} ETH</p>
             <div className="buttons">
               <button className="contribute-button" onClick={() => contribute(1)}>Contribute 1 ETH</button>
               <button className="extract-button" onClick={extractFunds}>Extract Funds</button>
             </div>
           </div>
         )}
         <style jsx>{`
           .container {
             display: flex;
             flex-direction: column;
             align-items: center;
             justify-content: center;
             height: 100vh;
             background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
             font-family: 'Times New Roman', sans-serif;
           }
           header {
             margin-bottom: 2rem;
             text-align: center;
           }
           h1 {
             font-size: 3rem;
             color: #333;
             margin-bottom: 0.5rem;
           }
           .connect-button,
           .contribute-button,
           .extract-button {
             padding: 1rem 2rem;
             margin: 0.5rem;
             font-size: 1.2rem;
             border: none;
             border-radius: 5px;
             cursor: pointer;
             transition: background 0.3s ease;
           }
           .connect-button {
             background-color: #6c63ff;
             color: #fff;
           }
           .connect-button:hover {
             background-color: #5a54d8;
           }
           .account-info {
             text-align: center;
           }
           .buttons {
             margin-top: 1rem;
           }
           .contribute-button {
             background-color: #28a745;
             color: #fff;
           }
           .contribute-button:hover {
             background-color: #218838;
           }
           .extract-button {
             background-color: #dc3545;
             color: #fff;
           }
           .extract-button:hover {
             background-color: #c82333;
           }
           p {
             font-size: 1.2rem;
             color: #555;
             margin: 0.5rem 0;
           }
         `}</style>
       </main>
     );
      }


**Interact with the contract:**
- After deployment, interact with the contract by:
 * Connect Wallet: Link your MetaMask wallet to the application.
 * View Account Information: Displays your Ethereum account details and current balance.
 * Contribute: Use the contribute function to donate Ether to the charity campaign.
 * Extract Funds: If you're the contract owner, utilize the extractFunds function to withdraw collected funds.
 * Set Goal: If you're the contract owner, use the defineGoal function to establish a fundraising goal.


# AUTHOR
Metacrafter Student Ivanne Cres Yabut
https://x.com/ivanne_cres

# License
This project is licensed under the author License - see the LICENSE.md file for details. 

