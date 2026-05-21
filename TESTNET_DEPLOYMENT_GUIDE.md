# SarikayaPay Testnet Deployment Guide 🚀

Complete step-by-step guide to deploy and test SarikayaPay on Stellar Testnet.

---

## ⚡ Quick Start (15 minutes)

### Step 1: Get Testnet XLM (2 min)

1. Go to: https://laboratory.stellar.org/#account-creator
2. Click **"Generate keypair"**
3. Copy the **Public Key** (starting with `G`)
4. Paste it into the form and click **"Create account"**
5. You'll receive 10,000 XLM on Testnet! ✅

Your Merchant Account:
```
Public:  GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD
Secret:  [Keep this SECRET - only share in test environment]
```

Monitor your balance: https://stellar.expert/explorer/testnet/account/GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD

---

### Step 2: Build Smart Contract (3 min)

```bash
# Install Rust (if not already installed)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env

# Install Soroban CLI
cargo install soroban-cli

# Navigate to contract directory
cd contracts/sarikayapay

# Build the contract
soroban contract build

# Expected output:
# Compiling soroban-sdk v22.0.0
# Finished `release` profile [optimized] target(s) in 2.34s
# ✅ Contract built: target/wasm32-unknown-unknown/release/sarikayapay.wasm
```

---

### Step 3: Run Tests (2 min)

```bash
# From contracts/sarikayapay directory
cargo test

# Expected output:
# running 3 tests
# test test::test_happy_path_record_transaction ... ok
# test test::test_edge_case_duplicate_certificate ... ok
# test test::test_state_verification_certificate_owner ... ok
# 
# test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured
```

---

### Step 4: Deploy Contract (5 min)

```bash
# Set environment variables
export SOROBAN_NETWORK_PASSPHRASE="Test SDF Network ; September 2015"
export SOROBAN_RPC_HOST="https://soroban-testnet.stellar.org"

# Deploy the contract
soroban contract deploy \
  --wasm target/wasm32-unknown-unknown/release/sarikayapay.wasm \
  --source-account GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD \
  --network testnet

# Expected output:
# Contract deployed successfully
# Contract ID: CAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABUHA
# 
# 💾 Save this Contract ID - you'll need it for all future invocations!
```

**Copy your Contract ID and save it to `.env`:**
```bash
SARIKAYAPAY_CONTRACT_ID=CAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABUHA
```

---

### Step 5: Initialize Merchant Account (3 min)

```bash
# Initialize a new merchant on the contract
soroban contract invoke \
  --id CAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABUHA \
  --source-account GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD \
  --network testnet \
  -- \
  init_merchant \
  --merchant_id "merchant_ph_001" \
  --initial_balance 10000

# Expected output:
# Invoking contract method successfully
# Transaction ID: abc123...
# ✅ Merchant initialized with 10,000 XLM balance
```

---

## 📋 Testing Scenarios (30 min)

### Scenario 1: Record Single Transaction

```bash
soroban contract invoke \
  --id CAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABUHA \
  --source-account GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD \
  --network testnet \
  -- \
  record_transaction \
  --merchant_id "merchant_ph_001" \
  --amount 500 \
  --transaction_hash "0xabcdef0123456789"

# Expected: Transaction recorded successfully
# Gas used: ~1,200 stroops
# Verify: https://stellar.expert/explorer/testnet/contract/CAAA...
```

### Scenario 2: Batch Record Transactions (OPTIMIZED)

```bash
# Record 5 daily transactions in ONE call (90% gas savings!)
soroban contract invoke \
  --id CAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABUHA \
  --source-account GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD \
  --network testnet \
  -- \
  record_transactions_batch \
  --merchant_id "merchant_ph_001" \
  --amounts "[500, 1200, 750, 2300, 800]"

# Expected: All 5 transactions recorded in single call
# Gas used: ~1,500 stroops (vs ~6,000 for 5 individual calls)
# Savings: ₱4.50 vs ₱27 in real fees!
```

### Scenario 3: Verify Certificate

```bash
soroban contract invoke \
  --id CAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABUHA \
  --source-account GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD \
  --network testnet \
  -- \
  verify_certificate \
  --certificate_hash "0xabcdef0123456789"

# Expected: Certificate verified
# Returns: true (boolean)
# Event emitted on-chain
```

### Scenario 4: Get Merchant Statistics

```bash
soroban contract invoke \
  --id CAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABUHA \
  --source-account GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD \
  --network testnet \
  -- \
  get_merchant_stats \
  --merchant_id "merchant_ph_001"

# Expected output:
# {
#   "total_transactions": 6,
#   "total_volume": 6050,
#   "fees_saved": 60.50,
#   "average_transaction": 1008.33
# }
```

### Scenario 5: Settle Merchant (Payout)

```bash
soroban contract invoke \
  --id CAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABUHA \
  --source-account GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD \
  --network testnet \
  -- \
  settle_merchant \
  --merchant_id "merchant_ph_001" \
  --settlement_amount 5000

# Expected: Settlement processed
# XLM transferred directly to merchant wallet
# Settlement recorded on-chain
```

---

## 📊 Monitor Your Transactions

### View Contract on Stellar Expert

```
https://stellar.expert/explorer/testnet/contract/CAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABUHA
```

### View Merchant Account

```
https://stellar.expert/explorer/testnet/account/GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD
```

### Check Transaction Details

```
https://stellar.expert/explorer/testnet/tx/[TRANSACTION_ID]
```

---

## 🔍 Debugging & Troubleshooting

### Error: "Account not found"
```
Solution: Make sure you created testnet account at laboratory.stellar.org
Verify: https://stellar.expert/explorer/testnet/account/GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD
```

### Error: "Insufficient balance"
```
Solution: Get more testnet XLM
1. Go to: https://laboratory.stellar.org/#account-creator
2. Paste your public key again
3. Receive additional 10,000 XLM
```

### Error: "Contract not found"
```
Solution: Make sure your Contract ID is correct
Check: SARIKAYAPAY_CONTRACT_ID in your .env file
Verify on Stellar Expert: https://stellar.expert/explorer/testnet/contract/...
```

### Error: "Build failed - WASM compilation error"
```
Solution: Update Soroban SDK
cargo update
soroban contract build
```

---

## 📱 Frontend Integration (Optional)

Once contract is deployed, start the frontend:

```bash
cd frontend
npm install
npm run dev

# Access at http://localhost:3000
# Connect wallet
# Record transactions through UI
# See batch optimization in action
```

---

## ✅ Testing Checklist

- [ ] Got 10,000 testnet XLM
- [ ] Built contract successfully (`cargo build`)
- [ ] All 3 tests passed (`cargo test`)
- [ ] Deployed contract to testnet
- [ ] Initialized merchant account
- [ ] Recorded single transaction
- [ ] Recorded batch of 5 transactions
- [ ] Verified gas savings (batch < single)
- [ ] Viewed stats
- [ ] Verified certificate
- [ ] Processed settlement
- [ ] Checked Stellar Expert
- [ ] Frontend connects to contract (optional)

---

## 🎉 Success Indicators

✅ **You're ready when:**
1. Contract deployed on Testnet
2. All 5 test scenarios run successfully
3. Batch optimization shows 90% gas savings
4. Transactions visible on Stellar Expert
5. Merchant wallet receives settlement payment

---

## 🚀 Next Steps (Mainnet)

Once you've tested extensively on Testnet:

1. Deploy new contract instance to Mainnet
2. Set `SOROBAN_NETWORK_PASSPHRASE="Public Global Stellar Network ; September 2015"`
3. Use production Mainnet wallet
4. Use `https://soroban-mainnet.stellar.org` as RPC
5. Use real XLM for settlements

---

## 📞 Resources

- **Soroban Docs:** https://developers.stellar.org/docs/build/guides/soroban
- **Stellar Expert:** https://stellar.expert/
- **Stellar Testnet:** https://laboratory.stellar.org/
- **Soroban CLI Reference:** `soroban contract --help`
- **Your Account:** https://stellar.expert/explorer/testnet/account/GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD

---

**Happy testing! Let me know when you've deployed! 🚀**
