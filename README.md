# 🏗️ AWS 3-Tier Student Management App

This project is a hands-on implementation of a **3-tier architecture** using **Amazon Web Services (AWS)**. It demonstrates how to set up a **web-based Student Management System** using core cloud components including a web server (NGINX), application server (Apache Tomcat), and database server (MySQL).

---

## 📌 Project Architecture

**Three-tier Architecture Components:**

1. **Presentation Layer (Web Tier):**
   - NGINX server hosted on an EC2 instance
   - Handles incoming HTTP requests and forwards them to the application layer

2. **Application Layer (App Tier):**
   - Apache Tomcat on EC2
   - Processes logic and handles user requests

3. **Database Layer (DB Tier):**
   - MySQL database running on a private EC2 instance
   - Stores student data

---

## ☁️ AWS Services Used

- **EC2** – for hosting all three tiers
- **VPC** – with public and private subnets
- **Internet Gateway & Route Tables** – for internet access
- **Security Groups & NACLs** – for fine-grained access control
- **Elastic IP** – for static access to the web server
- **SSH** – for instance provisioning and setup

---

## 🔧 Project Setup Steps

1. **VPC Configuration**
   - Create a custom VPC with CIDR block
   - Configure public and private subnets
   - Set up internet gateway and routing tables

2. **Security Setup**
   - Define security groups for each layer:
     - Web: Allow HTTP/HTTPS and SSH
     - App: Allow traffic from Web tier only
     - DB: Allow traffic from App tier only

3. **Server Deployment**
   - Launch EC2 instances for each layer
   - Install necessary services:
     - NGINX on Web tier
     - Java & Tomcat on App tier
     - MySQL on DB tier

4. **App Configuration**
   - Deploy the Student Management App on Tomcat
   - Configure NGINX reverse proxy to point to the App tier
   - Connect Tomcat to MySQL with JDBC driver and correct credentials

---

## 📸 Screenshots

![Screenshot 2025-06-24 211640](https://github.com/user-attachments/assets/d94cac92-88f4-4c70-8184-ab031d30ad02)

![Screenshot 2025-06-24 223443](https://github.com/user-attachments/assets/13d50560-f3d6-425b-9b1e-6c5d3e24717b)
![Screenshot 2025-06-25 214316](https://github.com/user-attachments/assets/84f8a80d-898e-4056-a8c9-45d05b4bbdf7)
![Screenshot 2025-06-25 214415](https://github.com/user-attachments/assets/462bbdea-37fc-4883-ad9c-1aacb81d6151)


---

## 🧪 Known Issue

Currently, the application UI loads but the data is not being stored in the MySQL database. Possible causes:
- Incorrect JDBC connection string
- MySQL port not open to the App server
- DB credentials mismatch

> **To Fix:** Ensure security groups allow DB port (3306) from the App server and the JDBC config in `web.xml` is correct.

---

## 📚 Learning Outcomes

- How to architect and deploy a 3-tier application on AWS
- Hands-on experience with EC2, VPC, Subnets, and Security Groups
- Configuration of NGINX, Tomcat, and MySQL in a cloud environment
- Understanding of private and public IP, EIP, NAT vs IGW

---

## 🛠️ Tools & Technologies

`AWS EC2` `VPC` `NGINX` `Tomcat` `MySQL` `Linux` `Bash` `SSH` `Cloud Networking` `Web App Deployment`

---

## 🙋‍♀️ Author

**Tejaswini Shirke**  
GitHub: [Tejaswini2704](https://github.com/Tejaswini2704)

---
