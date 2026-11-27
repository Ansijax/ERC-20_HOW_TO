# ERC-20 Token Deployment Guide

This document explains how to set up a Foundry-based development environment, customize an ERC-20 token smart contract, deploy it to the **Sepolia Testnet**, and verify it on **Etherscan**.  
It is intended for beginners as well as developers who want a structured, step-by-step workflow.

---

## 📦 Prerequisites

Before starting, install **Foundry**, a fast and modern toolkit for Ethereum development:  
https://getfoundry.sh/

Foundry includes essential tools such as:

- **forge** → for building, testing, and deploying smart contracts  
- **cast** → for interacting with Ethereum networks from the command line  

You will also need:

- A **MetaMask wallet** to hold your testnet Ether and manage your keys  
- Some **Sepolia test ETH** to pay for deployment gas fees  
- An **Etherscan API key** to verify your smart contract’s source code  

> **Note:** after running the Foundry installer, `foundryup` may not be found
> because your shell has not reloaded its PATH. Run `source ~/.bashrc` (or open
> a new terminal), or add `export PATH="$PATH:$HOME/.foundry/bin"` to your
> shell profile. Then run `foundryup` and check with `forge --version`.

---

## 🚀 1. Create a New Foundry Project

To begin, create a new Foundry project using:

```bash
forge init <PROJECT_NAME>
```

This command generates a clean folder structure containing:

- a `src/` directory for your Solidity contracts  
- a `test/` directory for automated tests  
- configuration files already prepared for use with Foundry  

---

## 📥 2. Clone the ERC-20 Template

Clone the repository containing the example ERC-20 implementation:

```bash
git clone git@github.com:Ansijax/ERC-20_HOW_TO.git
```

After the repository is downloaded, locate the `ERC-20.sol` file and move it into your Foundry project’s `src/` directory.

This file contains a ready-to-use ERC-20 token template that you will customize.  
Placing it inside `src/` ensures that Foundry can compile it correctly when you run build or deployment commands.

---

## ✏️ 3. Customize the ERC-20 Contract

Open the `ERC-20.sol` file and modify it to match the characteristics of your token.  
You will need to update:

- **Token name** → The full name of your token (e.g., *MyToken*)  
- **Token symbol** → A shorthand identifier (e.g., *MTK*)  
- **Decimals** → The number of decimal units (commonly `18`)  
- **Contract class name** → This should reflect the token’s name for clarity  

The template does not compile until the three `TODO` lines in the constructor
are filled in. For example:

```solidity
name = "MyToken";
decimals = 2;
symbol = "12345";
```

---

## 🛠 4. Build the Project

Compile your project using:

```bash
forge build
```

This command will:

- Analyze your smart contract code  
- Compile it using Foundry’s integrated Solidity compiler  
- Notify you about any errors or warnings  

A successful build indicates that your contract is syntactically correct and ready for deployment.  
If error messages appear, review the contract carefully and ensure Solidity versions and imports match your setup.

---

## 🌐 5. Prepare for Deployment on Sepolia

Before deploying, complete these setup steps:

### Enable Testnets in MetaMask
By default, MetaMask hides test networks.  
Enable them following this guide:  
https://support.metamask.io/it/configure/networks/how-to-view-testnets-in-metamask/

### Obtain Sepolia Test ETH
Sepolia ETH is required to pay for gas fees during deployment.  
You can request test ETH from faucets such as:

- https://sepolia-faucet.pk910.de/  
- or any other trusted Sepolia faucet

Typically, receiving a small amount (0.1–0.5 Sepolia ETH) is enough for multiple deployments.

### Retrieve Your Private Key
You will need your private key so that `forge` can sign the deployment transaction:  
https://support.metamask.io/it/configure/accounts/how-to-export-an-accounts-private-key/

This key authorizes deployments from your wallet address.  
Keep it secure and **use only testnet accounts** for development.

---

## 🚢 6. Deploy the ERC-20 Token

Deploy your custom token to the Sepolia Testnet using:

```bash
forge create src/<CONTRACT_FILE_NAME>:<CONTRACT_CLASS_NAME>   --rpc-url https://ethereum-sepolia-rpc.publicnode.com   --private-key <YOUR_PRIVATE_KEY>   --broadcast   --constructor-args <TOTAL_SUPPLY>
```

Note:

- TOTAL_SUPPLY refers to the units of the token that will be deployed. If you've set the token decimals to 5 and you want to release 1000 tokens on deployment, TOTAL_SUPPLY should be 100000000 (10^8)
- Balances are stored in the smallest unit. With `decimals = 2`, sending
  N tokens means passing `N * 100` to `transfer`. Make sure the total supply
  is at least the amount you plan to send.
- To avoid pasting your private key in the command (and in the shell history),
  run `read -s PK` and use `--private-key $PK`.

During deployment:

- Foundry will compile (if needed) and prepare the contract bytecode  
- The transaction will be sent to the Sepolia network  
- Your wallet address will become the owner and initial token holder  

Once the deployment completes, Foundry will display your ETH address, your **token contract address** ("Deployed to:"), and the hash of the token's first transaction.  
The token contract address uniquely identifies your token on the blockchain.

To send tokens:

```
cast send <TOKEN_ADDRESS> "transfer(address,uint256)" <TO> <AMOUNT_IN_UNITS> \
  --rpc-url https://ethereum-sepolia-rpc.publicnode.com --private-key $PK
```

---

## 🔍 7. Verify the Contract on Etherscan

Verifying your contract allows anyone to read your source code directly from Etherscan, increasing transparency and making interactions easier.

### Before verification, you must obtain an Etherscan API Key

- Create an account on Etherscan: https://etherscan.io/register
- Go to **My Profile** -> **API Dashboard**
- Press **+ Add** and create an API Key Token, then copy it.

Use this command:

```bash
forge verify-contract <TOKEN_ADDRESS> src/<CONTRACT_FILE_NAME>:<CONTRACT_CLASS_NAME> \
  --chain sepolia \
  --etherscan-api-key <ETHERSCAN_API_KEY> \
  --constructor-args $(cast abi-encode "constructor(uint256)" <TOTAL_SUPPLY>) \
  --watch
```

`<TOTAL_SUPPLY>` must be the same value passed at deployment, and the source
file must not be modified after deploying.

After verification, your source code and ABI will appear on:  
https://sepolia.etherscan.io/

This step is highly recommended, especially if you intend to share your token or develop applications around it.

---

## 👛 8. View Your Token in MetaMask

If MetaMask does not automatically recognize your token, you can manually add it by:

1. Opening MetaMask  
2. Selecting “Import Tokens”  
3. Entering your **token contract address**  

Detailed instructions here:  
https://support.metamask.io/manage-crypto/tokens/how-to-display-tokens-in-metamask

After importing, your token balance should appear in your wallet, reflecting the initial supply minted during deployment.


I'd like to thank @LucaSforza for drafting the initial version of this how-to.
