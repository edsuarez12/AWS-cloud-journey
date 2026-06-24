# My AWS Cloud Learning Journey

Welcome to my portfolio! Here, I document the real-world cloud projects I am building as I study for my AWS certifications and prepare to transition into Cloud Engineering.

---

## Project 1: Cloud-Accelerated Static Portfolio Website
* **Live Demo:** [Click here to view my live site](https://d2k7l8ow1t19gk.cloudfront.net/)
* **AWS Services Used:** Amazon S3, Amazon CloudFront (CDN), AWS IAM (Origin Access Control)

### What I Built
I deployed a secure, globally distributed static portfolio website. Instead of running a traditional 24/7 virtual server, I used a modern serverless approach to host files instantly and cost-effectively.

### How It Works & Why I Used These Services
* **Amazon S3:** Used as object storage to hold my website files (`index.html`). I enabled Static Website Hosting so S3 could act as a basic web server without needing an operating system to manage.
* **Amazon CloudFront:** Connected to S3 to act as a Content Delivery Network (CDN). It automatically caches my website at global edge data centers closest to the user, massively speeding up load times. It also enforces HTTPS encryption for modern web security.

### Problem Solving & Key Takeaways
During deployment, I ran into a standard AWS `AccessDenied` XML error when hitting the CloudFront URL. To resolve this:
1. I learned that CloudFront requires explicit permission to pull files from a private S3 bucket.
2. I configured CloudFront's modern **Origin Access Control (OAC)** security setting.
3. I updated my **S3 Bucket Policy** with the generated JSON code to allow secure communication between the two services.



-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Project 2: Highly Available & Fault-Tolerant Web Server Fleet

### What I Built
I deployed a highly available, fault-tolerant web server architecture utilizing a fleet of Linux EC2 instances. The infrastructure dynamically scales based on incoming traffic and automatically balances users across multiple physical AWS data centers (Availability Zones).



### Architecture Components & Why I Used Them
* **Amazon EC2 (Elastic Compute Cloud):** Used to provision virtual Linux servers running Apache web server software to handle dynamic requests.
* **User Data Automation Scripting:** Utilized automated shell scripts upon instance boot to install packages, configure Apache, and deploy web code cleanly without manual host management.
* **Custom Amazon Machine Images (AMIs):** Created a master "golden image" configuration template of a healthy web server to allow instantaneous replication.
* **Application Load Balancer (ALB):** Acted as a single, internet-facing entry point to abstract the backend infrastructure and automatically distribute user traffic to healthy compute nodes.
* **Target Groups & Health Checks:** Configured active HTTP health checks to constantly poll instances on Port 80, automatically isolating any unhealthy nodes from receiving traffic.
* **Auto Scaling Groups (ASG):** Implemented automated scaling thresholds (Minimum: 2, Maximum: 4) to handle traffic elasticity, enforce high availability, and eliminate single points of failure.

### Core Engineering Takeaways
1. **Automation Over Manual Configuration:** Using Launch Templates paired with custom AMIs ensures that configuration drift is eliminated and infrastructure deployment is perfectly repeatable.
2. **Designing for Failure:** By stretching the Auto Scaling Group across multiple Availability Zones and leveraging ALB health checks, the application remains fully operational even if an entire physical AWS data center encounters an outage.
3. **Elasticity and Cloud Economics:** Setting dynamic scaling constraints ensures that resources are tightly optimized—scaling out only during high demand and scaling in during low traffic to prevent unexpected operational spend.
