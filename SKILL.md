---
name: salesforce-query-openclaw
description: Query Salesforce CRM data via Python with schema-adaptive onboarding. Use when you need to connect to a Salesforce org, discover available objects/fields, detect optional signal systems (like 6sense), build reusable field mappings, and run account/contact/opportunity/campaign/activity research without hardcoded org assumptions.
---

# Salesforce Query (Schema-Adaptive)

Use this skill to onboard any Salesforce org, not just RevenueCat-specific field layouts.

## 1) Configure credentials

Required variables:
- `SALESFORCE_CLIENT_ID`
- `SALESFORCE_CLIENT_SECRET`
- `SALESFORCE_INSTANCE_URL`

Preferred flow:
1. Create a Salesforce Connected App and dedicated least-privilege integration user.
2. Store secrets in a secure secret manager if possible.
3. Default persistence is macOS Keychain. Plaintext credential-file fallback has been removed.

Credentials can be persisted locally via:

```python
from salesforce_client import save_credentials, check_credentials

ok, missing = check_credentials()
if not ok:
    save_credentials(client_id="...", client_secret="...", instance_url="...")
```

## 2) Run onboarding discovery

Run an end-to-end discovery pass:

```bash
python3 scripts/onboarding.py
```

For stricter security (no credential file persisted):

```bash
python3 scripts/onboarding.py --no-save
```

If credentials are missing, onboarding prompts for them and saves them before continuing.

If credentials are not ready, run quick setup guidance mode:

```bash
python3 scripts/onboarding.py --guided
```

You can also type `help` at the first credential prompt to show setup guidance inline.

If Python dependency `requests` is missing, onboarding auto-bootstraps a local venv at `.venv-salesforce` and continues.

Run a credential safety check any time:

```bash
python3 scripts/credential_doctor.py
```

For automation or CI, pass credentials directly:

```bash
python3 scripts/onboarding.py \
  --client-id "$SALESFORCE_CLIENT_ID" \
  --client-secret "$SALESFORCE_CLIENT_SECRET" \
  --instance-url "$SALESFORCE_INSTANCE_URL" \
  --non-interactive
```

Session-only credentials (no disk persistence):

```bash
python3 scripts/onboarding.py \
  --client-id "$SALESFORCE_CLIENT_ID" \
  --client-secret "$SALESFORCE_CLIENT_SECRET" \
  --instance-url "$SALESFORCE_INSTANCE_URL" \
  --non-interactive --no-save
```

Optional custom output path:

```bash
python3 scripts/onboarding.py --out ~/.config/openclaw/salesforce_profile.json
```

This will:
1. Validate auth.
2. Discover accessible objects.
3. Describe fields on core GTM objects.
4. Detect optional signal packs (intent, revenue, risk, engagement).
5. Generate targeted follow-up questions.
6. Save a reusable profile JSON.

## 3) Ask adaptive follow-ups

Use generated questions from onboarding output, then persist answers in the profile.

Reference question bank:
- `references/question-bank.md`

## 4) Build and use mapping profile

Use `assets/profile-template.json` as the canonical profile format.

Keep uncertain mappings marked as low confidence until user confirms.

## 5) Query patterns

For ready-to-use workflows, use:

```bash
python3 scripts/starter_workflows.py
```

or import functions from `scripts/starter_workflows.py`:
- `top_accounts_to_work_today(...)`
- `pipeline_rescue_board(...)`
- `campaign_followup_opportunities(...)`

Each returns a structured payload with `summary`, `rows`, and `next_best_action` to proactively guide users.

Use `salesforce_client.py` for regular querying:

```python
from salesforce_client import SalesforceClient

sf = SalesforceClient()
accounts = sf.get_account_by_name("Acme")
opps = sf.get_open_opportunities()
```

For schema discovery:

```python
from salesforce_client import SalesforceClient
from schema_discovery import SchemaDiscovery

sf = SalesforceClient()
discovery = SchemaDiscovery(sf)
schema = discovery.discover()
```

## References
- Discovery approach: `references/discovery-playbook.md`
- Follow-up prompts: `references/question-bank.md`
- Security and credential setup: `references/security-setup.md`
- Starter workflow guide: `references/starter-workflows.md`
