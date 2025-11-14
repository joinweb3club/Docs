# Contract Addresses

## Base Mainnet

All contracts are deployed and verified on Base blockchain.

| Contract | Address | Verified |
|----------|---------|----------|
| ClubManager | `0xd4BAB0d82948955B09760F26F5EDd5E19F2Bee55` | ✅ |
| ClubMembershipQuery | `0x2A152405afB201258D66919570BbD4625455a65f` | ✅ |
| Web3ClubRegistry | `0xD0F9eaf5987a7382AAf01B1553051fc705639FCE` | ✅ |
| Web3ClubNFT | `0xe51DaB702d2A49791488d259CebbA583437727b5` | ✅ |
| ClubPassFactory | `0x27d7E9855250d6Bc84850377E533219FB8794b92` | ✅ |
| TemporaryMembership | `0xFb5567Aaf6057bbf5D729d35f56C5688B36cC940` | ✅ |
| TokenBasedAccess | `0xd286EE3fD7115aB0bf1dC99f8Ce8685EAE200372` | ✅ |
| Web3ClubGovernance | `0x2D1c7083C8EAD07471CA360C28c6a925500C1BB7` | ✅ |
| Web3ClubResolver | `0x523d65CFc9Da66f5bB6544E6FbF31E393ed8Ad2A` | ✅ |
| CrossChainVerification | `0x8cEdb654f2D0157276b9E44c610b0c967C211a84` | ✅ |
| Groth16Verifier (ZKP) | `0x2b76ec6110d0885f7fed4701487f86f770bd5f79` | ✅ |
| ProofOfOwnership | `0x7587CA385f1e10c411638003dA0f1bd3C99b919e` | ✅ |

**Network Details:**
- Chain ID: 8453
- RPC URL: https://mainnet.base.org
- Explorer: https://basescan.org

## Usage

```javascript
// Base Mainnet Contracts
const BASE_CONTRACTS = {
    clubManager: '0xd4BAB0d82948955B09760F26F5EDd5E19F2Bee55',
    query: '0x2A152405afB201258D66919570BbD4625455a65f',
    registry: '0xD0F9eaf5987a7382AAf01B1553051fc705639FCE',
    nft: '0xe51DaB702d2A49791488d259CebbA583437727b5',
    passFactory: '0x27d7E9855250d6Bc84850377E533219FB8794b92',
    tempMembership: '0xFb5567Aaf6057bbf5D729d35f56C5688B36cC940',
    tokenAccess: '0xd286EE3fD7115aB0bf1dC99f8Ce8685EAE200372',
    governance: '0x2D1c7083C8EAD07471CA360C28c6a925500C1BB7',
    resolver: '0x523d65CFc9Da66f5bB6544E6FbF31E393ed8Ad2A',
    crossChain: '0x8cEdb654f2D0157276b9E44c610b0c967C211a84',
    zkpVerifier: '0x2b76ec6110d0885f7fed4701487f86f770bd5f79',
    proofOfOwnership: '0x7587CA385f1e10c411638003dA0f1bd3C99b919e'
};

// Auto-select based on network
const { ethers } = require('ethers');
const provider = new ethers.providers.JsonRpcProvider('https://mainnet.base.org');
const networkId = await provider.getNetwork().then(n => n.chainId);

if (networkId === 8453) {
    console.log('Connected to Base Mainnet');
    // Use BASE_CONTRACTS
}
```

## Verification

All contracts are verified on BaseScan. You can view the source code and interact directly:

- [ClubManager on BaseScan](https://basescan.org/address/0xd4BAB0d82948955B09760F26F5EDd5E19F2Bee55)
- [ClubMembershipQuery on BaseScan](https://basescan.org/address/0x2A152405afB201258D66919570BbD4625455a65f)
- [Web3ClubRegistry on BaseScan](https://basescan.org/address/0xD0F9eaf5987a7382AAf01B1553051fc705639FCE)
- [Web3ClubNFT on BaseScan](https://basescan.org/address/0xe51DaB702d2A49791488d259CebbA583437727b5)
- [ClubPassFactory on BaseScan](https://basescan.org/address/0x27d7E9855250d6Bc84850377E533219FB8794b92)
- [TemporaryMembership on BaseScan](https://basescan.org/address/0xFb5567Aaf6057bbf5D729d35f56C5688B36cC940)
- [TokenBasedAccess on BaseScan](https://basescan.org/address/0xd286EE3fD7115aB0bf1dC99f8Ce8685EAE200372)
- [Web3ClubGovernance on BaseScan](https://basescan.org/address/0x2D1c7083C8EAD07471CA360C28c6a925500C1BB7)
- [Web3ClubResolver on BaseScan](https://basescan.org/address/0x523d65CFc9Da66f5bB6544E6FbF31E393ed8Ad2A)
- [CrossChainVerification on BaseScan](https://basescan.org/address/0x83a4bd9403915a56Ca3e896d69b94bae0Bc6a346)
- [Groth16Verifier (ZKP) on BaseScan](https://basescan.org/address/0x2b76ec6110d0885f7fed4701487f86f770bd5f79)
- [ProofOfOwnership on BaseScan](https://basescan.org/address/0x7587CA385f1e10c411638003dA0f1bd3C99b919e)

## Important Notes

1. **Network**: All contracts are deployed on Base Mainnet (Chain ID: 8453)
2. **RPC Endpoint**: Use https://mainnet.base.org or other Base RPC providers
3. **Verify addresses**: Always double-check contract addresses before integrating
4. **Gas Fees**: Base offers significantly lower gas fees compared to Ethereum mainnet

## Getting Base ETH

To interact with contracts on Base:
- Bridge from Ethereum: https://bridge.base.org
- Buy directly: Major exchanges support Base network
- Faucet (testnet only): For Base Sepolia testing

## Updates

Contract addresses are updated when:
- New versions are deployed
- Upgrades are performed
- New chains are supported

Subscribe to our [Discord announcements](#) to stay updated.

