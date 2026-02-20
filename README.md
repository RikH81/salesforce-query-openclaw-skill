# Salesforce Query (Schema-Adaptive) Skill

Query Salesforce CRM data with adaptive schema discovery and reusable GTM workflows.

## What this skill does

- Connects to any Salesforce org via OAuth client credentials
- Discovers available objects/fields at runtime (no hardcoded org assumptions)
- Builds a reusable profile for account/contact/opportunity/campaign/activity workflows
- Provides starter workflow scripts for prioritization and outreach research

## Security model

This skill uses **Keychain-only credential persistence** on macOS:

- Credentials are stored in macOS Keychain service: `openclaw.salesforce`
- No plaintext credential file fallback
- Credentials can be provided session-only with `--no-save`

Required variables:

- `SALESFORCE_CLIENT_ID`
- `SALESFORCE_CLIENT_SECRET`
- `SALESFORCE_INSTANCE_URL`

## Quick start

```bash
python3 scripts/onboarding.py
```

Non-interactive:

```bash
python3 scripts/onboarding.py \
  --client-id "$SALESFORCE_CLIENT_ID" \
  --client-secret "$SALESFORCE_CLIENT_SECRET" \
  --instance-url "$SALESFORCE_INSTANCE_URL" \
  --non-interactive
```

Session-only credentials (no persistence):

```bash
python3 scripts/onboarding.py \
  --client-id "$SALESFORCE_CLIENT_ID" \
  --client-secret "$SALESFORCE_CLIENT_SECRET" \
  --instance-url "$SALESFORCE_INSTANCE_URL" \
  --non-interactive --no-save
```

## Validate credential posture

```bash
python3 scripts/credential_doctor.py
```

Expected result for hardened setup:

- all three credential keys present in Keychain
- legacy plaintext credential files absent

## Notes for production

- Use a dedicated, least-privilege Salesforce integration user
- Rotate secrets immediately if exposed
- Keep repository free of secrets and org-specific sensitive data
