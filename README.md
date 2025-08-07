## 💳 🚀 CI/CD Pipeline to Azure VM with Docker (GitLab CI/CD)
      This project uses GitLab CI/CD to automate deployment to an Azure Virtual Machine using Docker over SSH.

**FlexiPay** is a scalable and secure custom payment gateway built with **Node.js**, **Express**, and **MongoDB**. It acts as a central platform to process payments through various providers including **Stripe**, **PayPal**, and digital wallets. Designed with extensibility in mind, FlexiPay can be embedded in SaaS platforms or marketplaces using a platform-owned strategy, managing merchant payments and weekly balance distributions.

---

## 📁 .gitlab-ci.yml Overview
   The pipeline includes the following stages:
- build (optional): Build Docker image
- deploy: SSH into Azure VM and deploy Docker container

Sample .gitlab-ci.yml

```bash
   stages:
     - deploy
   
   deploy:
     stage: deploy
     image: docker:latest
     services:
       - docker:dind
     variables:
       DOCKER_HOST: tcp://docker:2375/
       DOCKER_TLS_CERTDIR: ""
     before_script:
       - apk add --no-cache openssh
       - echo "$SSH_PRIVATE_KEY" > id_rsa
       - chmod 600 id_rsa
     script:
       - ssh -o StrictHostKeyChecking=no -i id_rsa $SSH_USER@$SSH_HOST "
           docker pull $DOCKER_IMAGE &&
           docker stop $CONTAINER_NAME || true &&
           docker rm $CONTAINER_NAME || true &&
           docker run -d --name $CONTAINER_NAME $DOCKER_IMAGE
         "
```
---

## 🔐 Environment Variables (GitLab → Settings → CI/CD → Variables)
   - SSH_PRIVATE_KEY
   - SSH_USER
   - SSH_HOST
   - DOCKER_IMAGE
   - CONTAINER_NAME
---

## 💻 Preparing the Azure VM

1. Open port 22 in your VM’s Network Security Group (NSG)
2. Install Docker on the VM:

---

## 🚀 CI/CD with Azure DevOps + Azure
This project demonstrates a professional CI/CD pipeline using Azure DevOps to build a Docker image and deploy it to an Azure Virtual Machine, even in a restricted environment (e.g., CloudBox with limited Azure permissions).

---
## 🧱 CI/CD Pipeline Flow
```bash
1. Git push to main branch
2. Azure DevOps pipeline is triggered
3. Docker image is built on a hosted Azure agent
4. Image is pushed to Azure Container Registry (ACR)
5. Azure VM is accessed via SSH
6. The container is pulled and deployed from ACR
```

---
## ⚙️ Prerequisites
Before using this pipeline, ensure the following are available:

1. Azure DevOps project and repo
2. Azure Container Registry (ACR)
3. Azure Linux VM with Docker installed
4. SSH access via uploaded private key
5. Service connection manually created (if app registration is restricted)

---
## 🔐 Azure DevOps Setup
1. Add the required secrets (variables) and upload the private SSH key
2. Create the azure-pipelines.yml file or create one using the classic UI
3. once the pipeline is done, you can test the endpoints using the defined endpoint on azure for this VM

---

## 👥 Contributing

Pull requests are welcome! For major changes, open an issue first to discuss what you’d like to change or enhance.

---

## 📝 License

MIT © 2025 FlexiPay

```

Would you like me to generate an example `.env`, a Postman collection, or include Stripe setup notes in the README?
```
