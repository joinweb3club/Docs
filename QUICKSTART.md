# Quick Start Guide

Get started with Web3.club integration in 5 minutes.

## Installation

```bash
npm install ethers
```

## Basic Setup

```javascript
const { ethers } = require('ethers');

// 1. Connect to Base network
const provider = new ethers.providers.JsonRpcProvider('https://mainnet.base.org');

// 2. Contract addresses on Base Mainnet
const CONTRACTS = {
    clubManager: '0xd4BAB0d82948955B09760F26F5EDd5E19F2Bee55',
    query: '0x2A152405afB201258D66919570BbD4625455a65f',
    tempMembership: '0xFb5567Aaf6057bbf5D729d35f56C5688B36cC940',
    passFactory: '0x27d7E9855250d6Bc84850377E533219FB8794b92',
    tokenAccess: '0xd286EE3fD7115aB0bf1dC99f8Ce8685EAE200372'
};

// 3. Load ABIs (see CONTRACT_ABIS.md)
const clubManagerABI = require('./abis/ClubManager.json');
const queryABI = require('./abis/ClubMembershipQuery.json');

// 4. Create contract instances
const clubManager = new ethers.Contract(
    CONTRACTS.clubManager,
    clubManagerABI,
    provider
);

const queryContract = new ethers.Contract(
    CONTRACTS.query,
    queryABI,
    provider
);
```

## Common Operations

### Check if User is a Member

```javascript
async function isMember(clubDomain, userAddress) {
    return await queryContract.hasActiveMembership(clubDomain, userAddress);
}

// Example
const result = await isMember('myclub', '0x742d35Cc6634C0532925a3b844Bc454e4438f44e');
console.log(`Is member: ${result}`);
```

### Get Club Information

```javascript
async function getClubInfo(clubDomain) {
    const details = await clubManager.getClubDetails(clubDomain);
    return {
        name: details.name,
        members: details.memberCount.toString(),
        admin: details.admin
    };
}

// Example
const info = await getClubInfo('myclub');
console.log(info);
```

### Purchase Membership

```javascript
async function buyMembership(clubDomain, signer) {
    const tempMembership = new ethers.Contract(
        CONTRACTS.tempMembership,
        tempMembershipABI,
        signer
    );
    
    // Get price
    const price = await tempMembership.getClubPrice(clubDomain);
    
    // Purchase
    const tx = await tempMembership.purchaseMembership(clubDomain, {
        value: price
    });
    
    await tx.wait();
    console.log('Membership purchased!');
}
```

### Set Up Token-Gated Access

```javascript
async function setupTokenGate(clubDomain, tokenAddress, minAmount, signer) {
    const tokenAccess = new ethers.Contract(
        CONTRACTS.tokenAccess,
        tokenAccessABI,
        signer
    );
    
    const tx = await tokenAccess.addTokenGate(
        clubDomain,
        tokenAddress,
        ethers.utils.parseUnits(minAmount, 18)
    );
    
    await tx.wait();
    console.log('Token gate configured!');
}
```

## Next Steps

1. Read the [Full API Documentation](README.md)
2. Check out [Integration Examples](EXAMPLES.md)
3. Join our [Developer Discord](#)

## Need Help?

- 📖 [Full Documentation](README.md)
- 💬 [Discord Community](#)
- 🐛 [Report Issues](#)

