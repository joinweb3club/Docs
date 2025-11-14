# Integration Examples

Real-world examples of integrating Web3.club into your application.

## Table of Contents

1. [Content Platform (OnlyFans-style)](#content-platform)
2. [DAO Governance](#dao-governance)
3. [NFT Community](#nft-community)
4. [Subscription Service](#subscription-service)
5. [Token-Gated Discord Bot](#discord-bot)

---

## Content Platform

Build a content subscription platform where creators monetize their content.

### Architecture

```
User → Frontend → Check Membership → Show/Hide Content
                ↓
            Smart Contracts (Web3.club)
```

### Implementation

```javascript
// backend/services/membership.js
class MembershipService {
    constructor(provider) {
        this.queryContract = new ethers.Contract(
            QUERY_ADDRESS,
            QUERY_ABI,
            provider
        );
    }
    
    async canAccessContent(creatorDomain, userAddress) {
        try {
            return await this.queryContract.hasActiveMembership(
                creatorDomain,
                userAddress
            );
        } catch (error) {
            console.error('Membership check failed:', error);
            return false;
        }
    }
    
    async getMembershipTier(creatorDomain, userAddress) {
        const [isPermanent, isTemporary, isTokenBased, isCrossChain] = 
            await this.queryContract.checkDetailedMembership(
                userAddress,
                creatorDomain
            );
        
        if (isPermanent) return 'premium'; // Lifetime access
        if (isTemporary) return 'standard'; // Time-limited
        if (isTokenBased || isCrossChain) return 'token'; // Token holders
        
        return 'none';
    }
}

// Express.js middleware
const membershipService = new MembershipService(provider);

async function requireMembership(req, res, next) {
    const { creatorDomain } = req.params;
    const userAddress = req.user.walletAddress;
    
    const hasAccess = await membershipService.canAccessContent(
        creatorDomain,
        userAddress
    );
    
    if (!hasAccess) {
        return res.status(403).json({
            error: 'Membership required',
            message: `You need to subscribe to @${creatorDomain} to access this content`,
            subscribeUrl: `/subscribe/${creatorDomain}`
        });
    }
    
    next();
}

// Protected routes
app.get('/api/content/:creatorDomain/:contentId', 
    requireMembership, 
    async (req, res) => {
        // Serve premium content
        const content = await getContent(req.params.contentId);
        res.json(content);
    }
);

// Subscription page
app.get('/api/subscribe/:creatorDomain/options', async (req, res) => {
    const { creatorDomain } = req.params;
    
    const conditions = await this.queryContract.getClubMembershipConditions(
        creatorDomain
    );
    
    res.json({
        creator: creatorDomain,
        options: [
            {
                type: 'monthly',
                price: ethers.utils.formatEther(conditions.membershipPrice),
                duration: '30 days'
            },
            {
                type: 'quarterly',
                price: ethers.utils.formatEther(conditions.quarterPrice),
                duration: '90 days',
                savings: '15%'
            },
            {
                type: 'yearly',
                price: ethers.utils.formatEther(conditions.yearPrice),
                duration: '365 days',
                savings: '30%'
            },
            {
                type: 'lifetime',
                price: await getLifetimePrice(creatorDomain),
                duration: 'Forever',
                badge: 'Best Value'
            }
        ]
    });
});

async function getLifetimePrice(creatorDomain) {
    const passFactory = new ethers.Contract(
        PASS_FACTORY_ADDRESS,
        PASS_FACTORY_ABI,
        provider
    );
    const price = await passFactory.getClubPrice(creatorDomain);
    return ethers.utils.formatEther(price);
}
```

### Frontend (React)

```jsx
// components/ContentGate.jsx
import { useAccount, useContract } from 'wagmi';
import { useState, useEffect } from 'react';

function ContentGate({ creatorDomain, children }) {
    const { address } = useAccount();
    const [hasAccess, setHasAccess] = useState(false);
    const [loading, setLoading] = useState(true);
    
    const queryContract = useContract({
        address: QUERY_ADDRESS,
        abi: QUERY_ABI
    });
    
    useEffect(() => {
        async function checkAccess() {
            if (!address) {
                setHasAccess(false);
                setLoading(false);
                return;
            }
            
            try {
                const isMember = await queryContract.hasActiveMembership(
                    creatorDomain,
                    address
                );
                setHasAccess(isMember);
            } catch (error) {
                console.error('Error checking membership:', error);
                setHasAccess(false);
            } finally {
                setLoading(false);
            }
        }
        
        checkAccess();
    }, [address, creatorDomain]);
    
    if (loading) {
        return <div>Checking membership...</div>;
    }
    
    if (!hasAccess) {
        return <SubscriptionPrompt creatorDomain={creatorDomain} />;
    }
    
    return children;
}

// Usage
function PremiumPost({ creatorDomain, post }) {
    return (
        <ContentGate creatorDomain={creatorDomain}>
            <div className="premium-content">
                <h2>{post.title}</h2>
                <p>{post.content}</p>
                <img src={post.image} alt={post.title} />
            </div>
        </ContentGate>
    );
}
```

---

## DAO Governance

Create a DAO where only token holders can vote.

### Smart Contract Setup

```javascript
// scripts/setupDAO.js
async function setupDAO() {
    const [deployer] = await ethers.getSigners();
    
    // 1. Create club for DAO
    const clubManager = await ethers.getContractAt(
        'ClubManager',
        CLUB_MANAGER_ADDRESS
    );
    
    await clubManager.createClub(
        'mydao',
        'My DAO',
        'MDAO',
        'A decentralized autonomous organization',
        'https://mydao.com/logo.png',
        'https://mydao.com/banner.png',
        'https://metadata.mydao.com/'
    );
    
    // 2. Set up token gate
    const tokenAccess = await ethers.getContractAt(
        'TokenBasedAccess',
        TOKEN_ACCESS_ADDRESS
    );
    
    // Require 100 governance tokens to be a member
    await tokenAccess.addTokenGate(
        'mydao',
        GOVERNANCE_TOKEN_ADDRESS,
        ethers.utils.parseEther('100')
    );
    
    console.log('DAO setup complete!');
}
```

### Voting Integration

```javascript
// backend/services/voting.js
class VotingService {
    constructor(provider) {
        this.tokenAccess = new ethers.Contract(
            TOKEN_ACCESS_ADDRESS,
            TOKEN_ACCESS_ABI,
            provider
        );
    }
    
    async canVote(daoClub, voterAddress) {
        // Check if voter meets token requirements
        return await this.tokenAccess.hasActiveMembership(
            daoClub,
            voterAddress
        );
    }
    
    async getVotingPower(daoClub, voterAddress) {
        // Get token gates to determine voting power
        const gates = await this.tokenAccess.getTokenGates(daoClub);
        
        let totalPower = 0;
        
        for (let i = 0; i < gates.tokenAddresses.length; i++) {
            if (gates.tokenTypes[i] === 0) { // ERC20
                const token = new ethers.Contract(
                    gates.tokenAddresses[i],
                    ERC20_ABI,
                    this.provider
                );
                
                const balance = await token.balanceOf(voterAddress);
                totalPower += parseFloat(ethers.utils.formatEther(balance));
            }
        }
        
        return totalPower;
    }
}

// API endpoints
app.post('/api/proposals/:id/vote', async (req, res) => {
    const { id } = req.params;
    const { vote } = req.body; // 'yes' or 'no'
    const voterAddress = req.user.walletAddress;
    
    const votingService = new VotingService(provider);
    
    // Check voting eligibility
    const canVote = await votingService.canVote('mydao', voterAddress);
    if (!canVote) {
        return res.status(403).json({
            error: 'Not eligible to vote',
            message: 'You must hold at least 100 governance tokens'
        });
    }
    
    // Get voting power
    const power = await votingService.getVotingPower('mydao', voterAddress);
    
    // Record vote
    await recordVote(id, voterAddress, vote, power);
    
    res.json({
        success: true,
        votingPower: power
    });
});
```

---

## NFT Community

Create exclusive access for NFT holders.

### Setup

```javascript
async function setupNFTCommunity() {
    const clubManager = await ethers.getContractAt('ClubManager', CLUB_MANAGER_ADDRESS);
    const tokenAccess = await ethers.getContractAt('TokenBasedAccess', TOKEN_ACCESS_ADDRESS);
    
    // Create club
    await clubManager.createClub(
        'nftclub',
        'Cool NFT Community',
        'CNFT',
        'Exclusive club for NFT holders',
        'https://nftclub.com/logo.png',
        'https://nftclub.com/banner.png',
        'https://metadata.nftclub.com/'
    );
    
    // Require holding at least 1 NFT from the collection
    await tokenAccess.addNFTGate(
        'nftclub',
        NFT_COLLECTION_ADDRESS,
        1
    );
    
    console.log('NFT Community setup complete!');
}
```

### Discord Bot Integration

```javascript
// bot/commands/verify.js
const { SlashCommandBuilder } = require('discord.js');
const { ethers } = require('ethers');

module.exports = {
    data: new SlashCommandBuilder()
        .setName('verify')
        .setDescription('Verify your NFT ownership')
        .addStringOption(option =>
            option.setName('address')
                .setDescription('Your Ethereum address')
                .setRequired(true)
        ),
    
    async execute(interaction) {
        const address = interaction.options.getString('address');
        
        // Validate address
        if (!ethers.utils.isAddress(address)) {
            return interaction.reply({
                content: 'Invalid Ethereum address!',
                ephemeral: true
            });
        }
        
        // Check membership
        const tokenAccess = new ethers.Contract(
            TOKEN_ACCESS_ADDRESS,
            TOKEN_ACCESS_ABI,
            provider
        );
        
        const isMember = await tokenAccess.hasActiveMembership(
            'nftclub',
            address
        );
        
        if (isMember) {
            // Grant Discord role
            const role = interaction.guild.roles.cache.find(
                r => r.name === 'NFT Holder'
            );
            
            await interaction.member.roles.add(role);
            
            return interaction.reply({
                content: '✅ Verified! You now have access to NFT holder channels.',
                ephemeral: true
            });
        } else {
            return interaction.reply({
                content: '❌ You don\'t own any NFTs from the collection.',
                ephemeral: true
            });
        }
    }
};
```

---

## Subscription Service

Time-based subscription service with automatic access management.

### Backend Service

```javascript
// services/subscription-manager.js
class SubscriptionManager {
    constructor(provider) {
        this.tempMembership = new ethers.Contract(
            TEMP_MEMBERSHIP_ADDRESS,
            TEMP_MEMBERSHIP_ABI,
            provider
        );
        this.queryContract = new ethers.Contract(
            QUERY_ADDRESS,
            QUERY_ABI,
            provider
        );
    }
    
    async getSubscriptionStatus(serviceDomain, userAddress) {
        const status = await this.queryContract.checkUserMembership(
            userAddress,
            serviceDomain
        );
        
        if (!status.isMember) {
            return {
                active: false,
                message: 'No active subscription'
            };
        }
        
        if (status.expirationDate === 0) {
            return {
                active: true,
                type: 'lifetime',
                message: 'Lifetime subscription'
            };
        }
        
        const expiryDate = new Date(status.expirationDate * 1000);
        const now = new Date();
        const daysLeft = Math.ceil((expiryDate - now) / (1000 * 60 * 60 * 24));
        
        return {
            active: expiryDate > now,
            type: status.membershipType,
            expiresAt: expiryDate.toISOString(),
            daysLeft: daysLeft,
            message: daysLeft > 0 
                ? `${daysLeft} days remaining` 
                : 'Subscription expired'
        };
    }
    
    async getRenewalOptions(serviceDomain) {
        const conditions = await this.queryContract.getClubMembershipConditions(
            serviceDomain
        );
        
        return {
            monthly: {
                price: ethers.utils.formatEther(conditions.membershipPrice),
                currency: 'ETH',
                duration: 30
            },
            quarterly: {
                price: ethers.utils.formatEther(conditions.quarterPrice),
                currency: 'ETH',
                duration: 90,
                discount: this.calculateDiscount(
                    conditions.membershipPrice,
                    conditions.quarterPrice,
                    3
                )
            },
            yearly: {
                price: ethers.utils.formatEther(conditions.yearPrice),
                currency: 'ETH',
                duration: 365,
                discount: this.calculateDiscount(
                    conditions.membershipPrice,
                    conditions.yearPrice,
                    12
                )
            }
        };
    }
    
    calculateDiscount(monthlyPrice, specialPrice, months) {
        const regularTotal = monthlyPrice.mul(months);
        const discount = regularTotal.sub(specialPrice);
        const percentDiscount = discount.mul(10000).div(regularTotal);
        return (percentDiscount / 100).toFixed(1) + '%';
    }
}

// API Routes
app.get('/api/subscription/status', async (req, res) => {
    const manager = new SubscriptionManager(provider);
    const status = await manager.getSubscriptionStatus(
        'myservice',
        req.user.walletAddress
    );
    
    res.json(status);
});

app.get('/api/subscription/renew-options', async (req, res) => {
    const manager = new SubscriptionManager(provider);
    const options = await manager.getRenewalOptions('myservice');
    
    res.json(options);
});
```

### Auto-Renewal Reminder

```javascript
// cron/check-expiring-subscriptions.js
const cron = require('node-cron');

// Run daily at 9 AM
cron.schedule('0 9 * * *', async () => {
    console.log('Checking for expiring subscriptions...');
    
    const users = await getAllUsers();
    const manager = new SubscriptionManager(provider);
    
    for (const user of users) {
        const status = await manager.getSubscriptionStatus(
            'myservice',
            user.walletAddress
        );
        
        // Send reminder if expiring within 7 days
        if (status.active && status.daysLeft <= 7 && status.daysLeft > 0) {
            await sendRenewalReminder(user.email, {
                daysLeft: status.daysLeft,
                expiresAt: status.expiresAt
            });
        }
    }
    
    console.log('Expiration check complete');
});

async function sendRenewalReminder(email, details) {
    // Send email notification
    await sendEmail({
        to: email,
        subject: 'Your subscription is expiring soon',
        html: `
            <h2>Subscription Expiring</h2>
            <p>Your subscription will expire in ${details.daysLeft} days.</p>
            <p>Expires on: ${new Date(details.expiresAt).toLocaleDateString()}</p>
            <a href="https://myservice.com/renew">Renew Now</a>
        `
    });
}
```

---

## Token-Gated Discord Bot

Full Discord bot with token-gated channel access.

```javascript
// bot/index.js
const { Client, GatewayIntentBits } = require('discord.js');
const { ethers } = require('ethers');

const client = new Client({
    intents: [
        GatewayIntentBits.Guilds,
        GatewayIntentBits.GuildMembers
    ]
});

const provider = new ethers.providers.JsonRpcProvider(process.env.RPC_URL);

const tokenAccess = new ethers.Contract(
    TOKEN_ACCESS_ADDRESS,
    TOKEN_ACCESS_ABI,
    provider
);

// Command: /verify
client.on('interactionCreate', async interaction => {
    if (!interaction.isChatInputCommand()) return;
    
    if (interaction.commandName === 'verify') {
        const address = interaction.options.getString('wallet');
        
        try {
            // Check membership
            const isMember = await tokenAccess.hasActiveMembership(
                'mycommunity',
                address
            );
            
            if (isMember) {
                // Save wallet to database
                await saveUserWallet(interaction.user.id, address);
                
                // Grant role
                const memberRole = interaction.guild.roles.cache.find(
                    r => r.name === 'Token Holder'
                );
                await interaction.member.roles.add(memberRole);
                
                await interaction.reply({
                    content: '✅ Verified! Welcome to the community!',
                    ephemeral: true
                });
            } else {
                await interaction.reply({
                    content: '❌ You don\'t meet the token requirements.',
                    ephemeral: true
                });
            }
        } catch (error) {
            console.error('Verification error:', error);
            await interaction.reply({
                content: '❌ Verification failed. Please try again.',
                ephemeral: true
            });
        }
    }
});

// Periodic verification check
setInterval(async () => {
    console.log('Running periodic verification check...');
    
    const guild = client.guilds.cache.first();
    const memberRole = guild.roles.cache.find(r => r.name === 'Token Holder');
    
    for (const [memberId, member] of memberRole.members) {
        const wallet = await getUserWallet(memberId);
        
        if (wallet) {
            const stillMember = await tokenAccess.hasActiveMembership(
                'mycommunity',
                wallet
            );
            
            if (!stillMember) {
                // Remove role if no longer eligible
                await member.roles.remove(memberRole);
                console.log(`Removed role from ${member.user.tag}`);
            }
        }
    }
}, 24 * 60 * 60 * 1000); // Check daily

client.login(process.env.DISCORD_BOT_TOKEN);
```

---

## More Examples

For more integration examples, check out:

- [GitHub Repository](https://github.com/web3club/examples)
- [Sample Projects](https://github.com/web3club/sample-projects)
- [Community Examples](https://github.com/web3club/community-examples)

---

**Need help with your integration?** Join our [Discord](https://discord.gg/web3club) or open an [GitHub Issue](https://github.com/web3club/contracts/issues).

