# Not Only Bitcoin Tips

DApp for creating fundraising campaigns on Internet Computer with Internet Identity authentication support.

## Features

- 🎯 Creating fundraising campaigns
- 📱 QR codes for campaign sharing
- 🔐 Secure authentication via Internet Identity
- 💰 Support for multiple cryptocurrencies (ICP, BTC, ETH, USDT)
- 🌐 Decentralized on Internet Computer

## Tech Stack

- **Frontend**: React + TypeScript + Vite + Tailwind CSS
- **Backend**: Motoko (Internet Computer)
- **Authentication**: Internet Identity
- **Deployment**: DFX + Internet Computer

## Quick Start

### Prerequisites

- [DFX](https://internetcomputer.org/docs/current/developer-docs/setup/install/) installed
- [Node.js](https://nodejs.org/) (v16 or higher)
- [npm](https://www.npmjs.com/) or [yarn](https://yarnpkg.com/)

### Local Development

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd notonlybitcointips
   ```

2. **Install dependencies**
   ```bash
   npm install
   cd frontend && npm install
   ```

3. **Start local Internet Computer replica**
   ```bash
   dfx start --clean --background
   ```

4. **Deploy canisters**
   ```bash
   dfx deploy
   ```

5. **Start frontend development server**
   ```bash
   cd frontend && npm run dev
   ```

6. **Open the application**
   - Frontend: http://localhost:5173
   - Internet Identity: http://127.0.0.1:4943/?canisterId=umunu-kh777-77774-qaaca-cai&id=u6s2n-gx777-77774-qaaba-cai

### Environment Setup

1. **Create canister_ids.json** (if not exists):
   ```bash
   cp canister_ids.json.example canister_ids.json
   ```

2. **Create frontend/.env**:
   ```bash
   cd frontend
   echo "VITE_CANISTER_ID_USER_CANISTER=$(dfx canister id user_canister)" > .env
   echo "VITE_CANISTER_ID_INTERNET_IDENTITY=$(dfx canister id internet_identity)" >> .env
   echo "VITE_CANISTER_HOST=http://127.0.0.1:4943" >> .env
   echo "DFX_NETWORK=local" >> .env
   echo "VITE_DFX_NETWORK=local" >> .env
   ```

## Usage

### Authentication

1. Open the application in your browser
2. Click "Sign in with Internet Identity"
3. Follow the instructions to create or sign in to Internet Identity

### Creating a Campaign

1. After authentication, fill out the registration form
2. Go to campaign creation
3. Fill in the name, description, and select currencies for donations
4. Click "Create Campaign"

### Sharing Campaigns

- Each campaign gets a unique URL and QR code
- Share the link or QR code to collect donations
- Support for multiple cryptocurrencies

### Sending ICP Donations

Each campaign has its own ICP account identifier. Send ICP tokens from your wallet directly to this address. The identifier is derived from the canister ID and a unique subaccount stored with the campaign.

For local testing the address works with the local ledger. In production use the same address on the mainnet ledger. Configure `VITE_CANISTER_HOST` and `VITE_DFX_NETWORK` in `frontend/.env` to switch between environments.

## Project Structure

```
notonlybitcointips/
├── backend/
│   ├── user_canister.did    # Candid interface
│   └── user_canister.mo     # Motoko backend
├── frontend/
│   ├── src/
│   │   ├── App.tsx          # Main app component
│   │   ├── MainApp.tsx      # Authentication & campaign creation
│   │   ├── CampaignPage.tsx # Campaign page
│   │   └── canisters/       # Canister integration
│   ├── package.json
│   └── vite.config.ts
├── dfx.json                 # DFX configuration
└── canister_ids.json        # Canister IDs mapping
```

## Deployment

### Local Deployment

```bash
dfx deploy
```

### Mainnet Deployment

```bash
dfx deploy --network ic
```

## Development

### Backend (Motoko)

The backend is written in Motoko and handles:
- User management
- Campaign creation and retrieval
- Data persistence on Internet Computer

#### Recent Fixes

**Canister Upgrade Data Preservation (Fixed)**

Previously, there was a critical issue where campaign data was lost during canister upgrades due to incorrect order of operations in the `postupgrade` system function. This has been resolved:

- **Problem**: The `campaigns` array was cleared before being used to restore the `campaignsMap`, causing all campaigns to disappear after upgrades
- **Solution**: Modified `postupgrade` to create local copies of stable arrays before clearing them, ensuring data is preserved during HashMap restoration
- **Impact**: All campaigns and users are now correctly preserved during canister upgrades

For testing upgrade functionality, see the [TESTING.md](TESTING.md) file for detailed upgrade test scenarios.

### Frontend (React)

The frontend is built with React and provides:
- User authentication via Internet Identity
- Campaign creation interface
- QR code generation for sharing
- Responsive design with Tailwind CSS

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test locally
5. Submit a pull request

## License

This project is licensed under the MIT License.

## Support

For issues and questions:
- Check the [Internet Computer documentation](https://internetcomputer.org/docs/current/developer-docs/)
- Review the [DFX documentation](https://internetcomputer.org/docs/current/developer-docs/setup/install/)
- Open an issue in this repository

## Acknowledgments

- Built for ICP Hackathon
- Powered by Internet Computer
- Uses Internet Identity for authentication
