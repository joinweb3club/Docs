# Web3.club Documentation Index

Welcome to the Web3.club developer documentation. This guide will help you integrate Web3.club's decentralized membership infrastructure into your application.

## 📚 Documentation Structure

### Getting Started
1. **[README.md](README.md)** - Complete API reference and integration guide
   - Overview of Web3.club features
   - Full API documentation for all contracts
   - Detailed function signatures and parameters
   - Code examples for common operations

2. **[QUICKSTART.md](QUICKSTART.md)** - Get started in 5 minutes
   - Basic setup
   - First contract call
   - Common operations

3. **[USAGE_GUIDE.md](USAGE_GUIDE.md)** - Practical usage with real addresses
   - Using the actual deployed contracts
   - Complete working examples
   - Express.js API server example

4. **[CONTRACT_ADDRESSES.md](CONTRACT_ADDRESSES.md)** - Deployed contract addresses
   - Base Mainnet addresses
   - Network configuration
   - BaseScan links

### Integration Guides

4. **[INTEGRATION_EXAMPLES.md](INTEGRATION_EXAMPLES.md)** - Real-world integration examples
   - Content platform (OnlyFans-style)
   - DAO governance
   - NFT communities
   - Subscription services
   - Discord bot integration

5. **[ABI_EXAMPLES.md](ABI_EXAMPLES.md)** - Contract ABIs and interfaces
   - Human-readable ABIs
   - JSON ABI fragments
   - Usage examples

6. **[contracts-config.json](contracts-config.json)** - Complete contract configuration
   - All contract addresses on Base
   - ABI file references
   - Network configuration
   - Ready to use in your application

### Smart Contracts

6. **Contract Source Files** - Solidity source code
   - `ClubManager.sol` - Main club management
   - `ClubMembershipQuery.sol` - Membership queries
   - `TemporaryMembership.sol` - Time-based memberships
   - `PermanentMembership.sol` - Permanent NFT passes
   - `TokenBasedAccess.sol` - Token-gated access
   - `Web3ClubRegistry.sol` - Domain registration
   - `Web3ClubNFT.sol` - Domain NFTs
   - `Verifier.sol` - ZKP verification

## 🚀 Quick Navigation

### For Different Use Cases

**Building a Content Platform?**
→ Start with [INTEGRATION_EXAMPLES.md - Content Platform](INTEGRATION_EXAMPLES.md#content-platform)

**Creating a DAO?**
→ Start with [INTEGRATION_EXAMPLES.md - DAO Governance](INTEGRATION_EXAMPLES.md#dao-governance)

**NFT Community?**
→ Start with [INTEGRATION_EXAMPLES.md - NFT Community](INTEGRATION_EXAMPLES.md#nft-community)

**Subscription Service?**
→ Start with [INTEGRATION_EXAMPLES.md - Subscription Service](INTEGRATION_EXAMPLES.md#subscription-service)

**Discord Integration?**
→ Start with [INTEGRATION_EXAMPLES.md - Discord Bot](INTEGRATION_EXAMPLES.md#discord-bot)

### By Task

**Check if user is a member:**
```javascript
// See README.md - ClubMembershipQuery Contract
const isMember = await queryContract.hasActiveMembership(clubDomain, userAddress);
```

**Get club information:**
```javascript
// See README.md - ClubManager Contract
const details = await clubManager.getClubDetails(clubDomain);
```

**Purchase membership:**
```javascript
// See README.md - TemporaryMembership Contract
await tempMembership.purchaseMembership(clubDomain, { value: price });
```

**Set up token requirements:**
```javascript
// See README.md - TokenBasedAccess Contract
await tokenAccess.addTokenGate(clubDomain, tokenAddress, threshold);
```

## 📖 Contract Reference

### Core Contracts

| Contract | Purpose | Documentation |
|----------|---------|---------------|
| **ClubManager** | Create and manage clubs | [README.md#clubmanager](README.md#clubmanager-contract) |
| **ClubMembershipQuery** | Query membership status | [README.md#clubmembershipquery](README.md#clubmembershipquery-contract) |
| **TemporaryMembership** | Time-based memberships | [README.md#temporarymembership](README.md#temporarymembership-contract) |
| **PermanentMembership** | Permanent NFT passes | [README.md#permanentmembership](README.md#permanentmembership-clubpassfactory-contract) |
| **TokenBasedAccess** | Token-gated access | [README.md#tokenbasedaccess](README.md#tokenbasedaccess-contract) |
| **Web3ClubRegistry** | Domain registration | [README.md#web3clubregistry](README.md#web3clubregistry-contract) |
| **Web3ClubNFT** | Domain NFTs | [README.md#web3clubnft](README.md#web3clubnft-contract) |

### Key Functions by Use Case

**Membership Verification:**
- `hasActiveMembership()` - Check if user has active membership
- `checkUserMembership()` - Get detailed membership status
- `checkDetailedMembership()` - Check all membership types
- `isMember()` - Simple membership check

**Club Management:**
- `createClub()` - Create new club
- `getClubDetails()` - Get club information
- `updateClubMetadata()` - Update club details
- `getUserClubs()` - Get all clubs user belongs to

**Membership Purchase:**
- `purchaseMembership()` - Buy monthly membership
- `purchaseQuarterMembership()` - Buy 3-month membership
- `purchaseYearMembership()` - Buy annual membership
- `purchaseMembershipFor()` - Buy permanent pass

**Access Control:**
- `addTokenGate()` - Add ERC20 requirement
- `addNFTGate()` - Add NFT requirement
- `addERC1155Gate()` - Add ERC1155 requirement
- `addCrossChainTokenGate()` - Add cross-chain requirement

## 🔧 Development Tools

### Required Dependencies

```bash
npm install ethers
# or
npm install web3
```

### Testing
Use Sepolia testnet for development:
- Get test ETH: https://sepoliafaucet.com/
- Contract addresses: [CONTRACT_ADDRESSES.md#sepolia-testnet](CONTRACT_ADDRESSES.md#sepolia-testnet)

### Frameworks

Compatible with:
- ethers.js v5/v6
- web3.js
- wagmi
- viem
- Hardhat
- Foundry

## 📝 Code Examples

### Basic Membership Check

```javascript
const { ethers } = require('ethers');
const provider = new ethers.providers.JsonRpcProvider(RPC_URL);

const queryContract = new ethers.Contract(
    QUERY_ADDRESS,
    ['function hasActiveMembership(string,address) view returns (bool)'],
    provider
);

const isMember = await queryContract.hasActiveMembership('myclub', userAddress);
```

### Full Integration Example

See [INTEGRATION_EXAMPLES.md](INTEGRATION_EXAMPLES.md) for complete applications.

## 🤝 Support

### Resources
- 📖 Full Documentation: [README.md](README.md)
- 💡 Examples: [INTEGRATION_EXAMPLES.md](INTEGRATION_EXAMPLES.md)
- 🚀 Quick Start: [QUICKSTART.md](QUICKSTART.md)

### Community
- 💬 Discord: [Join our community](#)
- 🐦 Twitter: [@web3club](#)
- 📧 Email: dev@web3.club

### Development
- 🐛 Report Issues: [GitHub Issues](#)
- 💻 Contribute: [GitHub Repository](#)
- 📚 API Docs: [README.md](README.md)

## 🔄 Updates

This documentation is regularly updated. Last update: 2025-01

For the latest version, always check:
- [GitHub Repository](#)
- [Official Website](#)

---

## Next Steps

1. **First Time Here?**
   - Start with [QUICKSTART.md](QUICKSTART.md)
   - Read the [Overview](README.md#overview)
   
2. **Ready to Build?**
   - Check [CONTRACT_ADDRESSES.md](CONTRACT_ADDRESSES.md) for contract addresses
   - Review [INTEGRATION_EXAMPLES.md](INTEGRATION_EXAMPLES.md) for your use case
   - Read the [API Reference](README.md#api-reference)

3. **Need Help?**
   - Join our [Discord](#)
   - Check [GitHub Issues](#)
   - Email us at dev@web3.club

---

**Happy Building! 🚀**

