# CI/CD Pipeline for Flask App 🚀

## 📌 Overview
This project demonstrates a **CI/CD pipeline** using **GitHub Actions** that:
- Tests a Python Flask app with `pytest`
- Builds a Docker image
- Pushes the image to **Amazon ECR**
- Deploys the container on an **EC2 instance**
- Sends **email notifications** on success/failure

---

## 🛠 Prerequisites
- AWS ECR repository created
- EC2 instance (Amazon Linux/Ubuntu) with:
  - Docker installed
  - IAM role: `AmazonEC2ContainerRegistryReadOnly`
  - Security group allowing port `5000`
- GitHub Secrets configured:
  - `AWS_ACCOUNT_ID`, `AWS_REGION`, `ECR_REPO`
  - `EC2_HOST`, `EC2_USER`, `EC2_SSH_KEY`
  - `SMTP_SERVER`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS`, `NOTIFY_EMAIL`

---

## ⚙️ Pipeline Stages
1. **Checkout** → Pull source code  
2. **Install dependencies** → `pip install -r requirements.txt`  
3. **Test** → Run `pytest` (pipeline stops if tests fail)  
4. **Build** → Docker image tagged with commit SHA  
5. **Push to ECR** → Upload image  
6. **Deploy to EC2** → Replace running container, verify `/health` endpoint  
7. **Notify** → Email with build details  

---

## 🔑 Secrets Management
All sensitive values are stored in **GitHub Secrets**.  
Never commit credentials to the repository.

---

## 📬 Notifications
- **Success email** → Includes commit SHA, branch, image tag, EC2 target, pipeline link  
- **Failure email** → Includes failed stage, commit SHA, branch, pipeline logs link  

---

## ▶️ Manual Deployment
If CI/CD is unavailable:
```bash
docker pull <ECR_REPO>:<COMMIT_SHA>
docker stop flask-app || true
docker rm flask-app || true
docker run -d --name flask-app -p 5000:5000 <ECR_REPO>:<COMMIT_SHA>
