# Cloud Portfolio Website & CI/CD Pipeline

A fully automated, production‑grade cloud portfolio website deployed using a CI/CD pipeline built with GitHub Actions, Amazon S3, and CloudFront. The site is globally distributed, secured with HTTPS, and updates automatically on every push to the `main` branch.

---

## 🚀 Project Overview

This project hosts my personal cloud portfolio website using a modern, automated deployment workflow.  
The site is:

- **Version‑controlled** in GitHub  
- **Automatically deployed** using GitHub Actions  
- **Hosted** on Amazon S3  
- **Delivered globally** via CloudFront  
- **Secured** with an ACM‑issued HTTPS certificate  
- **Mapped to a custom domain** managed in Route 53  

This setup mirrors real‑world DevOps and cloud engineering practices.

---

## 🧱 Architecture

**High‑level flow:**

1. I update my website locally (HTML/CSS).
2. I commit and push changes to GitHub.
3. GitHub Actions:
   - Configures AWS credentials  
   - Syncs the updated files to S3  
   - Invalidates CloudFront cache  
4. CloudFront instantly serves the new version worldwide.

**AWS Services Used:**

- **Amazon S3** — static website hosting  
- **Amazon CloudFront** — CDN + HTTPS  
- **AWS Certificate Manager (ACM)** — SSL/TLS certificate  
- **AWS IAM** — secure programmatic access  
- **Amazon Route 53** — DNS + custom domain  
- **GitHub Actions** — CI/CD automation  

---

## 🛠️ CI/CD Pipeline Details

The deployment workflow runs automatically on every push to the `main` branch.

### **Pipeline Steps**

1. **Checkout repository**  
2. **Configure AWS credentials** using GitHub Secrets  
3. **Sync files to S3**  
4. **Invalidate CloudFront cache** so updates appear instantly  

### **Workflow File (`deploy.yml`)**

```yaml
name: Deploy to S3 and CloudFront

on:
  push:
    branches: [ "main" ]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ${{ secrets.AWS_REGION }}

    - name: Sync files to S3
      run: |
        aws s3 sync . s3://${{ secrets.S3_BUCKET }} --delete --exclude ".git/*" --exclude ".github/*"

    - name: Invalidate CloudFront cache
      run: |
        aws cloudfront create-invalidation \
          --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }} \
          --paths "/*"
