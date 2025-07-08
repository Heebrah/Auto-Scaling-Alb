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


## Project on Auto Scaling with ALB
1. Firstly go to the EC2 dashboard and then go to Launch Template
![caption](/img/1.ec2-dashboard.jpg)

2. then create Launch template 
![caption](/img/2.create-launch-template.jpg)

3. Creating Launch template by selecting the AMI
![caption](/img/3.create-launch-interface.jpg)

4. select the instance type
![caption](/img/4.launch-interface.jpg)

5. you can add a user data
![caption](/img/5.launch-user-data.jpg)

6. A successfkul launch instance will display
![caption](/img/6.view-launch-page.jpg)

7. After we move to auto-scaling group, then create auto scale

![caption](/img/7.auto-scaling-group.jpg)

8. Name the group and select your created launch template 
![caption](/img/8.select-launch-template.jpg)

9. Move to network section. Select your vpc and also availability zones
![caption](/img/9.network-setup.jpg)

10. You can created a new load balancer if there's none existing b4 
![caption](/img/12.create-load-balancer.jpg)

11. Then save ur auto scale group
![caption](/img/10.next-review.jpg)

This will create a running instance on ec2 instance
![caption](/img/13.success.jpg)


