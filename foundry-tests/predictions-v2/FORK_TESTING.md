# Fork Testing Guide

This guide explains how to test your prediction market contracts on Base networks using Foundry's fork feature **without deploying**.

## What is Fork Testing?

Foundry can create a local copy of a real blockchain (testnet or mainnet) and run tests against it. This means:
- ✅ Uses REAL network state (block number, contracts, balances)
- ✅ Uses REAL Chainlink oracles with live prices
- ✅ No deployment needed (runs locally)
- ✅ No gas costs (free testing)
- ✅ Fast iteration

## Network Options

### 1. Base Sepolia (Testnet) - Recommended for Testing
- **Chain ID**: 84532
- **RPC**: `https://sepolia.base.org`
- **Explorer**: https://sepolia.basescan.org
- **Use Case**: Safe testing environment with free testnet ETH

### 2. Base Mainnet - Production Testing
- **Chain ID**: 8453
- **RPC**: `https://mainnet.base.org`
- **Explorer**: https://basescan.org
- **Chainlink ETH/USD**: `0x71041dddad3595F9CEd3DcCFBe3D1F4b0a16Bb70`
- **Use Case**: Test with real production data and prices

## Running Fork Tests

### Test on Base Sepolia (Testnet)
```bash
cd foundry-tests/predictions-v2

# Run Base Sepolia fork tests
forge test --match-contract BaseSepoliaForkTest --fork-url https://sepolia.base.org -vvv

# Run specific test
forge test --match-test testPredictionMarketOnFork --fork-url https://sepolia.base.org -vvv
```

### Test on Base Mainnet (with real Chainlink data!)
```bash
# Run Base Mainnet fork tests with REAL oracle prices
forge test --match-contract BaseMainnetForkTest --fork-url https://mainnet.base.org -vvv

# Test with specific block (for reproducibility)
forge test --match-contract BaseMainnetForkTest --fork-url https://mainnet.base.org --fork-block-number 10000000 -vvv
```

### Run Local Tests (no fork)
```bash
# Run tests without forking (uses local network)
forge test --match-contract BaseSimpleTest -vv
```

## Test Files

### 1. `BaseSimpleTest.t.sol`
- Basic functionality tests
- No forking required
- Fast execution
- Good for development

### 2. `BaseSepoliaFork.t.sol`
- Tests on Base Sepolia testnet fork
- Simulates testnet environment
- Uses mock oracle (or real if available)

### 3. `BaseMainnetFork.t.sol`
- Tests on Base Mainnet fork
- Uses REAL Chainlink ETH/USD oracle
- Production-like conditions
- Real market prices

## What Gets Tested

All tests verify:
- ✅ **Contract Deployment**: Deploys correctly on Base
- ✅ **Prediction Rounds**: Start, lock, execute cycle works
- ✅ **Betting**: Users can place bull/bear bets
- ✅ **Oracle Integration**: Price feeds update correctly
- ✅ **Reward Distribution**: Winners get paid correctly
- ✅ **Treasury**: Fees collected and claimable
- ✅ **Access Control**: Admin/operator permissions
- ✅ **Gas Usage**: Optimized for Base network

## Example Output

```
Running 1 test for test/BaseMainnetForkTest.t.sol:BaseMainnetForkTest
[PASS] testMainnetPredictionMarket() (gas: 1245678)
Logs:
  === Forked Base Mainnet ===
  Chain ID: 8453
  Block Number: 10123456
  Current ETH Price: $4523
  Total pool: 0.8 ETH
  Bull bets: 0.5 ETH
  Bear bets: 0.3 ETH
  Lock Price: $4523
  Close Price: $4531
  Bulls Won!
  Bull winnings: 0.776 ETH

Test result: ok. 1 passed; 0 failed
```

## How It Works

### Fork Testing Process:
1. **Foundry fetches** the current state of Base network
2. **Creates local copy** with all deployed contracts
3. **Your contract deploys** on this local fork
4. **Tests run** using real network data
5. **Oracle calls** use actual Chainlink prices
6. **No on-chain transactions** - everything is local

### Advantages:
- 🚀 Test against real production environment
- 💰 Zero gas costs (all local)
- ⚡ Fast execution (no waiting for blocks)
- 🔄 Reproducible (can fork at specific block)
- 🎯 Accurate (uses real oracle data)

## Configuration

Edit `.env` file (copy from `.env.example`):
```bash
# Base Sepolia
BASE_SEPOLIA_RPC_URL=https://sepolia.base.org

# Base Mainnet (optional - for production testing)
BASE_MAINNET_RPC_URL=https://mainnet.base.org

# Your wallet (for deployment scenarios)
PRIVATE_KEY=your_private_key_here
```

## Deployment vs Fork Testing

| Feature | Fork Testing | Real Deployment |
|---------|-------------|-----------------|
| Cost | Free | Costs gas |
| Speed | Instant | Wait for blocks |
| Network | Local copy | Real network |
| Oracle | Real data | Real data |
| Risk | Zero | Contract on-chain |
| Use Case | Testing | Production |

## Next Steps

1. ✅ **Run fork tests** to verify everything works
2. ✅ **Iterate quickly** with local testing
3. ✅ **Deploy to testnet** when ready
4. ✅ **Test on testnet** with real users
5. ✅ **Deploy to mainnet** for production

## Useful Commands

```bash
# Run all tests
forge test

# Run with verbose output
forge test -vvv

# Run specific test file
forge test --match-path test/BaseMainnetFork.t.sol

# Run with gas report
forge test --gas-report

# Fork at specific block
forge test --fork-url https://mainnet.base.org --fork-block-number 10000000

# Debug test
forge test --match-test testPredictionMarket -vvvv
```

## Troubleshooting

### Fork fails with "connection error"
- Check RPC URL is correct
- Try alternative RPC (e.g., https://base-sepolia-rpc.publicnode.com)
- Ensure internet connection is stable

### Tests fail with "oracle error"
- Oracle address might be incorrect
- Use BaseSimpleTest.t.sol with mock oracle instead

### Gas errors
- Fork tests use same gas limits as real network
- Optimize contract if needed

## Resources

- [Foundry Fork Testing Docs](https://book.getfoundry.sh/forge/fork-testing)
- [Base Network Docs](https://docs.base.org)
- [Chainlink Price Feeds](https://docs.chain.link/data-feeds)
- [BaseScan](https://basescan.org) - Block explorer
