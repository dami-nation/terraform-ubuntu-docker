# Terraform Ubuntu Docker Host

This repository contains a small Terraform setup to spin up a disposable Ubuntu EC2 instance with Docker installed on boot.

I use this kind of setup when I need a clean environment to test Docker images or validate something quickly without clicking through the AWS console or rebuilding the same instance over and over.

The thinking behind this setup, along with the decisions and tradeoffs, is written up here:  
https://oladami.com/posts/terraform-ubuntu/

This repo is the sanitized version of that setup.

---

## What this does

- Creates an Ubuntu EC2 instance
- Installs Docker automatically via `user_data`
- Installs `kubectl` and `kind` so the host can run a local Kubernetes cluster
- Exposes the instance with a public IP
- Is designed to be created and destroyed quickly

This is intentionally small. The goal is low friction.

---

## Usage

Inputs are documented in `terraform.tfvars.example`.

Create your own `terraform.tfvars` locally (do not commit it), then run:

```bash
terraform init
terraform plan
terraform apply
```
Once the instance is up, SSH into it and optionally create a Kubernetes cluster using Kind:

```bash
kind create cluster --name dev-cluster
kubectl get nodes
```
Destroy when you’re done:

```bash
terraform destroy
```

---

## Verification

After connecting to the instance you can verify the tooling is available:

```bash
docker ps
kind --version
kubectl version --client
```

If desired, create a test cluster:

```bash
kind create cluster
kubectl get nodes
```

## Notes

- No Terraform state or real variables are committed to this repo
- AWS credentials are expected to be provided via CLI profiles or environment variables
- This setup assumes a default VPC
- The Kubernetes cluster (if created) runs locally on the instance using Kind and Docker

