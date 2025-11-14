# Contract ABI Examples

This document provides essential ABI fragments for integrating with Web3.club contracts.

## How to Use ABIs

```javascript
const { ethers } = require('ethers');

// Option 1: Use minimal ABI (recommended for specific functions)
const minimalABI = [
    "function isMember(string domainName, address user) view returns (bool)",
    "function getClubDetails(string domainName) view returns (uint256, address, bool, uint256, string, string, string, string, string, string)"
];

const contract = new ethers.Contract(contractAddress, minimalABI, provider);

// Option 2: Use full ABI (for complete contract interaction)
const fullABI = require('./full-abis/ClubManager.json');
const contract = new ethers.Contract(contractAddress, fullABI, provider);
```

## ClubManager - Essential Functions

```json
[
    {
        "inputs": [
            {"internalType": "string", "name": "domainName", "type": "string"},
            {"internalType": "address", "name": "user", "type": "address"}
        ],
        "name": "isMember",
        "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
        "stateMutability": "view",
        "type": "function"
    },
    {
        "inputs": [
            {"internalType": "string", "name": "domainName", "type": "string"}
        ],
        "name": "getClubDetails",
        "outputs": [
            {"internalType": "uint256", "name": "domainId", "type": "uint256"},
            {"internalType": "address", "name": "admin", "type": "address"},
            {"internalType": "bool", "name": "active", "type": "bool"},
            {"internalType": "uint256", "name": "memberCount", "type": "uint256"},
            {"internalType": "string", "name": "name", "type": "string"},
            {"internalType": "string", "name": "symbol", "type": "string"},
            {"internalType": "string", "name": "description", "type": "string"},
            {"internalType": "string", "name": "logoURL", "type": "string"},
            {"internalType": "string", "name": "bannerURL", "type": "string"},
            {"internalType": "string", "name": "baseURI", "type": "string"}
        ],
        "stateMutability": "view",
        "type": "function"
    },
    {
        "inputs": [
            {"internalType": "address", "name": "user", "type": "address"}
        ],
        "name": "getUserClubs",
        "outputs": [{"internalType": "string[]", "name": "", "type": "string[]"}],
        "stateMutability": "view",
        "type": "function"
    }
]
```

## ClubMembershipQuery - Essential Functions

```json
[
    {
        "inputs": [
            {"internalType": "string", "name": "domainName", "type": "string"},
            {"internalType": "address", "name": "account", "type": "address"}
        ],
        "name": "hasActiveMembership",
        "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
        "stateMutability": "view",
        "type": "function"
    },
    {
        "inputs": [
            {"internalType": "address", "name": "user", "type": "address"},
            {"internalType": "string", "name": "domainName", "type": "string"}
        ],
        "name": "checkUserMembership",
        "outputs": [
            {
                "components": [
                    {"internalType": "bool", "name": "isMember", "type": "bool"},
                    {"internalType": "uint256", "name": "expirationDate", "type": "uint256"},
                    {"internalType": "string", "name": "membershipType", "type": "string"}
                ],
                "internalType": "struct ClubMembershipQuery.MembershipStatus",
                "name": "status",
                "type": "tuple"
            }
        ],
        "stateMutability": "view",
        "type": "function"
    },
    {
        "inputs": [
            {"internalType": "string", "name": "domainName", "type": "string"}
        ],
        "name": "getClubMembershipConditions",
        "outputs": [
            {
                "components": [
                    {"internalType": "string", "name": "clubName", "type": "string"},
                    {"internalType": "address", "name": "clubOwner", "type": "address"},
                    {"internalType": "address", "name": "permanentMembershipContract", "type": "address"},
                    {"internalType": "bool", "name": "hasPermanentMembership", "type": "bool"},
                    {"internalType": "address", "name": "tokenAddress", "type": "address"},
                    {"internalType": "uint256", "name": "requiredTokenAmount", "type": "uint256"},
                    {"internalType": "bool", "name": "isNFT", "type": "bool"},
                    {"internalType": "bool", "name": "hasTokenRequirement", "type": "bool"},
                    {"internalType": "address", "name": "temporaryMembershipContract", "type": "address"},
                    {"internalType": "uint256", "name": "membershipPrice", "type": "uint256"},
                    {"internalType": "uint256", "name": "quarterPrice", "type": "uint256"},
                    {"internalType": "uint256", "name": "yearPrice", "type": "uint256"}
                ],
                "internalType": "struct ClubMembershipQuery.ClubMembershipConditions",
                "name": "conditions",
                "type": "tuple"
            }
        ],
        "stateMutability": "view",
        "type": "function"
    }
]
```

## TemporaryMembership - Essential Functions

```json
[
    {
        "inputs": [
            {"internalType": "string", "name": "domainName", "type": "string"}
        ],
        "name": "purchaseMembership",
        "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
        "stateMutability": "payable",
        "type": "function"
    },
    {
        "inputs": [
            {"internalType": "string", "name": "domainName", "type": "string"}
        ],
        "name": "getClubPrice",
        "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
        "stateMutability": "view",
        "type": "function"
    },
    {
        "inputs": [
            {"internalType": "string", "name": "domainName", "type": "string"},
            {"internalType": "address", "name": "account", "type": "address"}
        ],
        "name": "getMembershipExpiry",
        "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
        "stateMutability": "view",
        "type": "function"
    }
]
```

## TokenBasedAccess - Essential Functions

```json
[
    {
        "inputs": [
            {"internalType": "string", "name": "domainName", "type": "string"},
            {"internalType": "address", "name": "tokenAddress", "type": "address"},
            {"internalType": "uint256", "name": "threshold", "type": "uint256"}
        ],
        "name": "addTokenGate",
        "outputs": [{"internalType": "bool", "name": "", "type": "bool"}],
        "stateMutability": "nonpayable",
        "type": "function"
    },
    {
        "inputs": [
            {"internalType": "string", "name": "domainName", "type": "string"}
        ],
        "name": "getTokenGates",
        "outputs": [
            {"internalType": "address[]", "name": "tokenAddresses", "type": "address[]"},
            {"internalType": "uint256[]", "name": "thresholds", "type": "uint256[]"},
            {"internalType": "uint256[]", "name": "tokenIds", "type": "uint256[]"},
            {"internalType": "uint8[]", "name": "tokenTypes", "type": "uint8[]"},
            {"internalType": "uint32[]", "name": "chainIds", "type": "uint32[]"},
            {"internalType": "string[]", "name": "tokenSymbols", "type": "string[]"},
            {"internalType": "string[]", "name": "crossChainAddresses", "type": "string[]"}
        ],
        "stateMutability": "view",
        "type": "function"
    }
]
```

## Human-Readable ABIs (Recommended)

For most use cases, we recommend using human-readable ABIs with ethers.js:

```javascript
// ClubManager
const clubManagerABI = [
    "function isMember(string domainName, address user) view returns (bool)",
    "function getClubDetails(string domainName) view returns (uint256 domainId, address admin, bool active, uint256 memberCount, string name, string symbol, string description, string logoURL, string bannerURL, string baseURI)",
    "function getUserClubs(address user) view returns (string[])",
    "function createClub(string domainName, string name, string symbol, string description, string logoURL, string bannerURL, string baseURI) returns (bool)",
    "event ClubCreated(string indexed domainName, address indexed admin, uint256 domainId, string name, string symbol, string description)",
    "event MembershipChanged(address indexed user, string indexed domainName, bool status)"
];

// ClubMembershipQuery
const queryABI = [
    "function hasActiveMembership(string domainName, address account) view returns (bool)",
    "function checkUserMembership(address user, string domainName) view returns (tuple(bool isMember, uint256 expirationDate, string membershipType))",
    "function getUserMemberships(address user) view returns (string[], tuple(bool isMember, uint256 expirationDate, string membershipType)[])",
    "function checkDetailedMembership(address user, string domainName) view returns (bool isPermanent, bool isTemporary, bool isTokenBased, bool isCrossChain)"
];

// TemporaryMembership
const tempMembershipABI = [
    "function purchaseMembership(string domainName) payable returns (uint256)",
    "function purchaseQuarterMembership(string domainName) payable returns (uint256)",
    "function purchaseYearMembership(string domainName) payable returns (uint256)",
    "function getClubPrice(string domainName) view returns (uint256)",
    "function getMembershipExpiry(string domainName, address account) view returns (uint256)",
    "event Membership(string indexed domainName, address indexed member, uint256 tokenId, uint256 price, uint256 expiryTime, string action)"
];

// ClubPassFactory
const passFactoryABI = [
    "function purchaseMembershipFor(string domainName) payable returns (uint256)",
    "function getClubPrice(string domainName) view returns (uint256)",
    "function queryClubMembership(string domainName, address account) view returns (bool isMember, bool isActive, uint256 tokenId, uint256 memberCount)"
];

// TokenBasedAccess
const tokenAccessABI = [
    "function addTokenGate(string domainName, address tokenAddress, uint256 threshold) returns (bool)",
    "function addNFTGate(string domainName, address nftContract, uint256 requiredAmount) returns (bool)",
    "function hasActiveMembership(string domainName, address account) view returns (bool)",
    "function getTokenGates(string domainName) view returns (address[], uint256[], uint256[], uint8[], uint32[], string[], string[])"
];

// Web3ClubResolver
const resolverABI = [
    "function setRecord(string domainName, uint8 recordType, string value) returns ()",
    "function setBatchRecords(string domainName, uint8[] recordTypes, string[] values) returns ()",
    "function removeRecord(string domainName, uint8 recordType) returns ()",
    "function getRecord(string domainName, uint8 recordType) view returns (string)",
    "function getRecordDetails(string domainName, uint8 recordType) view returns (uint8 recordType, string value, uint256 lastUpdated)",
    "function getAllRecordTypes(string domainName) view returns (uint8[])",
    "function setNFTContract(address _nftContract) returns ()",
    "function setRegistryContract(address _registryContract) returns ()",
    "event RecordUpdated(string indexed domain, uint8 recordType, string value)",
    "event RecordRemoved(string indexed domain, uint8 recordType)"
];
```

## Full ABI Files

For complete ABI files, please refer to:
- `/abis/ClubManager.json`
- `/abis/ClubMembershipQuery.json`
- `/abis/TemporaryMembership.json`
- `/abis/ClubPassFactory.json`
- `/abis/TokenBasedAccess.json`
- `/abis/Web3ClubResolver.json`

Or download from [GitHub Releases](https://github.com/web3club/contracts/releases)

## Usage Example

```javascript
const { ethers } = require('ethers');

// Using human-readable ABI
const queryABI = [
    "function hasActiveMembership(string domainName, address account) view returns (bool)"
];

const provider = new ethers.providers.JsonRpcProvider(RPC_URL);
const contract = new ethers.Contract(QUERY_ADDRESS, queryABI, provider);

// Make call
const isMember = await contract.hasActiveMembership('myclub', userAddress);
console.log(`Is member: ${isMember}`);
```

## Web3ClubResolver Usage Example

```javascript
const { ethers } = require('ethers');

// Web3ClubResolver ABI
const resolverABI = [
    "function setRecord(string domainName, uint8 recordType, string value) returns ()",
    "function getRecord(string domainName, uint8 recordType) view returns (string)"
];

// Record types: A=0, AAAA=1, CNAME=2, TXT=3, MX=4, URL=5
const RecordType = {
    A: 0,      // IPv4 address
    AAAA: 1,   // IPv6 address
    CNAME: 2,  // Canonical name
    TXT: 3,    // Text record
    MX: 4,     // Mail exchange
    URL: 5     // Redirect URL
};

const provider = new ethers.providers.JsonRpcProvider(RPC_URL);
const signer = new ethers.Wallet(PRIVATE_KEY, provider);
const resolver = new ethers.Contract(RESOLVER_ADDRESS, resolverABI, signer);

// Set a URL record for a domain
await resolver.setRecord('myclub', RecordType.URL, 'https://myclub.com');

// Get the URL record
const url = await resolver.getRecord('myclub', RecordType.URL);
console.log(`Domain URL: ${url}`);
```

