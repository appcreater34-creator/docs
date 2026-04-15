The fully-formed ref of the branch or tag that triggered the workflow run. For workflows triggered by `push`, this is the branch or tag ref that was pushed. For workflows triggered by `pull_request` that were not merged, this is the pull request merge branch. If the pull request was merged, this is the branch it was merged into. For workflows triggered by `release`, this is the release tag created. For other triggers, this is the branch or tag ref that triggered the workflow run. This is only set if a branch or tag is available for the event type. The ref given is fully-formed, meaning that for branches the format is `refs/heads/<branch_name>`. For pull request events except `pull_request_target` that were not merged, it is `refs/pull/<pr_number>/merge`. `pull_request_target` events have the `ref` from the base branch. For tags it is `refs/tags/<tag_name>`. For example, `refs/heads/feature-branch-1`. For more information about pull request merge branches, see [AUTOTITLE](/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests#pull-request-refs-and-merge-branches).
# Stripe Commission Processor with YBee Integration

Automated payment processing and commission transfers using Stripe webhooks.

## Setup Instructions

### 1. Clone & Install

```bash
git clone <your-repo>
cd stripe-commission-processor
pip install -r requirements.txt
```

### 2. Configure Environment Variables

```bash
cp .env.example .env
```

Edit `.env` with your values:

```env
STRIPE_PUBLIC_KEY=pk_test_YOUR_KEY
STRIPE_SECRET_KEY=sk_test_YOUR_KEY
STRIPE_WEBHOOK_SECRET=whsec_test_YOUR_KEY
COMMISSION_ACCOUNT_ID=acct_YOUR_ACCOUNT
```

**Get your keys from:** https://dashboard.stripe.com/apikeys

### 3. Run Locally

```bash
python main.py
```

Server runs on `http://localhost:5000`

### 4. Set Up Stripe Webhooks

1. Go to https://dashboard.stripe.com/webhooks
2. Add endpoint: `https://yourdomain.com/webhooks/stripe`
3. Select events: `charge.succeeded`, `charge.failed`, `payout.paid`
4. Copy webhook secret to `.env` as `STRIPE_WEBHOOK_SECRET`

### 5. Deploy to Production

```bash
gunicorn main:app
```

## API Endpoints

### Health Check
```bash
GET /health
```

### Create Payment Intent
```bash
POST /api/create-payment-intent
Content-Type: application/json

{
  "amount": 100.00,
  "promoter_account_id": "acct_xxxxx"
}
```

### Get Charge Details
```bash
GET /api/charge/{charge_id}
```

### Calculate Commission
```bash
POST /api/commissions/calculate
Content-Type: application/json

{
  "amount": 100.00
}
```

### Commission History
```bash
GET /api/commissions/history
```

### Stripe Webhook (Automatic)
```
POST /webhooks/stripe
```

## Commission Flow

1. **Customer pays** → Payment intent created
2. **Stripe processes** → Charge succeeds
3. **Webhook triggered** → Automatic commission calculation
4. **Transfer initiated** → Funds sent to promoter account

## Example Usage

```python
import requests

# Create payment
response = requests.post('http://localhost:5000/api/create-payment-intent', json={
    "amount": 100.00,
    "promoter_account_id": "acct_xxxxx"
})

# Calculate commission
response = requests.post('http://localhost:5000/api/commissions/calculate', json={
    "amount": 100.00
})
```

## Support

- Stripe Docs: https://stripe.com/docs
- Flask Docs: https://flask.palletsprojects.com/
- Contact: your-email@example.com
