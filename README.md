# Web3.club Developer Documentation

> **Complete Integration Guide for Building Web3 Applications with Club Membership**

Web3.club provides a decentralized club membership infrastructure that allows developers to integrate community features into their applications. Whether you're building a content platform like OnlyFans, a DAO tool, or any membership-based application, this documentation will guide you through integrating with our smart contracts.

---

## Table of Contents

1. [Overview](#overview)
2. [Quick Start](#quick-start)
3. [Contract Addresses](#contract-addresses)
4. [Core Contracts](#core-contracts)
5. [Common Use Cases](#common-use-cases)
6. [API Reference](#api-reference)
7. [Code Examples](#code-examples)
8. [Error Handling](#error-handling)

---

## Overview

Web3.club is a decentralized infrastructure for creating and managing membership-based communities on blockchain. Our smart contracts provide:

- **Domain Registration**: Register `.web3.club` domains as NFTs
- **Club Management**: Create and manage clubs tied to domains
- **Flexible Membership**: Support for permanent, temporary, and token-gated membership
- **Cross-chain Verification**: Verify token holdings across different chains
- **ZKP Authentication**: Privacy-preserving membership verification

### Key Features for Developers

- ✅ **Easy Integration**: Simple contract calls to check membership status
- ✅ **Multiple Membership Types**: Permanent NFT passes, time-based memberships, token-gated access
- ✅ **Decentralized**: No central authority, all logic on-chain
- ✅ **Composable**: Use with existing ERC20/ERC721/ERC1155 tokens
- ✅ **Privacy-First**: Optional zero-knowledge proofs for membership verification

---

## Quick Start

### Prerequisites

```bash
npm install ethers
# or
npm install web3
```

### Basic Setup

```javascript
const { ethers } = require('ethers');

// Connect to Base network
const provider = new ethers.providers.JsonRpcProvider('https://mainnet.base.org');

// Contract addresses on Base Mainnet
const CLUB_MANAGER_ADDRESS = "0xd4BAB0d82948955B09760F26F5EDd5E19F2Bee55";
const QUERY_CONTRACT_ADDRESS = "0x2A152405afB201258D66919570BbD4625455a65f";

// Load contract ABI and create instance
const clubManager = new ethers.Contract(
    CLUB_MANAGER_ADDRESS,
    CLUB_MANAGER_ABI,
    provider
);
```

### First Call: Check if User is Club Member

```javascript
async function checkMembership(clubDomain, userAddress) {
    const isMember = await clubManager.isMember(clubDomain, userAddress);
    console.log(`User is member: ${isMember}`);
    return isMember;
}

// Example usage
await checkMembership("myclub", "0x742d35Cc6634C0532925a3b844Bc454e4438f44e");
```

---

## Contract Addresses

All contracts are deployed on **Base Mainnet** (Chain ID: 8453)

| Contract | Address | Network |
|----------|---------|---------|
| ClubManager | `0xd4BAB0d82948955B09760F26F5EDd5E19F2Bee55` | Base Mainnet |
| ClubMembershipQuery | `0x2A152405afB201258D66919570BbD4625455a65f` | Base Mainnet |
| Web3ClubRegistry | `0xD0F9eaf5987a7382AAf01B1553051fc705639FCE` | Base Mainnet |
| Web3ClubNFT | `0xe51DaB702d2A49791488d259CebbA583437727b5` | Base Mainnet |
| ClubPassFactory | `0x27d7E9855250d6Bc84850377E533219FB8794b92` | Base Mainnet |
| TemporaryMembership | `0xFb5567Aaf6057bbf5D729d35f56C5688B36cC940` | Base Mainnet |
| TokenBasedAccess | `0xd286EE3fD7115aB0bf1dC99f8Ce8685EAE200372` | Base Mainnet |

**Network Info:**
- RPC URL: `https://mainnet.base.org`
- Chain ID: `8453`
- Explorer: https://basescan.org

For complete address list, see [CONTRACT_ADDRESSES.md](CONTRACT_ADDRESSES.md)

---

## Core Contracts

### 1. ClubManager
**Purpose**: Central hub for club creation and management

**Key Functions**:
- Create clubs
- Manage club admins
- Track club members
- Update club metadata

### 2. ClubMembershipQuery
**Purpose**: Query membership status across all membership types

**Key Functions**:
- Check if user has active membership
- Get user's membership details
- Query club membership conditions
- Get all clubs a user belongs to

### 3. TemporaryMembership
**Purpose**: Time-based membership (monthly, quarterly, yearly)

**Key Functions**:
- Purchase time-limited memberships
- Check membership expiration
- Set membership pricing

### 4. PermanentMembership (ClubPassFactory)
**Purpose**: Permanent NFT-based membership passes

**Key Functions**:
- Purchase permanent membership
- Manage club pass contracts
- Set pricing and supply limits

### 5. TokenBasedAccess
**Purpose**: Token-gated membership (ERC20/721/1155)

**Key Functions**:
- Set token requirements
- Check token balances
- Support cross-chain tokens

---

## Common Use Cases

### Use Case 1: Content Platform (like OnlyFans)

**Scenario**: You're building a content subscription platform where creators can manage their subscribers.

**Implementation**:
1. Creator registers a club domain
2. Creator sets membership price (permanent or temporary)
3. Users purchase membership to access content
4. Your app checks membership before showing content

```javascript
// Check if user can access creator's content
async function canAccessContent(creatorClub, userAddress) {
    const query = new ethers.Contract(QUERY_ADDRESS, QUERY_ABI, provider);
    const hasAccess = await query.hasActiveMembership(creatorClub, userAddress);
    return hasAccess;
}
```

### Use Case 2: DAO Community

**Scenario**: Create a DAO where only token holders can join.

**Implementation**:
1. Create club with domain
2. Set token-gated access (e.g., "must hold 100 DAO tokens")
3. Users automatically get membership if they hold required tokens

```javascript
// Add token requirement
const tokenAccess = new ethers.Contract(TOKEN_ACCESS_ADDRESS, ABI, signer);
await tokenAccess.addTokenGate(
    "mydao",                    // club domain
    "0xTOKEN_ADDRESS",          // your DAO token
    ethers.utils.parseEther("100") // required amount
);
```

### Use Case 3: NFT Community

**Scenario**: Create exclusive access for NFT holders.

**Implementation**:
1. Create club domain
2. Set NFT requirement
3. NFT holders automatically have membership

```javascript
// Add NFT gate requirement
await tokenAccess.addNFTGate(
    "nftclub",           // club domain
    "0xNFT_ADDRESS",     // NFT collection address
    1                    // minimum NFTs required
);
```

---

## API Reference

### ClubManager Contract

#### `createClub`
Create a new club tied to a domain.

**Function Signature**:
```solidity
function createClub(
    string memory domainName,
    string memory name,
    string memory symbol,
    string memory description,
    string memory logoURL,
    string memory bannerURL,
    string memory baseURI
) external returns (bool)
```

**Parameters**:
- `domainName` (string): Club domain name (e.g., "myclub")
- `name` (string): Club display name
- `symbol` (string): Club symbol (short name)
- `description` (string): Club description
- `logoURL` (string): URL to club logo
- `bannerURL` (string): URL to club banner
- `baseURI` (string): Base URI for metadata

**Returns**: `bool` - Success status

**Example**:
```javascript
const tx = await clubManager.createClub(
    "myclub",
    "My Awesome Club",
    "MAC",
    "A club for awesome people",
    "https://example.com/logo.png",
    "https://example.com/banner.png",
    "https://metadata.example.com/"
);
await tx.wait();
```

---

#### `isMember`
Check if an address is a member of a club.

**Function Signature**:
```solidity
function isMember(string memory domainName, address user) public view returns (bool)
```

**Parameters**:
- `domainName` (string): Club domain name
- `user` (address): User address to check

**Returns**: `bool` - Whether user is a member

**Example**:
```javascript
const isMember = await clubManager.isMember(
    "myclub",
    "0x742d35Cc6634C0532925a3b844Bc454e4438f44e"
);
console.log(`Is member: ${isMember}`);
```

---

#### `getClubDetails`
Get detailed information about a club.

**Function Signature**:
```solidity
function getClubDetails(string memory domainName) public view returns (
    uint256 domainId,
    address admin,
    bool active,
    uint256 memberCount,
    string memory name,
    string memory symbol,
    string memory description,
    string memory logoURL,
    string memory bannerURL,
    string memory baseURI
)
```

**Parameters**:
- `domainName` (string): Club domain name

**Returns**: Tuple containing:
- `domainId` (uint256): NFT token ID of the domain
- `admin` (address): Club administrator address
- `active` (bool): Whether club is active
- `memberCount` (uint256): Number of members
- `name` (string): Club name
- `symbol` (string): Club symbol
- `description` (string): Club description
- `logoURL` (string): Logo URL
- `bannerURL` (string): Banner URL
- `baseURI` (string): Metadata base URI

**Example**:
```javascript
const details = await clubManager.getClubDetails("myclub");
console.log(`Club: ${details.name}`);
console.log(`Members: ${details.memberCount}`);
console.log(`Admin: ${details.admin}`);
```

---

#### `getUserClubs`
Get all clubs a user is a member of.

**Function Signature**:
```solidity
function getUserClubs(address user) public view returns (string[] memory)
```

**Parameters**:
- `user` (address): User address

**Returns**: `string[]` - Array of club domain names

**Example**:
```javascript
const clubs = await clubManager.getUserClubs("0x742d35Cc6634C0532925a3b844Bc454e4438f44e");
console.log(`User is member of ${clubs.length} clubs:`, clubs);
// Output: ["club1", "club2", "club3"]
```

---

#### `updateClubMetadata`
Update club metadata (admin only).

**Function Signature**:
```solidity
function updateClubMetadata(
    string memory domainName,
    string memory name,
    string memory symbol,
    string memory description,
    string memory logoURL,
    string memory bannerURL,
    string memory baseURI
) external
```

**Parameters**: Same as `createClub`

**Requires**: Caller must be club admin

**Example**:
```javascript
const signer = provider.getSigner();
const clubManagerWithSigner = clubManager.connect(signer);

await clubManagerWithSigner.updateClubMetadata(
    "myclub",
    "Updated Club Name",
    "UCN",
    "New description",
    "https://new-logo.png",
    "https://new-banner.png",
    "https://new-metadata-uri/"
);
```

---

### ClubMembershipQuery Contract

#### `checkUserMembership`
Get detailed membership status for a user.

**Function Signature**:
```solidity
function checkUserMembership(address user, string memory domainName) public view returns (
    MembershipStatus memory status
)

struct MembershipStatus {
    bool isMember;
    uint256 expirationDate;
    string membershipType;
}
```

**Parameters**:
- `user` (address): User address
- `domainName` (string): Club domain name

**Returns**: `MembershipStatus` struct containing:
- `isMember` (bool): Whether user is currently a member
- `expirationDate` (uint256): Unix timestamp of expiration (0 for permanent/token-based)
- `membershipType` (string): Type of membership ("permanent", "temporary", "token-based", "cross-chain")

**Example**:
```javascript
const queryContract = new ethers.Contract(QUERY_ADDRESS, QUERY_ABI, provider);
const status = await queryContract.checkUserMembership(
    "0x742d35Cc6634C0532925a3b844Bc454e4438f44e",
    "myclub"
);

console.log(`Is Member: ${status.isMember}`);
console.log(`Type: ${status.membershipType}`);
if (status.expirationDate > 0) {
    const expDate = new Date(status.expirationDate * 1000);
    console.log(`Expires: ${expDate.toLocaleDateString()}`);
}
```

---

#### `getClubMembershipConditions`
Get all membership requirements and pricing for a club.

**Function Signature**:
```solidity
function getClubMembershipConditions(string memory domainName) public view returns (
    ClubMembershipConditions memory conditions
)
```

**Returns**: `ClubMembershipConditions` struct containing:
- `clubName` (string): Club name
- `clubOwner` (address): Club owner address
- `permanentMembershipContract` (address): Contract for permanent membership
- `temporaryMembershipContract` (address): Contract for temporary membership
- `membershipPrice` (uint256): Monthly membership price in wei
- `quarterPrice` (uint256): 3-month membership price
- `yearPrice` (uint256): Annual membership price
- `tokenRequirements` (TokenRequirement[]): Array of token requirements

**Example**:
```javascript
const conditions = await queryContract.getClubMembershipConditions("myclub");

console.log(`Monthly Price: ${ethers.utils.formatEther(conditions.membershipPrice)} ETH`);
console.log(`Quarterly Price: ${ethers.utils.formatEther(conditions.quarterPrice)} ETH`);
console.log(`Yearly Price: ${ethers.utils.formatEther(conditions.yearPrice)} ETH`);

// Check token requirements
conditions.tokenRequirements.forEach((req, index) => {
    console.log(`Requirement ${index + 1}:`);
    console.log(`  Token: ${req.tokenAddress}`);
    console.log(`  Amount: ${req.requiredAmount}`);
    console.log(`  Type: ${req.tokenType}`); // 0=ERC20, 1=ERC721, 2=ERC1155, 3=CROSSCHAIN
});
```

---

#### `checkDetailedMembership`
Check all types of membership for a user.

**Function Signature**:
```solidity
function checkDetailedMembership(address user, string memory domainName) external view returns (
    bool isPermanent,
    bool isTemporary,
    bool isTokenBased,
    bool isCrossChain
)
```

**Parameters**:
- `user` (address): User address
- `domainName` (string): Club domain name

**Returns**: 4 boolean values indicating each membership type

**Example**:
```javascript
const [isPermanent, isTemporary, isTokenBased, isCrossChain] = 
    await queryContract.checkDetailedMembership(userAddress, "myclub");

if (isPermanent) console.log("User has permanent pass");
if (isTemporary) console.log("User has temporary subscription");
if (isTokenBased) console.log("User has token-gated access");
if (isCrossChain) console.log("User verified via cross-chain");
```

---

#### `getUserMemberships`
Get all memberships for a user across all clubs.

**Function Signature**:
```solidity
function getUserMemberships(address user) public view returns (
    string[] memory domainNames,
    MembershipStatus[] memory statuses
)
```

**Parameters**:
- `user` (address): User address

**Returns**: 
- `domainNames` (string[]): Array of club names
- `statuses` (MembershipStatus[]): Corresponding membership statuses

**Example**:
```javascript
const [clubs, statuses] = await queryContract.getUserMemberships(userAddress);

for (let i = 0; i < clubs.length; i++) {
    console.log(`Club: ${clubs[i]}`);
    console.log(`  Active: ${statuses[i].isMember}`);
    console.log(`  Type: ${statuses[i].membershipType}`);
}
```

---

### TemporaryMembership Contract

#### `purchaseMembership`
Purchase a monthly membership.

**Function Signature**:
```solidity
function purchaseMembership(string memory domainName) external payable returns (uint256)
```

**Parameters**:
- `domainName` (string): Club domain name

**Requires**: Must send exact price in ETH (check with `getClubPrice`)

**Returns**: `uint256` - Token ID of membership

**Example**:
```javascript
const tempMembership = new ethers.Contract(TEMP_MEMBERSHIP_ADDRESS, ABI, signer);

// Get price first
const price = await tempMembership.getClubPrice("myclub");

// Purchase membership
const tx = await tempMembership.purchaseMembership("myclub", {
    value: price
});
await tx.wait();
```

---

#### `purchaseQuarterMembership`
Purchase a 3-month membership.

**Function Signature**:
```solidity
function purchaseQuarterMembership(string memory domainName) external payable returns (uint256)
```

**Example**:
```javascript
const quarterPrice = await tempMembership.getClubQuarterPrice("myclub");
await tempMembership.purchaseQuarterMembership("myclub", { value: quarterPrice });
```

---

#### `purchaseYearMembership`
Purchase a 12-month membership.

**Function Signature**:
```solidity
function purchaseYearMembership(string memory domainName) external payable returns (uint256)
```

**Example**:
```javascript
const yearPrice = await tempMembership.getClubYearPrice("myclub");
await tempMembership.purchaseYearMembership("myclub", { value: yearPrice });
```

---

#### `getClubPrice`
Get monthly membership price.

**Function Signature**:
```solidity
function getClubPrice(string memory domainName) external view returns (uint256)
```

**Returns**: `uint256` - Price in wei

---

#### `getMembershipExpiry`
Get when a user's membership expires.

**Function Signature**:
```solidity
function getMembershipExpiry(string memory domainName, address account) external view returns (uint256)
```

**Returns**: `uint256` - Unix timestamp of expiration

**Example**:
```javascript
const expiry = await tempMembership.getMembershipExpiry("myclub", userAddress);
const expiryDate = new Date(expiry * 1000);
console.log(`Membership expires: ${expiryDate.toLocaleString()}`);

const daysLeft = Math.floor((expiry * 1000 - Date.now()) / (1000 * 60 * 60 * 24));
console.log(`Days remaining: ${daysLeft}`);
```

---

#### `hasActiveMembership`
Check if user has an active (non-expired) temporary membership.

**Function Signature**:
```solidity
function hasActiveMembership(string memory domainName, address account) public view returns (bool)
```

**Returns**: `bool` - Whether membership is currently active

---

### PermanentMembership (ClubPassFactory) Contract

#### `purchaseMembershipFor`
Purchase permanent membership pass.

**Function Signature**:
```solidity
function purchaseMembershipFor(string memory domainName) external payable returns (uint256)
```

**Parameters**:
- `domainName` (string): Club domain name

**Requires**: Must send exact price (check with `getClubPrice`)

**Returns**: `uint256` - NFT token ID

**Example**:
```javascript
const passFactory = new ethers.Contract(PASS_FACTORY_ADDRESS, ABI, signer);

// Get price
const price = await passFactory.getClubPrice("myclub");

// Purchase permanent pass
const tx = await passFactory.purchaseMembershipFor("myclub", {
    value: price
});
const receipt = await tx.wait();

// Get token ID from event
const event = receipt.events.find(e => e.event === 'ProxyPurchase');
const tokenId = event.args.tokenId;
console.log(`Purchased permanent pass #${tokenId}`);
```

---

#### `queryClubMembership`
Query membership details from permanent pass contract.

**Function Signature**:
```solidity
function queryClubMembership(string memory domainName, address account) external view returns (
    bool isMember,
    bool isActive,
    uint256 tokenId,
    uint256 memberCount
)
```

**Returns**:
- `isMember` (bool): Whether user owns a pass
- `isActive` (bool): Whether pass is active
- `tokenId` (uint256): Token ID of the pass
- `memberCount` (uint256): Total members with passes

**Example**:
```javascript
const [isMember, isActive, tokenId, memberCount] = 
    await passFactory.queryClubMembership("myclub", userAddress);

if (isMember) {
    console.log(`User owns pass #${tokenId}`);
    console.log(`Total pass holders: ${memberCount}`);
}
```

---

#### `setClubPrice`
Set price for permanent membership (admin only).

**Function Signature**:
```solidity
function setClubPrice(string memory domainName, uint256 newPrice) external
```

**Requires**: Caller must be club admin

**Example**:
```javascript
const newPrice = ethers.utils.parseEther("0.5"); // 0.5 ETH
await passFactory.setClubPrice("myclub", newPrice);
```

---

#### `setClubMaxSupply`
Set maximum number of passes (admin only).

**Function Signature**:
```solidity
function setClubMaxSupply(string memory domainName, uint256 newMaxSupply) external
```

**Parameters**:
- `domainName` (string): Club domain
- `newMaxSupply` (uint256): Max supply (0 = unlimited)

---

### TokenBasedAccess Contract

#### `addTokenGate`
Add ERC20 token requirement.

**Function Signature**:
```solidity
function addTokenGate(
    string memory domainName,
    address tokenAddress,
    uint256 threshold
) external returns (bool)
```

**Parameters**:
- `domainName` (string): Club domain name
- `tokenAddress` (address): ERC20 token contract address
- `threshold` (uint256): Minimum tokens required

**Requires**: Caller must be club admin

**Example**:
```javascript
const tokenAccess = new ethers.Contract(TOKEN_ACCESS_ADDRESS, ABI, signer);

// Require users to hold at least 1000 tokens
await tokenAccess.addTokenGate(
    "myclub",
    "0xTOKEN_CONTRACT_ADDRESS",
    ethers.utils.parseUnits("1000", 18) // 1000 tokens (18 decimals)
);
```

---

#### `addNFTGate`
Add ERC721 NFT requirement.

**Function Signature**:
```solidity
function addNFTGate(
    string memory domainName,
    address nftContract,
    uint256 requiredAmount
) external returns (bool)
```

**Parameters**:
- `domainName` (string): Club domain name
- `nftContract` (address): NFT collection address
- `requiredAmount` (uint256): Minimum NFTs required

**Example**:
```javascript
// Require users to own at least 1 NFT from collection
await tokenAccess.addNFTGate(
    "myclub",
    "0xNFT_CONTRACT_ADDRESS",
    1
);
```

---

#### `addERC1155Gate`
Add ERC1155 token requirement.

**Function Signature**:
```solidity
function addERC1155Gate(
    string memory domainName,
    address nftContract,
    uint256 tokenId,
    uint256 requiredAmount
) external returns (bool)
```

**Parameters**:
- `tokenId` (uint256): Specific token ID required
- `requiredAmount` (uint256): Minimum amount of that token ID

**Example**:
```javascript
// Require users to own at least 5 of token ID #42
await tokenAccess.addERC1155Gate(
    "myclub",
    "0xERC1155_CONTRACT",
    42,  // token ID
    5    // minimum amount
);
```

---

#### `addCrossChainTokenGate`
Add cross-chain token requirement.

**Function Signature**:
```solidity
function addCrossChainTokenGate(
    string memory domainName,
    uint32 chainId,
    string memory tokenAddress,
    string memory tokenSymbol,
    uint256 tokenId,
    uint256 threshold
) external
```

**Parameters**:
- `chainId` (uint32): Chain ID (e.g., 1 for Ethereum, 137 for Polygon)
- `tokenAddress` (string): Token address on that chain
- `tokenSymbol` (string): Token symbol for display
- `tokenId` (uint256): Token ID (for ERC1155, 0 for ERC20/721)
- `threshold` (uint256): Required amount

**Example**:
```javascript
// Require users to hold tokens on Polygon
await tokenAccess.addCrossChainTokenGate(
    "myclub",
    137, // Polygon chain ID
    "0xTOKEN_ON_POLYGON",
    "MATIC",
    0,
    ethers.utils.parseEther("100")
);
```

---

#### `getTokenGates`
Get all token requirements for a club.

**Function Signature**:
```solidity
function getTokenGates(string memory domainName) external view returns (
    address[] memory tokenAddresses,
    uint256[] memory thresholds,
    uint256[] memory tokenIds,
    uint8[] memory tokenTypes,
    uint32[] memory chainIds,
    string[] memory tokenSymbols,
    string[] memory crossChainAddresses
)
```

**Returns**: Arrays of token gate details

**Example**:
```javascript
const gates = await tokenAccess.getTokenGates("myclub");

for (let i = 0; i < gates.tokenAddresses.length; i++) {
    console.log(`Gate ${i + 1}:`);
    console.log(`  Address: ${gates.tokenAddresses[i]}`);
    console.log(`  Threshold: ${gates.thresholds[i]}`);
    console.log(`  Type: ${gates.tokenTypes[i]}`); // 0=ERC20, 1=ERC721, 2=ERC1155, 3=CROSSCHAIN
    console.log(`  Chain: ${gates.chainIds[i]}`);
}
```

---

#### `hasActiveMembership`
Check if user meets any token requirement.

**Function Signature**:
```solidity
function hasActiveMembership(string memory domainName, address account) public view returns (bool)
```

**Returns**: `bool` - Whether user meets at least one token requirement

**Note**: This checks real-time token balances on-chain.

---

### Web3ClubRegistry Contract

#### `getDomainStatus`
Get current status of a domain.

**Function Signature**:
```solidity
function getDomainStatus(string memory name) public view returns (DomainStatus)

enum DomainStatus {
    Available,  // 0
    Active,     // 1
    Frozen,     // 2 (expired but in grace period)
    Reclaimed   // 3 (fully expired)
}
```

**Returns**: `uint8` - Domain status (0-3)

**Example**:
```javascript
const registry = new ethers.Contract(REGISTRY_ADDRESS, ABI, provider);
const status = await registry.getDomainStatus("myclub");

const statusNames = ["Available", "Active", "Frozen", "Reclaimed"];
console.log(`Domain status: ${statusNames[status]}`);
```

---

#### `isValidDomainName`
Check if a domain name is valid and available.

**Function Signature**:
```solidity
function isValidDomainName(string memory name) public view returns (bool)
```

**Returns**: `bool` - Whether name is valid

**Example**:
```javascript
const isValid = await registry.isValidDomainName("my_new_club");
console.log(`Domain name valid: ${isValid}`);
```

---

### Web3ClubNFT Contract

#### `getDomainInfo`
Get domain registration information.

**Function Signature**:
```solidity
function getDomainInfo(string memory domainName) external view returns (
    DomainInfo memory
)

struct DomainInfo {
    uint256 registrationTime;
    uint256 expiryTime;
    address registrant;
}
```

**Returns**: Domain info struct

**Example**:
```javascript
const nftContract = new ethers.Contract(NFT_ADDRESS, ABI, provider);
const info = await nftContract.getDomainInfo("myclub");

console.log(`Registered: ${new Date(info.registrationTime * 1000).toLocaleDateString()}`);
console.log(`Expires: ${new Date(info.expiryTime * 1000).toLocaleDateString()}`);
console.log(`Owner: ${info.registrant}`);
```

---

#### `ownerOf`
Get current owner of a domain NFT.

**Function Signature**:
```solidity
function ownerOf(uint256 tokenId) external view returns (address)
```

**Parameters**:
- `tokenId` (uint256): Token ID (get from `getTokenId`)

**Returns**: `address` - Owner address

---

#### `getTokenId`
Get token ID for a domain name.

**Function Signature**:
```solidity
function getTokenId(string memory domainName) external view returns (uint256)
```

**Returns**: `uint256` - Token ID

---

## Code Examples

### Example 1: Building a Content Paywall

```javascript
const ethers = require('ethers');

class Web3ClubPaywall {
    constructor(provider, clubDomain) {
        this.provider = provider;
        this.clubDomain = clubDomain;
        
        // Initialize contracts
        this.queryContract = new ethers.Contract(
            QUERY_ADDRESS,
            QUERY_ABI,
            provider
        );
    }
    
    /**
     * Check if user can access premium content
     */
    async canAccessContent(userAddress) {
        try {
            const hasAccess = await this.queryContract.hasActiveMembership(
                this.clubDomain,
                userAddress
            );
            return hasAccess;
        } catch (error) {
            console.error('Error checking access:', error);
            return false;
        }
    }
    
    /**
     * Get membership details for display
     */
    async getMembershipInfo(userAddress) {
        const status = await this.queryContract.checkUserMembership(
            userAddress,
            this.clubDomain
        );
        
        return {
            isMember: status.isMember,
            type: status.membershipType,
            expiresAt: status.expirationDate > 0 
                ? new Date(status.expirationDate * 1000) 
                : null
        };
    }
    
    /**
     * Get pricing options
     */
    async getPricingOptions() {
        const conditions = await this.queryContract.getClubMembershipConditions(
            this.clubDomain
        );
        
        return {
            monthly: ethers.utils.formatEther(conditions.membershipPrice),
            quarterly: ethers.utils.formatEther(conditions.quarterPrice),
            yearly: ethers.utils.formatEther(conditions.yearPrice),
            permanent: await this.getPermanentPrice()
        };
    }
    
    async getPermanentPrice() {
        const passFactory = new ethers.Contract(
            PASS_FACTORY_ADDRESS,
            PASS_FACTORY_ABI,
            this.provider
        );
        const price = await passFactory.getClubPrice(this.clubDomain);
        return ethers.utils.formatEther(price);
    }
}

// Usage in your app
const paywall = new Web3ClubPaywall(provider, 'contentcreator');

// Middleware for Express.js
app.get('/premium-content/:id', async (req, res) => {
    const userAddress = req.user.walletAddress; // from your auth
    
    const canAccess = await paywall.canAccessContent(userAddress);
    
    if (!canAccess) {
        const pricing = await paywall.getPricingOptions();
        return res.status(403).json({
            error: 'Membership required',
            pricing: pricing
        });
    }
    
    // User has access, serve content
    res.json({ content: 'Premium content here...' });
});
```

---

### Example 2: DAO Membership Checker

```javascript
class DAOMembershipChecker {
    constructor(provider, daoClubDomain) {
        this.provider = provider;
        this.clubDomain = daoClubDomain;
        this.tokenAccess = new ethers.Contract(
            TOKEN_ACCESS_ADDRESS,
            TOKEN_ACCESS_ABI,
            provider
        );
    }
    
    /**
     * Setup DAO with token requirement
     */
    async setupTokenGate(signer, tokenAddress, minTokens) {
        const contract = this.tokenAccess.connect(signer);
        
        const tx = await contract.addTokenGate(
            this.clubDomain,
            tokenAddress,
            ethers.utils.parseUnits(minTokens.toString(), 18)
        );
        
        await tx.wait();
        console.log('Token gate configured');
    }
    
    /**
     * Check if user can vote (has required tokens)
     */
    async canVote(userAddress) {
        return await this.tokenAccess.hasActiveMembership(
            this.clubDomain,
            userAddress
        );
    }
    
    /**
     * Get all requirements
     */
    async getRequirements() {
        const gates = await this.tokenAccess.getTokenGates(this.clubDomain);
        
        const requirements = [];
        for (let i = 0; i < gates.tokenAddresses.length; i++) {
            requirements.push({
                tokenAddress: gates.tokenAddresses[i],
                minAmount: gates.thresholds[i].toString(),
                type: ['ERC20', 'ERC721', 'ERC1155', 'CROSSCHAIN'][gates.tokenTypes[i]]
            });
        }
        
        return requirements;
    }
}

// Usage
const daoChecker = new DAOMembershipChecker(provider, 'mydao');

// In your voting interface
async function submitVote(userAddress, proposalId, vote) {
    const canVote = await daoChecker.canVote(userAddress);
    
    if (!canVote) {
        const requirements = await daoChecker.getRequirements();
        throw new Error(`Insufficient tokens. Requirements: ${JSON.stringify(requirements)}`);
    }
    
    // Process vote...
}
```

---

### Example 3: Multi-Club Platform

```javascript
class MultiClubPlatform {
    constructor(provider) {
        this.provider = provider;
        this.clubManager = new ethers.Contract(
            CLUB_MANAGER_ADDRESS,
            CLUB_MANAGER_ABI,
            provider
        );
        this.queryContract = new ethers.Contract(
            QUERY_ADDRESS,
            QUERY_ABI,
            provider
        );
    }
    
    /**
     * Get all clubs user is member of
     */
    async getUserMemberships(userAddress) {
        const [clubs, statuses] = await this.queryContract.getUserMemberships(userAddress);
        
        const memberships = [];
        for (let i = 0; i < clubs.length; i++) {
            const details = await this.clubManager.getClubDetails(clubs[i]);
            
            memberships.push({
                clubDomain: clubs[i],
                clubName: details.name,
                memberCount: details.memberCount.toString(),
                admin: details.admin,
                membershipType: statuses[i].membershipType,
                isActive: statuses[i].isMember,
                expiresAt: statuses[i].expirationDate > 0 
                    ? new Date(statuses[i].expirationDate * 1000).toISOString()
                    : null
            });
        }
        
        return memberships;
    }
    
    /**
     * Get user's dashboard data
     */
    async getUserDashboard(userAddress) {
        const memberships = await this.getUserMemberships(userAddress);
        
        // Categorize by type
        const dashboard = {
            permanent: memberships.filter(m => m.membershipType === 'permanent'),
            temporary: memberships.filter(m => m.membershipType === 'temporary'),
            tokenBased: memberships.filter(m => m.membershipType === 'token-based'),
            crossChain: memberships.filter(m => m.membershipType === 'cross-chain'),
            expiringSoon: memberships.filter(m => {
                if (!m.expiresAt) return false;
                const daysUntilExpiry = (new Date(m.expiresAt) - new Date()) / (1000 * 60 * 60 * 24);
                return daysUntilExpiry > 0 && daysUntilExpiry <= 7;
            })
        };
        
        return dashboard;
    }
    
    /**
     * Explore available clubs
     */
    async exploreClub(clubDomain) {
        const details = await this.clubManager.getClubDetails(clubDomain);
        const conditions = await this.queryContract.getClubMembershipConditions(clubDomain);
        
        return {
            name: details.name,
            description: details.description,
            logo: details.logoURL,
            banner: details.bannerURL,
            memberCount: details.memberCount.toString(),
            admin: details.admin,
            pricing: {
                monthly: ethers.utils.formatEther(conditions.membershipPrice),
                quarterly: ethers.utils.formatEther(conditions.quarterPrice),
                yearly: ethers.utils.formatEther(conditions.yearPrice)
            },
            tokenRequirements: conditions.tokenRequirements
        };
    }
}

// API endpoints
const platform = new MultiClubPlatform(provider);

app.get('/api/user/:address/dashboard', async (req, res) => {
    const dashboard = await platform.getUserDashboard(req.params.address);
    res.json(dashboard);
});

app.get('/api/club/:domain', async (req, res) => {
    const clubInfo = await platform.exploreClub(req.params.domain);
    res.json(clubInfo);
});
```

---

### Example 4: Purchase Membership Flow

```javascript
async function purchaseMembershipFlow(clubDomain, membershipType, userSigner) {
    const queryContract = new ethers.Contract(QUERY_ADDRESS, QUERY_ABI, userSigner);
    
    // Step 1: Get pricing
    const conditions = await queryContract.getClubMembershipConditions(clubDomain);
    
    let price, contract, methodName;
    
    switch (membershipType) {
        case 'monthly':
            price = conditions.membershipPrice;
            contract = new ethers.Contract(TEMP_MEMBERSHIP_ADDRESS, TEMP_ABI, userSigner);
            methodName = 'purchaseMembership';
            break;
            
        case 'quarterly':
            price = conditions.quarterPrice;
            contract = new ethers.Contract(TEMP_MEMBERSHIP_ADDRESS, TEMP_ABI, userSigner);
            methodName = 'purchaseQuarterMembership';
            break;
            
        case 'yearly':
            price = conditions.yearPrice;
            contract = new ethers.Contract(TEMP_MEMBERSHIP_ADDRESS, TEMP_ABI, userSigner);
            methodName = 'purchaseYearMembership';
            break;
            
        case 'permanent':
            contract = new ethers.Contract(PASS_FACTORY_ADDRESS, PASS_ABI, userSigner);
            price = await contract.getClubPrice(clubDomain);
            methodName = 'purchaseMembershipFor';
            break;
            
        default:
            throw new Error('Invalid membership type');
    }
    
    // Step 2: Show price to user
    console.log(`Price: ${ethers.utils.formatEther(price)} ETH`);
    
    // Step 3: Execute purchase
    const tx = await contract[methodName](clubDomain, { value: price });
    
    console.log('Transaction sent:', tx.hash);
    
    // Step 4: Wait for confirmation
    const receipt = await tx.wait();
    
    console.log('Membership purchased!');
    console.log('Block:', receipt.blockNumber);
    
    // Step 5: Verify membership
    const isMember = await queryContract.hasActiveMembership(
        clubDomain,
        await userSigner.getAddress()
    );
    
    return {
        success: isMember,
        transactionHash: tx.hash,
        blockNumber: receipt.blockNumber
    };
}

// Usage
try {
    const result = await purchaseMembershipFlow('myclub', 'yearly', signer);
    console.log('Purchase successful:', result);
} catch (error) {
    console.error('Purchase failed:', error.message);
}
```

---

## Error Handling

### Common Errors

#### 1. "Club not initialized"
**Cause**: Trying to interact with a club that hasn't been created yet.

**Solution**: Check if club exists first:
```javascript
const clubManager = new ethers.Contract(...);
const isInitialized = await clubManager.isClubInitialized(clubDomain);
if (!isInitialized) {
    console.error('Club does not exist');
}
```

#### 2. "Insufficient funds"
**Cause**: Not sending enough ETH for membership purchase.

**Solution**: Always check price before purchasing:
```javascript
const price = await contract.getClubPrice(clubDomain);
// Send exact price
await contract.purchaseMembership(clubDomain, { value: price });
```

#### 3. "Not admin"
**Cause**: Trying to call admin-only function without permissions.

**Solution**: Check admin status:
```javascript
const admin = await clubManager.getClubAdmin(clubDomain);
const currentUser = await signer.getAddress();
if (admin.toLowerCase() !== currentUser.toLowerCase()) {
    console.error('You are not the club admin');
}
```

#### 4. "Already member"
**Cause**: User already owns a permanent membership pass.

**Solution**: Check membership before purchasing:
```javascript
const queryContract = new ethers.Contract(...);
const status = await queryContract.checkUserMembership(userAddress, clubDomain);
if (status.isMember && status.membershipType === 'permanent') {
    console.log('User already has permanent membership');
}
```

### Error Handling Best Practices

```javascript
async function safeContractCall(contractFunction, ...args) {
    try {
        const result = await contractFunction(...args);
        return { success: true, data: result };
    } catch (error) {
        console.error('Contract call failed:', error);
        
        // Parse error message
        let errorMessage = 'Unknown error';
        
        if (error.message.includes('user rejected')) {
            errorMessage = 'Transaction was rejected by user';
        } else if (error.message.includes('insufficient funds')) {
            errorMessage = 'Insufficient ETH balance';
        } else if (error.data) {
            // Try to decode custom error
            errorMessage = error.data.message || error.message;
        }
        
        return { success: false, error: errorMessage };
    }
}

// Usage
const result = await safeContractCall(
    clubManager.createClub,
    'myclub',
    'My Club',
    'MC',
    'Description',
    '',
    '',
    ''
);

if (result.success) {
    console.log('Club created successfully');
} else {
    console.error('Failed to create club:', result.error);
}
```

---

## Advanced Topics

### Working with Events

Listen to events for real-time updates:

```javascript
const clubManager = new ethers.Contract(CLUB_MANAGER_ADDRESS, ABI, provider);

// Listen for new club creations
clubManager.on('ClubCreated', (domainName, admin, domainId, name, symbol, description) => {
    console.log('New club created:');
    console.log(`  Domain: ${domainName}`);
    console.log(`  Name: ${name}`);
    console.log(`  Admin: ${admin}`);
});

// Listen for membership changes
clubManager.on('MembershipChanged', (user, domainName, status) => {
    console.log(`Membership change for ${user} in ${domainName}: ${status}`);
});

// Query historical events
const filter = clubManager.filters.ClubCreated(null, null);
const events = await clubManager.queryFilter(filter, -10000); // Last ~10k blocks

events.forEach(event => {
    console.log(`Club ${event.args.name} created at block ${event.blockNumber}`);
});
```

### Batch Operations

Efficiently query multiple clubs:

```javascript
async function batchGetClubDetails(clubDomains) {
    const clubManager = new ethers.Contract(CLUB_MANAGER_ADDRESS, ABI, provider);
    
    // Use Promise.all for parallel queries
    const results = await Promise.all(
        clubDomains.map(domain => 
            clubManager.getClubDetails(domain).catch(err => null)
        )
    );
    
    return clubDomains.map((domain, index) => ({
        domain,
        details: results[index]
    })).filter(item => item.details !== null);
}

// Usage
const clubs = ['club1', 'club2', 'club3'];
const details = await batchGetClubDetails(clubs);
```

---

## Testing

### Unit Testing Example (using Hardhat)

```javascript
const { expect } = require('chai');
const { ethers } = require('hardhat');

describe('Web3Club Integration', function() {
    let clubManager, queryContract, signer1, signer2;
    
    before(async function() {
        [signer1, signer2] = await ethers.getSigners();
        
        // Deploy or attach to existing contracts
        clubManager = await ethers.getContractAt(
            'ClubManager',
            CLUB_MANAGER_ADDRESS
        );
        
        queryContract = await ethers.getContractAt(
            'ClubMembershipQuery',
            QUERY_ADDRESS
        );
    });
    
    it('Should check membership correctly', async function() {
        const isMember = await queryContract.hasActiveMembership(
            'testclub',
            signer1.address
        );
        
        expect(isMember).to.be.a('boolean');
    });
    
    it('Should get club details', async function() {
        const details = await clubManager.getClubDetails('testclub');
        
        expect(details.name).to.be.a('string');
        expect(details.memberCount).to.be.a('number');
    });
});
```

---

## Support & Resources

- Email: abing@web3.club

---

## License

MIT License - See LICENSE file for details

---

**Built with ❤️ by the Web3.club Team**

