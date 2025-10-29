# 🔐 AWS ECR Login & Credential Validation

This guide documents the credential validation and Docker login flow for AWS Elastic Container Registry (ECR), integrated into both the Makefile and CLI scripts for modular reuse.

---

## 🧪 Credential Validation Flow

Before attempting any AWS CLI or Docker operations, validate credentials using:

```bash
aws sts get-caller-identity
