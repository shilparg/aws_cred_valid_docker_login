# aws_cred_valid_docker_login
AWS Credential Validation and Docker LoginHere’s your complete, audit-ready onboarding bundle captured in Markdown—ready for PDF export, repo inclusion, or executive presentation. It includes:

- 📘 `NOTES.md` for onboarding and governance
- 🛠️ `Makefile` target for automation
- 🐚 `login_ecr.sh` script for CLI reuse
- 🔁 `deploy.sh` integration
- ⚙️ CI/CD pipeline snippet

---

## 📘 `NOTES.md` — AWS ECR Login & Credential Validation

```markdown
# 🔐 AWS ECR Login & Credential Validation

This guide documents the credential validation and Docker login flow for AWS Elastic Container Registry (ECR), integrated into both the Makefile and CLI scripts for modular reuse.

---

## 🧪 Credential Validation Flow

Before attempting any AWS CLI or Docker operations, validate credentials using:

```bash
aws sts get-caller-identity
```

If this fails, credentials are either:
- Missing
- Expired
- Incorrectly configured

---

## 🛠️ Resetting AWS Credentials

To reset credentials:

```bash
unset AWS_ACCESS_KEY_ID
unset AWS_SECRET_ACCESS_KEY
unset AWS_SESSION_TOKEN
aws configure
```

You’ll be prompted for:
- AWS Access Key ID
- AWS Secret Access Key
- Default region (e.g., `us-east-1`)
- Output format (e.g., `json`)

---

## 🐳 Docker Login to AWS ECR

Use the following command to authenticate Docker with ECR:

```bash
aws ecr get-login-password --region us-east-1 | \
docker login --username AWS --password-stdin 258945422155.dkr.ecr.us-east-1.amazonaws.com
```

---

## 🧩 Makefile Target: `login-ecr`

```makefile
.PHONY: login-ecr

login-ecr:
	@echo "🔍 Validating AWS credentials..."
	@if ! aws sts get-caller-identity > /dev/null 2>&1; then \
		echo "❌ AWS credentials are invalid or missing."; \
		echo "🛠️ Reconfiguring AWS CLI..."; \
		aws configure; \
		echo "✅ AWS credentials updated."; \
	fi
	@echo "🔐 Logging in to AWS ECR..."
	@aws ecr get-login-password --region us-east-1 | \
	docker login --username AWS --password-stdin 258945422155.dkr.ecr.us-east-1.amazonaws.com
	@echo "✅ Docker login complete."
```

---

## 🐚 `login_ecr.sh` — Reusable Script

```bash
#!/bin/bash

set -euo pipefail

AWS_REGION="us-east-1"
ECR_URL="258945422155.dkr.ecr.${AWS_REGION}.amazonaws.com"

echo "🔍 Validating AWS credentials..."
if ! aws sts get-caller-identity > /dev/null 2>&1; then
  echo "❌ AWS credentials are invalid or missing."
  echo "🛠️ Reconfiguring AWS CLI..."
  aws configure
  echo "✅ AWS credentials updated."
fi

echo "🔐 Logging in to AWS ECR..."
aws ecr get-login-password --region "$AWS_REGION" | \
docker login --username AWS --password-stdin "$ECR_URL"

echo "✅ Docker login successful."
```

Make it executable:
```bash
chmod +x login_ecr.sh
```

---

## 🔁 `deploy.sh` Integration

Inside `deploy.sh`, you can call either the script or Makefile target:

```bash
#!/bin/bash
set -euo pipefail

# Option 1: Call script directly
./login_ecr.sh

# Option 2: Use Makefile target
make login-ecr
```

---

## ⚙️ CI/CD Integration Snippet

In GitHub Actions or GitLab CI, include this in your job step:

```yaml
- name: AWS ECR Login
  run: |
    aws configure set aws_access_key_id ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws configure set aws_secret_access_key ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    aws configure set region us-east-1
    ./login_ecr.sh
```

---

Let me know if you'd like this styled for PDF export, embedded into your README, or versioned for onboarding tiers (e.g., intern vs executive). I can also help you scaffold `.env` support or add logging for audit traceability.

