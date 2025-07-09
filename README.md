### 🔹Auto Scaling with Application Load Balancer (ALB) Using Launch Template

**Auto Scaling** and **ALB** are used together in AWS to automatically manage traffic and ensure availability of EC2 instances. A **Launch Template** defines how new instances should be created when scaling.

---

### ✅ Key Components

1. **Launch Template**

   * Defines instance configuration (AMI, instance type, key pair, user data, etc.)
   * Reusable when launching EC2 instances in Auto Scaling Groups.

2. **Application Load Balancer (ALB)**

   * Distributes incoming traffic across healthy EC2 instances.
   * Works at the **application layer (Layer 7)** and supports path-based routing.

3. **Auto Scaling Group (ASG)**

   * Automatically adds/removes EC2 instances based on demand.
   * Uses the Launch Template to create new instances.
   * Registers instances with the ALB target group.

---

### 🔧 Workflow Overview

1. **Create Launch Template**

   * Includes AMI ID, instance type, and security group.
   * Optionally includes user data (e.g., install a web server).

2. **Create Target Group**

   * ALB uses this group to register and route traffic to EC2 instances.
   * Health checks are configured here.

3. **Create Application Load Balancer**

   * Attach listeners (e.g., HTTP on port 80).
   * Forward traffic to the target group.

4. **Create Auto Scaling Group**

   * Attach the launch template and ALB target group.
   * Set minimum, maximum, and desired capacity.
   * Add scaling policies (e.g., based on CPU usage).

---

### 📈 Example Scenario

* Launch template installs a web app.
* ALB distributes traffic to EC2s in multiple AZs.
* Auto Scaling adds instances during high traffic and removes them during low traffic.

---

### ✅ Benefits

* **High availability**: Traffic is balanced across instances.
* **Fault tolerance**: Unhealthy instances are automatically replaced.
* **Cost-efficient**: Resources scale with demand.
* **Resilience**: Works across multiple Availability Zones.

## Using CLI to create a Load balancer
Here’s a **complete AWS CLI workflow** to set up **Auto Scaling with an Application Load Balancer (ALB)** using a **Launch Template**.

---

## ✅ Prerequisites

Ensure you have:

* A **VPC** with **public subnets**
* A **Security Group** that allows SSH and HTTP
* An **AMI ID** (e.g., Ubuntu or Amazon Linux)
* IAM permissions for EC2, Auto Scaling, ELB

---

## 🛠️ Step-by-Step AWS CLI Setup

---

### 🔹 Step 1: Create a Launch Template

```bash
aws ec2 create-launch-template \
  --launch-template-name my-launch-template \
  --version-description "v1" \
  --launch-template-data '{
    "ImageId": "ami-xxxxxxxxxxxxxxxxx",
    "InstanceType": "t2.micro",
    "SecurityGroupIds": ["sg-xxxxxxxxxxxxxxxxx"],
    "KeyName": "my-key",
    "UserData": "'"$(base64 -w 0 <<EOF
#!/bin/bash
apt update -y
apt install apache2 -y
systemctl start apache2
EOF
)"'"
}'
```

> 🔸 Replace `ami-xxxxxxxx`, `sg-xxxxxxxx`, and `my-key` with your values.

---

### 🔹 Step 2: Create a Target Group

```bash
aws elbv2 create-target-group \
  --name my-targets \
  --protocol HTTP \
  --port 80 \
  --vpc-id vpc-xxxxxxxx \
  --target-type instance
```

---

### 🔹 Step 3: Create an Application Load Balancer (ALB)

```bash
aws elbv2 create-load-balancer \
  --name my-alb \
  --subnets subnet-aaaa subnet-bbbb \
  --security-groups sg-xxxxxxxx \
  --scheme internet-facing \
  --type application \
  --ip-address-type ipv4
```

---

### 🔹 Step 4: Create a Listener for the ALB

```bash
aws elbv2 create-listener \
  --load-balancer-arn arn:aws:elasticloadbalancing:region:acct-id:loadbalancer/app/my-alb/xxxx \
  --protocol HTTP \
  --port 80 \
  --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:region:acct-id:targetgroup/my-targets/xxxx
```

---

### 🔹 Step 5: Create Auto Scaling Group

```bash
aws autoscaling create-auto-scaling-group \
  --auto-scaling-group-name my-asg \
  --launch-template "LaunchTemplateName=my-launch-template,Version=1" \
  --min-size 2 \
  --max-size 5 \
  --desired-capacity 2 \
  --vpc-zone-identifier "subnet-aaaa,subnet-bbbb" \
  --target-group-arns arn:aws:elasticloadbalancing:region:acct-id:targetgroup/my-targets/xxxx \
  --health-check-type ELB \
  --health-check-grace-period 300
```

---

### 🔹 Step 6: Create Scaling Policies (Optional)

```bash
aws autoscaling put-scaling-policy \
  --auto-scaling-group-name my-asg \
  --policy-name cpu-scale-out \
  --policy-type TargetTrackingScaling \
  --target-tracking-configuration file://tracking-policy.json
```

📄 `tracking-policy.json` example:

```json
{
  "PredefinedMetricSpecification": {
    "PredefinedMetricType": "ASGAverageCPUUtilization"
  },
  "TargetValue": 50.0
}
```

---

## ✅ Done!

Your setup now includes:

* Launch Template to define EC2
* Auto Scaling Group with min/max capacity
* Load Balancer for traffic distribution
* Target Group to route to EC2s
* Health check + scaling policy (optional)



## Project on Auto Scaling with ALB
***For us to begin this we need to understand the concept of Vpc which i have done in the previous project. link to the [click-here](http://github.com/heebrah/VPC).
After we have created our vpc and subnet. We can now proceed***

1. We will move to the Ec2 dashboard under **network** we will locate the **target group** and click **Create target group** 
![caption](/img/1.target-group.jpg)

2. in the options select **instance** then name your **target group** then select the **VPC** you created leave others in default select. then click **Create target group**
![caption](/img/2.target-option.jpg)

3. Here it's prompting for adding instance we can skip this by clicking create button
![caption](/img/3.create-target-group.jpg)

4. We can observe there no load balancer attached to it yet. 
![caption](/img/4.no-load-balancer.jpg)

5. Go to **Load Balancer** under **Load balancing** and click **create Load balancer**
![caption](/img/5.create-load-balancer.jpg)

6. Select **Application Load balancer** which represent Ec2 instance feature other like network related.
![caption](/img/6.create-Alb.jpg)

7. name your Load balancer then under Scheme select **internet-facing** next select **Ipv4** then under Vpc select our created **VPC** 
![caption](/img/7.Load-balance-option.jpg)

8. We can select our subnet then under security group we will select group we created that is aligned to the our **VPC** you can refer to the link above to see how security group are created and link to **VPC** then under **Listener and routing** we will select the **target group** we created at the beginning
![caption](/img/8.load-balance-option2.jpg)

9. Then click creat **Load balancer**
![caption](/img/9.create-load-balancer.jpg)

10. we got a success response creating the load balancer
![caption](/img/10.success-lb-page.jpg)

11. the state will be in provisioning we will wait for a while to turn to verified
![caption](/img/11.provisioning-load-balancer.jpg)

12. Now we move to **Launch template** under **Instance** and click **create Launch template**
![caption](/img/13.create-launch-template.jpg)

13. Firstly we name the launch template then under **Amazon Maching Image** we can select any OS image we prefer for my case i choose **Ubuntu** and choose the free tier then the **Instance type** to be a free tier eligible one
![caption](/img/14.launch-template.jpg)

14. key pair if we don't have a saved key pair we can create one by clicking **Create new key pair**. We can select the security Group we want by making sure it's the one that has access to **ssh** and **http** so that we can be able to display our instances. Under **Advance network configuration** we can choose **Auto Assign Public ip** to be **Enable**
 ![caption](/img/15.launch-template2.jpg)

 15. Under the Advance section at the bottom we can select the any user data we want like below i did select an apache web server to download and the user data i used is
 ```
 #!/bin/bash
apt update -y
apt install apache2 -y
systemctl enable apache2
systemctl start apache2

# Example config change or content update
echo "<h1>Updated Apache Content</h1>" > /var/www/html/index.html

# Reload Apache to apply changes
systemctl reload apache2

 ```
 ![caption](/img/16.advance-user-data.jpg)

 16. Now we move to **Auto scaling Groups** under Auto Scaling and click Create
![caption](/img/12.created-auto-scaling.jpg)

 17. We will name the **Auto Scaling group** and select the **Launch template** then next 
 ![caption](/img/17.Auto-scale-group.jpg)

18. Select our created **VPC** then select the subnet under it. Leave other as default ie **Balance best effort** then next
![caption](/img/18.asg-network.jpg)

19. Under Load balancing choose the **Attach existing Load balancer** this is because we have already created our Load balancer if not we can select create new. Then choose from your Load balancer 
![caption](/img/19.set-alb-asg.jpg)


20. scroll down and select **Elastic Load balancing Health check** this will give us the status of instance if its still up and running ![caption](/img/20.set-alb-asg-create.jpg)

21. We can select our desired capacity to be 1 or more in my case i select it to be 2 and minimum to 1 and maximum to be 3. Then no Scaling policy
![caption](/img/21.asg-policy.jpg)

22. click next ![caption](/img/22.asg-policy2.jpg)


23. skip and move to clicking **Create Auto Scale group** ![caption](/img/23.create-auto-scaling.jpg)

24. Going to the instance we can see the newly created instances which will be 2 as we state our desired capacity to be 2
![caption](/img/24a.we-can-see-the-instance-running.jpg)

25. We will move to the **Load Balancer** and copy the dns name so that we can display our user data input ![caption](/img/24b.copy-the-dns&past.jpg)


26. ![caption](/img/25.success-running-apache.jpg)