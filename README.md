# AWS E-Commerce Platform — AquaMart (Full-Stack)

A production-style e-commerce application deployed end-to-end on AWS.  
It includes a secure static frontend, backend API, database, custom domain, SSL, and complete cloud infrastructure following AWS best practices.


---

# 1. Project Overview

- **Frontend:** React app hosted on **Amazon S3**, delivered via **CloudFront** using **Origin Access Control (OAC)**.  
- **Backend:** Node.js & Express running on an **EC2 instance**, with IAM roles for secure DynamoDB access.  
- **Database:** DynamoDB tables for users, products, and orders.  
- **Domain & SSL:** Managed via **Route 53** and **AWS Certificate Manager (ACM)**.  
- **Security:** Private S3 bucket, IAM least-privilege roles, tight EC2 security groups.

---

# 2. AWS Architecture

User → CloudFront → S3 (Frontend) + EC2 API → DynamoDB

```
CloudFront (CDN + HTTPS)
          ↓
S3 Bucket (Frontend Hosting)
          ↓
User ←──────→ EC2 (Node.js API) ←→ DynamoDB
```

*(Architecture diagram is included in screenshots)*

---

# 3. Frontend (S3 + CloudFront + OAC + HTTPS)

##  Deployment Steps
1. Build the React app:
   ```bash
   cd frontend
   npm install
   npm run build
   ```
2. Create a private S3 bucket (block all public access).  
3. Upload the React build folder to S3.  
4. Create a CloudFront distribution:
   - Origin: S3 bucket  
   - Enable **Origin Access Control (OAC)**  
   - Default root: `index.html`  
5. Request an HTTPS certificate via ACM (us-east-1).  
6. Attach certificate to CloudFront.  
7. Invalidate CloudFront cache on updates.


---

# 4. Domain & DNS (Route 53)

##  Steps
1. Create Hosted Zone in Route 53.  
2. Map domain using **A (Alias) record → CloudFront**.  
3. Add ACM validation CNAME automatically.  
4. Wait for propagation until certificate = **Issued**.

 

---

# 5. Backend API (EC2 + IAM Role + Security Groups)

##  Steps
1. Launch EC2 Ubuntu instance.  
2. Attach an **IAM Role** with DynamoDB access policy.  
3. Configure Security Group:
   - **22** → SSH  
   - **80** → HTTP  
   - **443** → HTTPS  
   - **5000** → API port  
4. Install Node.js and PM2:
   ```bash
   sudo apt update
   sudo apt install nodejs npm -y
   sudo npm install -g pm2
   ```
5. Download backend code:
   ```bash
   git clone https://github.com/Pakthapriyan/Aquamart-Website
   cd backend
   npm install
   ```
6. Start server:
   ```bash
   pm2 start npm -- start
   pm2 save
   ```


---

# 6. Database (DynamoDB)

You created 3 tables:

| Table     | Partition Key |
|-----------|----------------|
| Users     | userId         |
| Products  | productId      |
| Orders    | orderId        |

- Fully managed  
- Auto-scaling  
- No servers needed  

---

# 7. Project Structure

```
project/
   frontend/
      src/
      public/
      package.json

   backend/
      src/
      routes/
      package.json

   README.md
```

---

# 8. Running Locally

##  Frontend
```bash
cd frontend
npm install
npm run dev
```

##  Backend
```bash
cd backend
npm install
npm start
```

---

# 9. Future Improvements

- Migrate backend to **ECS Fargate** (serverless containers)  
- Or move to **Lambda + API Gateway (Fully serverless)**  
- Add **Cognito authentication**  
- Add **CloudWatch dashboards + alarms**  
- Add CI/CD via **CodePipeline + CodeBuild**  
- Add AWS WAF for edge security  

---
