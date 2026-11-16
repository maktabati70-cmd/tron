# TRON Wallet — Android + Custodial Backend Starter

This is a **development scaffold**, not a production wallet. It is configured conceptually for TRON testnet first.
Do not deposit real funds or enable mainnet withdrawals until an independent security review, legal review,
key-management integration, reconciliation, monitoring, and operational controls are complete.

## Architecture
- `mobile/`: Flutter Android starter UI (Arabic-first).
- `backend/`: FastAPI API scaffold.
- `backend/app/ledger.py`: atomic double-entry ledger primitives.
- `backend/app/chain.py`: explicit adapter boundary for TRON RPC/indexer.
- `backend/app/signing.py`: intentionally unimplemented signing boundary. Integrate an HSM/KMS or audited signer;
  never put private keys in source code, `.env`, the mobile app, or ordinary database fields.

## Fee policy
The sample policy treats the entered amount as the **gross amount deducted from the user's wallet**.
Example: gross 100 USDT, service fee 0.50 USDT, recipient receives 99.50 USDT.
The backend must quote fees, lock funds, and settle only after a verified on-chain result.
Network resource costs (Energy/Bandwidth/TRX) are operator expenses and are accounted separately.

## Run backend locally
```bash
cd backend
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
uvicorn app.main:app --reload
```
Open `http://127.0.0.1:8000/docs`.

## Run Flutter
Install Flutter and Android Studio, then:
```bash
cd mobile
flutter create . --platforms=android
flutter run
```
The UI currently uses mock data and does not connect to a real wallet.

## Next implementation milestones
1. PostgreSQL migrations, users/authentication, MFA and device/session management.
2. Address derivation using audited HD wallet library and isolated key custody.
3. Deposit indexer with confirmed/solidified block tracking, idempotent event processing and reorg handling.
4. Withdrawal queue, risk checks, approval policy, signer/HSM integration and broadcast.
5. TRON resource manager: estimate Energy/Bandwidth, stake/delegate resources or fund controlled fee payer.
6. Reconciliation, admin audit trail, alerts, backups, incident response and penetration testing.

## Important TRON details
TRC-20 contract calls consume Energy and Bandwidth. TRON supports obtaining resources through staking
or receiving delegated resources. A custodial operator can fund resource costs, but this does not mean
the TRON protocol directly accepts USDT as network gas. See the official TRON developer documentation.
