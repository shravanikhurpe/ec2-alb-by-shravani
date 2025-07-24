# 📖 Public Application Load Balancer with EC2 Web Servers

This guide walks you through deploying a public **Application Load Balancer (ALB)** in AWS with **3 EC2 instances** running a simple Apache web server. The ALB will distribute traffic to these instances.

---

## 📋 Prerequisites

* AWS Account
* AWS EC2 Key Pair (we'll create `webserver.pem`)
* Basic AWS Console access

---

## 🚀 Project Steps

### 1️⃣ Launch 3 EC2 Instances

* **Instance Type:** `t2.micro`
* **Key Pair:** Create a new key pair named `webserver.pem`
* **Security Group:**
  Allow the following:

  * SSH (22)
  * HTTP (80)
  * HTTPS (443)

---

### 2️⃣ Add User Data (Startup Script)

While launching instances, in the **Advanced Details** section, paste the following **User Data** script:

```bash
#!/bin/bash
yum update -y
yum install httpd -y
systemctl start httpd
systemctl enable httpd
usermod -a -G apache ec2-user
chmod 755 /var/www/html
cd /var/www/html
echo "<h1>hello from $(hostname -f) webserver</h1>" > /var/www/html/index.html
```

This will install Apache, start the webserver, and set a custom welcome page showing the instance hostname.

---

### 3️⃣ Create an Application Load Balancer

* Go to **EC2 Console** → **Load Balancers**
* Click **Create Load Balancer**
* Select **Application Load Balancer**
* Name it `my-load-balancer`
* Scheme: `internet-facing`
* Listener: HTTP (80)
* Choose the same **Security Group** you created earlier

---

### 4️⃣ Create a Target Group

* Go to **Target Groups** in EC2 Console
* Click **Create Target Group**
* Type: `Instances`
* Protocol: HTTP (80)
* Health Check Path: `/index.html`
* Register the **3 EC2 instances** launched earlier

---

### 5️⃣ Attach Target Group to Load Balancer

* Go back to your **Load Balancer**
* In the **Listeners** section → **View/Edit rules**
* Forward requests to the **target group** you just created

---

### 6️⃣ Test the Setup

* Copy the **DNS name** of the Load Balancer (from Load Balancer details)
* Open it in your browser:
  `http://<load-balancer-dns-name>`

You should see a message like:
`hello from ip-172-31-XX-XX webserver`

---

## 🧹 Clean Up

After testing, delete the following resources to avoid charges:

1. **Deregister instances** from the target group
2. **Delete the target group**
3. **Delete the Load Balancer**
4. **Terminate the EC2 instances**


---

## 📑 Summary

| Resource      | Configuration                         |
| :------------ | :------------------------------------ |
| EC2 Instances | 3 × t2.micro, Apache installed        |
| Load Balancer | Public Application Load Balancer      |
| Target Group  | HTTP (80), Health Check `/index.html` |




