# WaifuToken Security Audit Report

###### tags: `WaifuToken`

## Introduction

### Project overview

The audited scope implements an algorithmic ERC 20 stable coin with the functionality of depositing and exchanging it for others ERC20 tokens.

### Scope of the Audit
The scope of the audit includes the following smart contracts at:
contracts/Oracle.sol
contracts/Waifu.sol
contracts/interfaces/aave.sol
contracts/interfaces/cMarket.sol
contracts/interfaces/compound.sol
contracts/interfaces/controller.sol
contracts/interfaces/UniswapV2Router02.sol
contracts/interfaces/vault.sol

## Security Assessment Methodology

One security beginner auditor and his fluffy cat  are involved in the work on the audit who check the provided source code independently of each other in accordance with the methodology described below:

### 1. Blind audit.

* Zip archive code study.
* Reverse research and study of the architecture of the code based on the source code only.

```
Stage goals:
* Building an independent view of the project’s architecture.
* Finding logical flaws.
```

### 2. Checking the code against the checklist of known vulnerabilities.

* Manual code check for vulnerabilities from Sergey Boogerwooger's checklist.

```
Stage goal: 
Eliminate typical vulnerabilities (e.g. reentrancy, gas limit, flashloan attacks etc.)
```

### 3. Checking the logic, architecture of the security model for compliance with the desired model.

* Examining comments in code
* Comparison of the desired model obtained during the study with the reversed view obtained during the blind audit

```
Stage goal: 
Detection of inconsistencies with the desired model
```

### 4. Preparation of the final audit report and delivery to Karolina.

## Findings Severity breakdown

### Classification of Issues

* CRITICAL: Bugs leading to assets theft, fund access locking, or any other loss funds to be transferred to any party. 
* MAJOR: Bugs that can trigger a contract failure. Further recovery is possible only by manual modification of the contract state or replacement.
* WARNINGS: Bugs that can break the intended contract logic or expose it to DoS attacks. 
* COMMENTS: Other issues and recommendations reported to/ acknowledged by the team.

## Report

### CRITICAL
#### 1. Spam with a toxic token
##### Files
* contracts/Waifu.sol
    * mint(address asset, uint amount, uint minted) 
    * mint(address asset, uint amount, uint minted, address recipient)
##### Description
User can mint Waifu with own tokens as many times as he wants that can spoil the liquidity of the token. This is possible because he can create pools with own token on sushiSwap and IB.

##### Recommendation
Use checklist of trusted tokens.

#### 2. Attack with flashLoan
##### Files
 * contracts/Waifu.sol
 * contracts/Oracle.sol
##### Description

Despite using TWAP, we can rock the asset price using flashLoader of sushiSwap. It is possible if you rock the price for 10-15 blocks. 

##### Recommendation
Use another oracles, for instance, chainlink

### COMMENTS

### 1. Not optimal using transfer

##### Files
* contracts/Waifu.sol
    * liquidate(address owner, address asset, uint max) 
    * _mintDai(uint amount, address recipient)
    * _burnDai(uint amount, address recipient)
    * _burn(address asset, uint amount, uint burned, address recipient)
    * _mint(address asset, uint amount, uint minted, address recipient)

##### Description

Calling ERC20.transfer() without handling the returned value is unsafe. Moreover, contract that uses transfer() is taking a hard dependency on gas costs by forwarding a fixed amount of gas: 2300.

##### Recommendation
Use call with a require statement.

### 2. Check the amount value

##### Files
* contracts/Waifu.sol
    * _mint(address asset, uint amount, uint minted, address recipient)
    * _mintDai(uint amount, address recipient)
    
##### Description
In the mint() functions does not require that
the amount argument passed in is larger than 0 while
still emitting a Mint event.

##### Recommendation
Use a require statement.

### 3. Prefix increments are cheaper than postfix increments

##### Files
* contracts/Waifu.sol
* contracts/Oracle.sol

##### Recommendation
Use prefix increments to save some gas.



## Results

### Findings list

Level | Amount
--- | ---
CRITICAL | 2
MAJOR | 0
WARNING | 0
COMMENT | 3



