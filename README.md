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


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Active Directory & Hybrid Cloud Identity Management Lab

## Project Overview
This repository documents the deployment of a secure, isolated Active Directory (AD) environment hosted within a cloud infrastructure (AWS). The purpose of this lab was to simulate a real-world enterprise environment by building a central Domain Controller, configuring internal DNS routing across virtual machines, joining a client workstation to the domain, and leveraging PowerShell to automate employee identity onboarding.

## Key Technical Skills Demonstrated
* **Cloud Infrastructure:** Multi-instance deployment on AWS EC2, network interface mapping, and security group rule enforcement.
* **Core Infrastructure & Identity Services:** Active Directory Domain Services (AD DS) configuration, DNS server configuration, and Domain Join implementation.
* **Automation & DevOps:** Writing and executing structured PowerShell scripts using the `ActiveDirectory` module to process bulk inputs via CSV.
* **Identity & Access Management (IAM):** Enforcement of Least Privilege, Remote Desktop Group Scope delegation, and Helpdesk identity provisioning lifecycle.

---

## 🤖 AI-Assisted Engineering Disclosure
While I am currently building my native scripting proficiency, the PowerShell automation script used in this lab was developed using an AI collaborator (LLM) as a technical peer programmer. 

My engineering focus during this phase of the project was on:
1.  **Architecting the Logic:** Defining the input structure (CSV headers), identifying required data transformations, and mapping out the target Active Directory schema paths (OUs).
2.  **Code Auditing & Comprehension:** Breaking down the syntax to deeply understand structural concepts like `ForEach` iteration, string manipulation (`SubString`), secure string data masking, and parameter handling inside the AD module.
3.  **Debugging & Validation:** Managing administrative execution policies, reviewing console output, and verifying real-time database population inside Active Directory Users and Computers.

---

## Architecture & Lab Components
* **Domain Controller (`DC01`):** Windows Server 2022 instance running AD DS and central DNS.
    * **Domain Name:** `edwinlab.local`
    * **Internal Network Scope:** `172.31.x.x`
* **Client Workstation (`CLIENT01`):** Windows 10/11 Enterprise instance simulating an employee endpoint.

---

## Phases Completed

### Phase 1: Network Topology & DNS Alignment
1.  **Central Infrastructure Discovery:** Retrieved the internal IPv4 network address of `DC01` to establish it as the primary nameserver for the environment.
2.  **DNS Routing:** Manually configured `CLIENT01`'s network adapter TCP/IPv4 properties to point its **Preferred DNS Server** directly to the internal IP of `DC01`. 
3.  **Domain Consolidation:** Successfully executed a secure Domain Join on `CLIENT01`, transitioning the workstation from an isolated local workgroup to a subordinate member of the `EDWINLAB` forest.

### Phase 2: PowerShell Automation for Onboarding (Bulk User Provisioning)
Instead of using manual graphical management, I engineered an automated pipeline to handle workforce creation at scale.

1.  **Directory Architecture:** Structured a nested hierarchy inside Active Directory Users and Computers (ADUC) by spinning up an Organizational Unit (OU) path: `OU=IT,OU=Corporate-Users,DC=edwinlab,DC=local`.
2.  **Data Source Design:** Created a mock corporate spreadsheet (`employees.csv`) to mimic data passed down from an HR provisioning platform:
    ```csv
    FirstName,LastName,Department,Title
    John,Doe,IT,Cloud Engineer
    Jane,Smith,IT,Security Analyst
    Alex,Martinez,IT,Network Administrator
    ```
3.  **The Automation Code:** Written and executed via PowerShell ISE with administrator elevation:
    ```powershell
    # Import the Active Directory Module
    Import-Module ActiveDirectory

    # Read the employee list
    $employees = Import-Csv -Path "C:\employees.csv"

    # Get current domain configuration dynamically
    $domainDN = (Get-ADDomain).DistinguishedName
    $targetOU = "OU=IT,OU=Corporate-Users,$domainDN"

    # Default password for new hires (Encrypted securely as required by AD)
    $securePassword = ConvertTo-SecureString "Welcome2026!" -AsPlainText -Force

    foreach ($user in $employees) {
        # Generate username format (e.g., jdoe) and lower-case it
        $username = ($user.FirstName.SubString(0,1) + $user.LastName).ToLower()
        $userPrincipalName = "$username@" + (Get-ADDomain).DNSRoot
        
        # Check if user already exists to prevent duplication conflicts
        if (Get-ADUser -Filter "SamAccountName -eq '$username'") {
            Write-Host "User $username already exists!" -ForegroundColor Yellow
        } else {
            # Create the user in Active Directory with mapped CSV parameters
            New-ADUser -Name "$($user.FirstName) $($user.LastName)" `
                       -SamAccountName $username `
                       -UserPrincipalName $userPrincipalName `
                       -GivenName $user.FirstName `
                       -Surname $user.LastName `
                       -Title $user.Title `
                       -Department $user.Department `
                       -Path $targetOU `
                       -AccountPassword $securePassword `
                       -ChangePasswordAtLogon $true `
                       -Enabled $true
                       
            Write-Host "Successfully created user: $username ($($user.Title))" -ForegroundColor Green
        }
    }
    ```

### Phase 3: Access Control & Integration Testing
1.  **Identity Verification:** Verified that the script correctly parsed the schema, generated consistent usernames (`jdoe`), assigned titles/departments, and injected them safely into the target `IT` OU container.
2.  **Access Control Delegation:** Enforced standard hardening principles. Since Active Directory restricts standard employees from remote login capabilities by default, I assumed a Domain Admin role on `CLIENT01` and added the newly created account to the local machine's **Remote Desktop Users** group.
3.  **Authentication Flow Validation:** Reset the temporary password using standard IT lifecycle procedures and successfully initiated a clean domain user login session for the newly created employee profile on `CLIENT01`.

---
## Future Roadmap
* **Phase 4 (Next):** Implementing Group Policy Objects (GPO) to restrict client machine endpoint access (disabling CMD, locking Control Panel, pushing security banners).
* **Phase 5:** Setting up Event Log Auditing to monitor for failed brute-force RDP logons (SIEM/SOC practice).
