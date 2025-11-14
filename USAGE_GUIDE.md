# Web3.club Usage Guide

This guide shows you how to use the deployed contracts with actual addresses and ABIs.

## Setup

### 1. Install Dependencies

```bash
npm install ethers
```

### 2. Load Contract Configuration

You can use the `contracts-config.json` file to automatically load all contract addresses and ABIs:

```javascript
const { ethers } = require('ethers');
const config = require('./contracts-config.json');

// Connect to Base network
const provider = new ethers.providers.JsonRpcProvider(config.rpcUrl);

// Load a contract
function getContract(contractName, signerOrProvider) {
    const contractConfig = config.contracts[contractName];
    const abiFile = require(contractConfig.abi);
    
    return new ethers.Contract(
        contractConfig.address,
        abiFile.abi,
        signerOrProvider
    );
}

// Usage
const clubManager = getContract('ClubManager', provider);
const queryContract = getContract('ClubMembershipQuery', provider);
```

### 3. Alternative: Manual Setup

```javascript
const { ethers } = require('ethers');

// Connect to Base Mainnet
const provider = new ethers.providers.JsonRpcProvider('https://mainnet.base.org');

// Load contracts manually
const ClubManagerABI = require('./Base/0xd4BAB0d82948955B09760F26F5EDd5E19F2Bee55.json');
const QueryABI = require('./Base/0x2A152405afB201258D66919570BbD4625455a65f.json');

const clubManager = new ethers.Contract(
    '0xd4BAB0d82948955B09760F26F5EDd5E19F2Bee55',
    ClubManagerABI.abi,
    provider
);

const queryContract = new ethers.Contract(
    '0x2A152405afB201258D66919570BbD4625455a65f',
    QueryABI.abi,
    provider
);
```

## Common Operations

### Check if User is a Club Member

```javascript
async function checkMembership(clubDomain, userAddress) {
    const queryContract = getContract('ClubMembershipQuery', provider);
    
    const isMember = await queryContract.hasActiveMembership(
        clubDomain,
        userAddress
    );
    
    console.log(`User ${userAddress} is ${isMember ? '' : 'NOT '}a member of ${clubDomain}`);
    return isMember;
}

// Example
await checkMembership('myclub', '0x742d35Cc6634C0532925a3b844Bc454e4438f44e');
```

### Get Club Details

```javascript
async function getClubInfo(clubDomain) {
    const clubManager = getContract('ClubManager', provider);
    
    const details = await clubManager.getClubDetails(clubDomain);
    
    return {
        domainId: details.domainId.toString(),
        admin: details.admin,
        active: details.active,
        memberCount: details.memberCount.toString(),
        name: details.name,
        symbol: details.symbol,
        description: details.description,
        logoURL: details.logoURL,
        bannerURL: details.bannerURL,
        baseURI: details.baseURI
    };
}

// Example
const clubInfo = await getClubInfo('myclub');
console.log(clubInfo);
```

### Get User's Clubs

```javascript
async function getUserClubs(userAddress) {
    const clubManager = getContract('ClubManager', provider);
    
    const clubs = await clubManager.getUserClubs(userAddress);
    
    console.log(`User is member of ${clubs.length} clubs:`, clubs);
    return clubs;
}

// Example
await getUserClubs('0x742d35Cc6634C0532925a3b844Bc454e4438f44e');
```

### Get Membership Pricing

```javascript
async function getClubPricing(clubDomain) {
    const queryContract = getContract('ClubMembershipQuery', provider);
    
    const conditions = await queryContract.getClubMembershipConditions(clubDomain);
    
    return {
        monthly: ethers.utils.formatEther(conditions.membershipPrice),
        quarterly: ethers.utils.formatEther(conditions.quarterPrice),
        yearly: ethers.utils.formatEther(conditions.yearPrice)
    };
}

// Example
const pricing = await getClubPricing('myclub');
console.log('Pricing:', pricing);
// Output: { monthly: '0.01', quarterly: '0.025', yearly: '0.08' }
```

### Purchase Monthly Membership

```javascript
async function purchaseMembership(clubDomain, signer) {
    const tempMembership = getContract('TemporaryMembership', signer);
    
    // Get price
    const price = await tempMembership.getClubPrice(clubDomain);
    
    console.log(`Price: ${ethers.utils.formatEther(price)} ETH`);
    
    // Purchase
    const tx = await tempMembership.purchaseMembership(clubDomain, {
        value: price
    });
    
    console.log('Transaction sent:', tx.hash);
    const receipt = await tx.wait();
    console.log('Membership purchased! Block:', receipt.blockNumber);
    
    return receipt;
}

// Example (requires signer with funds)
const signer = provider.getSigner();
await purchaseMembership('myclub', signer);
```

### Check Detailed Membership Status

```javascript
async function getDetailedMembership(clubDomain, userAddress) {
    const queryContract = getContract('ClubMembershipQuery', provider);
    
    const [isPermanent, isTemporary, isTokenBased, isCrossChain] = 
        await queryContract.checkDetailedMembership(userAddress, clubDomain);
    
    return {
        permanent: isPermanent,
        temporary: isTemporary,
        tokenBased: isTokenBased,
        crossChain: isCrossChain
    };
}

// Example
const status = await getDetailedMembership(
    'myclub',
    '0x742d35Cc6634C0532925a3b844Bc454e4438f44e'
);
console.log('Membership types:', status);
```

### Set Token Gate (Admin Only)

```javascript
async function setupTokenGate(clubDomain, tokenAddress, minAmount, signer) {
    const tokenAccess = getContract('TokenBasedAccess', signer);
    
    const tx = await tokenAccess.addTokenGate(
        clubDomain,
        tokenAddress,
        ethers.utils.parseEther(minAmount.toString())
    );
    
    await tx.wait();
    console.log('Token gate configured!');
}

// Example (requires admin signer)
await setupTokenGate(
    'myclub',
    '0xYourTokenAddress',
    '100', // Require 100 tokens
    adminSigner
);
```

## Complete Example Application

Here's a complete Express.js API server example:

```javascript
const express = require('express');
const { ethers } = require('ethers');
const config = require('./contracts-config.json');

const app = express();
const provider = new ethers.providers.JsonRpcProvider(config.rpcUrl);

// Helper function to load contracts
function getContract(name) {
    const contractConfig = config.contracts[name];
    const abiFile = require(contractConfig.abi);
    return new ethers.Contract(
        contractConfig.address,
        abiFile.abi,
        provider
    );
}

// API: Check membership
app.get('/api/membership/:club/:address', async (req, res) => {
    try {
        const { club, address } = req.params;
        const queryContract = getContract('ClubMembershipQuery');
        
        const isMember = await queryContract.hasActiveMembership(club, address);
        
        res.json({
            club,
            address,
            isMember,
            network: 'Base Mainnet'
        });
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// API: Get club details
app.get('/api/club/:domain', async (req, res) => {
    try {
        const { domain } = req.params;
        const clubManager = getContract('ClubManager');
        
        const details = await clubManager.getClubDetails(domain);
        
        res.json({
            domain,
            name: details.name,
            description: details.description,
            memberCount: details.memberCount.toString(),
            admin: details.admin,
            active: details.active,
            logo: details.logoURL,
            banner: details.bannerURL
        });
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// API: Get pricing
app.get('/api/pricing/:club', async (req, res) => {
    try {
        const { club } = req.params;
        const queryContract = getContract('ClubMembershipQuery');
        
        const conditions = await queryContract.getClubMembershipConditions(club);
        
        res.json({
            club,
            pricing: {
                monthly: ethers.utils.formatEther(conditions.membershipPrice) + ' ETH',
                quarterly: ethers.utils.formatEther(conditions.quarterPrice) + ' ETH',
                yearly: ethers.utils.formatEther(conditions.yearPrice) + ' ETH'
            }
        });
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// API: Get user's clubs
app.get('/api/user/:address/clubs', async (req, res) => {
    try {
        const { address } = req.params;
        const clubManager = getContract('ClubManager');
        
        const clubs = await clubManager.getUserClubs(address);
        
        res.json({
            address,
            clubs,
            count: clubs.length
        });
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Web3.club API server running on port ${PORT}`);
    console.log(`Network: ${config.network} (Chain ID: ${config.chainId})`);
});
```

## Testing

Run the example server:

```bash
node server.js
```

Test endpoints:

```bash
# Check membership
curl http://localhost:3000/api/membership/myclub/0x742d35Cc6634C0532925a3b844Bc454e4438f44e

# Get club details
curl http://localhost:3000/api/club/myclub

# Get pricing
curl http://localhost:3000/api/pricing/myclub

# Get user's clubs
curl http://localhost:3000/api/user/0x742d35Cc6634C0532925a3b844Bc454e4438f44e/clubs
```

## Next Steps

- Read the [Full API Reference](README.md)
- Check [Integration Examples](INTEGRATION_EXAMPLES.md)
- Join our [Discord Community](#)

## Support

- 📖 Documentation: [README.md](README.md)
- 💬 Discord: [Join us](#)
- 🐛 Issues: [GitHub](https://github.com/web3club/contracts/issues)

