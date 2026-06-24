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
