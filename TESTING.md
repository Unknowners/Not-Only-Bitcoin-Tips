# Testing Guide

This document provides comprehensive testing instructions for the Not Only Bitcoin Tips application.

## Prerequisites

Before testing, ensure you have:
- DFX installed and configured
- Node.js and npm installed
- Local Internet Computer replica running
- All canisters deployed

## Local Testing Setup

### Step 1: Start Local Environment

```bash
# Start local replica
dfx start --clean --background

# Deploy canisters
dfx deploy

# Start frontend development server
cd frontend && npm run dev
```

### Step 2: Internet Identity Setup

1. Open http://127.0.0.1:4943/?canisterId=umunu-kh777-77774-qaaca-cai&id=u6s2n-gx777-77774-qaaba-cai
2. Create a new identity or sign in to existing one
3. Note your Principal ID for testing

### Step 3: User Registration

1. Open http://localhost:5173
2. Click "Sign in with Internet Identity"
3. Fill out the registration form (name and email)
4. Click "Complete Registration"

### Step 4: Campaign Creation

1. Fill out the campaign creation form
2. Select currencies for donations
3. Click "Create Campaign"
4. Verify QR code and link generation

## Test Scenarios

### Authentication Flow

- [ ] User can sign in with Internet Identity
- [ ] User can see their Principal ID
- [ ] User can logout successfully
- [ ] User registration works correctly

### Campaign Management

- [ ] User can create new campaigns
- [ ] Campaign details are saved correctly
- [ ] QR codes are generated properly
- [ ] Campaign links work correctly

### Verifying ICP Deposits

1. Obtain the campaign's deposit account from the campaign page.
2. Send a small amount of ICP to this account using your wallet or the ledger canister.
3. Check the balance of the subaccount via the ledger canister to confirm the deposit.

### Data Persistence

- [ ] Campaigns persist after page refresh
- [ ] User data is saved correctly
- [ ] Campaign retrieval works properly

### Canister Upgrade Testing

**Critical Test: Data Preservation During Upgrade**

This test verifies that campaigns and users are preserved during canister upgrades.

#### Manual Test Scenario:

1. **Setup Initial Data:**
   ```bash
   # Deploy canister
   dfx deploy user_canister
   
   # Create test user and campaign via Candid UI or frontend
   # Note the campaign ID for verification
   ```

2. **Create Test Campaign:**
   - Create a user account
   - Create at least one campaign with name "Test Campaign Before Upgrade"
   - Record the campaign ID returned
   - Verify campaign exists: `getCampaign(campaignId)`

3. **Perform Canister Upgrade:**
   ```bash
   # Upgrade the canister (this triggers preupgrade/postupgrade)
   dfx deploy user_canister --mode upgrade
   ```

4. **Verify Data Preservation:**
   - Call `getCampaign(campaignId)` - should return the campaign
   - Call `getAllCampaigns()` - should include the test campaign
   - Verify campaign details match original data
   - Check that `campaignsMap.size()` matches expected count

5. **Expected Results:**
   - [ ] Campaign exists after upgrade
   - [ ] Campaign data is identical to pre-upgrade state
   - [ ] All campaign fields are preserved (name, description, owner, etc.)
   - [ ] No data loss occurs during upgrade process

#### Debug Information:

The postupgrade function now includes debug logging. Check dfx logs for:
```
Postupgrade completed: restored X users and Y campaigns
```

#### Complete Data Preservation Verification:

**All Stable Data (Preserved during upgrade):**
1. **Users**: All user accounts with id, name, email, createdAt
2. **Campaigns**: All campaigns with complete details including subaccounts
3. **Initializer Principal**: The original deployer Principal (newly added)

**Test all preserved data:**
```bash
# After upgrade, verify:
dfx canister call user_canister getAllUsers
dfx canister call user_canister getAllCampaigns  
dfx canister call user_canister getInitializerPrincipal
dfx canister call user_canister userExists
```

#### Critical Test: Wallet Address Preservation

**Wallet addresses are FULLY preserved during upgrades!**

1. **Before upgrade - record wallet data:**
   ```bash
   # Get campaign details including subaccount
   dfx canister call user_canister getCampaign '("your_campaign_id")'
   dfx canister call user_canister getCampaignSubaccount '("your_campaign_id")'
   
   # Note the subaccount blob for comparison
   ```

2. **After upgrade - verify wallet access:**
   ```bash
   # Same commands should return identical results
   dfx canister call user_canister getCampaign '("your_campaign_id")'
   dfx canister call user_canister getCampaignSubaccount '("your_campaign_id")'
   
   # Subaccount blob must be IDENTICAL
   # Frontend will generate the same ICP address
   ```

3. **Wallet Address Components (All Preserved):**
   - ✅ **Campaign ID**: Stored in stable `campaigns` array
   - ✅ **Subaccount**: 32-byte blob stored in `campaign.subaccount`
   - ✅ **Canister Principal**: Never changes for the canister
   - ✅ **Generation Algorithm**: Deterministic (same inputs = same address)

4. **Expected Results:**
   - [ ] Same campaign ID returns identical subaccount
   - [ ] Frontend generates identical ICP address
   - [ ] All existing wallet addresses remain accessible
   - [ ] No funds are lost or become inaccessible

#### Automated Test (Future Enhancement):

For automated testing, consider creating a Motoko test file that:
1. Simulates preupgrade by calling the system function
2. Verifies stable variable contents
3. Simulates postupgrade and verifies HashMap restoration
4. Confirms no data loss occurred

## API Testing

### User Canister Methods

Test the following methods via Candid UI:

1. **whoami()** - Should return Principal ID
2. **createUser(name, email)** - Should create user
3. **userExists()** - Should return true for existing users
4. **createCampaign(name, description, tokens)** - Should create campaign
5. **getCampaign(id)** - Should return campaign details
6. **getUserCampaigns(userId)** - Should return user's campaigns

### Candid UI Access

- Local: http://127.0.0.1:4943/?canisterId=umunu-kh777-77774-qaaca-cai&id=uzt4z-lp777-77774-qaabq-cai

## Troubleshooting

### Common Issues

1. **"No such file or directory" error** - Make sure dfx is running
2. **Authentication error** - Check if Internet Identity canister is deployed
3. **Connection error to canister** - Verify canister ID in frontend

### Debug Steps

1. Check browser console for errors
2. Verify canister IDs in frontend/.env
3. Check dfx logs: `dfx logs`
4. Restart local replica if needed

## Performance Testing

### Load Testing

- Test with multiple campaigns
- Verify QR code generation performance
- Check memory usage with large datasets

### Browser Compatibility

Test in:
- Chrome (recommended)
- Firefox
- Safari
- Edge

## Security Testing

### Authentication

- Verify Internet Identity integration
- Test logout functionality
- Check session management

### Data Validation

- Test with invalid input data
- Verify error handling
- Check XSS prevention

## Reporting Issues

When reporting issues, include:
- Browser and version
- DFX version
- Error messages from console
- Steps to reproduce
- Expected vs actual behavior 