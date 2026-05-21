# SarikayaPay Mainnet Migration Checklist

**Only proceed to Mainnet after passing ALL Testnet scenarios.** ⚠️

---

## ✅ Pre-Migration Verification

- [ ] All 5 Testnet scenarios completed successfully
- [ ] Contract tested with 100+ transactions
- [ ] Gas costs verified and optimized
- [ ] Security audit completed
- [ ] Demo video recorded (2-3 minutes)
- [ ] Pitch deck prepared
- [ ] Team coordination finalized

---

## 🔒 Security Considerations

### **Before Mainnet Deployment:**

1. **Code Review**
   - [ ] Contract reviewed by 2+ team members
   - [ ] Logic verified for edge cases
   - [ ] No hardcoded secrets in code

2. **Testing**
   - [ ] 100%+ test coverage
   - [ ] Stress tested with 1,000+ transactions
   - [ ] Error handling verified
   - [ ] Rollback procedure documented

3. **Data Validation**
   - [ ] Input validation on all functions
   - [ ] Integer overflow checks
   - [ ] Timestamp validation

---

## 💰 Mainnet Cost Estimation

| Metric | Value | Notes |
|--------|-------|-------|
| Contract Deploy | 600-800 stroops | One-time cost (~$0.001) |
| Merchant Init | 1,500-2,000 stroops | Per merchant (~$0.0003) |
| Single Transaction | 1,000-1,500 stroops | Per transaction (~$0.0001) |
| Batch 10 txns | 1,500-2,000 stroops | 90% savings (~$0.0002) |

**Monthly Cost for 100 Merchants (30 transactions each):**
- Traditional MDR (1-3%): ₱15,000-45,000
- SarikayaPay: ₱50-100 (~$1-2)
- **Merchant Savings: ₱14,900-44,900/month** 🎉

---

## 🚀 Mainnet Deployment Steps

### **Step 1: Prepare for Mainnet**

```bash
# Update environment variables
export SOROBAN_NETWORK_PASSPHRASE="Public Global Stellar Network ; September 2015"
export SOROBAN_RPC_HOST="https://soroban-mainnet.stellar.org"

# Verify mainnet credentials
soroban config identity ls
soroban config identity show pilanthony
```

### **Step 2: Fund Mainnet Account**

```bash
# Your Mainnet wallet needs XLM for deployment
# Minimum: 5 XLM for safety

# Option 1: From another wallet
# Send 5+ XLM to: GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD

# Option 2: Exchange service
# Buy XLM on Kraken, Binance, etc. and transfer

# Verify balance
soroban contract invoke \
  --id account \
  --source-account GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD \
  --network mainnet \
  -- \
  balance
```

### **Step 3: Deploy to Mainnet**

```bash
# Final build
cd contracts/sarikayapay
soroban contract build

# Deploy
soroban contract deploy \
  --wasm target/wasm32-unknown-unknown/release/sarikayapay.wasm \
  --source-account GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD \
  --network mainnet

# ✅ You'll receive:
# Contract deployed successfully!
# Contract ID: CAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABUHA
```

### **Step 4: Initialize First Merchant (Real Money!)**

```bash
export MAINNET_CONTRACT_ID="CAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABUHA"

# Initialize with real XLM (you can start small)
soroban contract invoke \
  --id $MAINNET_CONTRACT_ID \
  --source-account GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD \
  --network mainnet \
  -- \
  init_merchant \
  --merchant_id merchant_ph_live_001 \
  --initial_balance 100

# ✅ Merchant account created on Mainnet
```

### **Step 5: Monitor & Verify**

```bash
# Check contract on Mainnet
# https://stellar.expert/explorer/mainnet/contract/[YOUR_CONTRACT_ID]

# View transactions
# https://stellar.expert/explorer/mainnet/account/GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD
```

---

## 📊 Mainnet Monitoring Dashboard

### **Stellar Expert (Real-time)**
- **Your Account:** https://stellar.expert/explorer/mainnet/account/GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD
- **Contract:** https://stellar.expert/explorer/mainnet/contract/[YOUR_CONTRACT_ID]
- **Transactions:** See all operations in real-time
- **Balances:** Confirm settlements

### **Custom Dashboard** (Optional)
```bash
# Build custom monitoring
# Query Horizon API for live data
curl https://horizon.stellar.org/accounts/GBCOMIMPXHBHW3WPBLFN5NQ4XQUL6XYZX3SBP7TFPK4VRPTSD4HCLJMD/transactions
```

---

## 🎯 Post-Deployment

### **Day 1: Onboard First Real Merchants**
- [ ] Set up 5-10 pilot merchants
- [ ] Test real transaction recording
- [ ] Verify settlement payments
- [ ] Collect feedback

### **Week 1: Scale Operations**
- [ ] Add 50+ merchants
- [ ] Monitor contract performance
- [ ] Optimize based on real usage
- [ ] Plan marketing push

### **Month 1: Full Launch**
- [ ] 500+ merchants on platform
- [ ] ₱1-2M/month transaction volume
- [ ] Proven ₱500K+/month saved for merchants
- [ ] Prepare Series A pitch

---

## ⚠️ Rollback Procedure

If critical issues occur:

1. **Pause new registrations**
   - Stop accepting new merchants temporarily
   - Notify existing users

2. **Deploy hotfix**
   ```bash
   # Build patched version
   soroban contract build
   
   # Deploy new contract
   soroban contract deploy ... --network mainnet
   ```

3. **Migrate to new contract**
   - Update frontend to use new contract ID
   - Run migration script for existing data
   - Verify all balances

4. **Communicate to users**
   - Post on social media
   - Email merchants
   - Update website

---

## 📞 Emergency Contacts

- **Stellar Support:** https://stellar.org/contact
- **Soroban Support:** soroban@stellar.org
- **Your Team:** [Add contact info]

---

## 🏆 Success Metrics (Post-Deployment)

Track these KPIs:

| Metric | Target | Notes |
|--------|--------|-------|
| Active Merchants | 100+ in Month 1 | Growth indicator |
| Daily Transactions | 1,000+ | Volume metric |
| Average Settlement | ₱5,000 | Merchant revenue |
| Fees Saved | ₱500-2,000/merchant/month | Value proposition |
| Uptime | 99.9%+ | Reliability |
| Gas Cost/Txn | <2,000 stroops | Efficiency |

---

## 🎬 Demo Video Script (Mainnet Launch)

```
0:00 - Show problem (sari-sari store struggling with MDR)
0:15 - Show solution (SarikayaPay dashboard)
0:30 - Live demo: Record 10 transactions in 1 batch
0:45 - Show gas savings (₱25 vs ₱250 traditional)
1:00 - Show Mainnet transaction on Stellar Expert
1:15 - Show merchant impact (₱500-2,000/week saved)
1:30 - Call to action (join platform)
2:00 - End with metrics
```

---

**Congratulations on reaching Mainnet! 🚀** 

Next step: Onboard real merchants and prove the impact!
