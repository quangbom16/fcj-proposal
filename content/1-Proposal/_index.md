---
title: "Proposal"
date: "2025-10-02"
draft: false
pre: " <b> 1. </b> "
---

## Multi-Platform File Analysis System - VirusTotal Clone

### 1. Project Summary
This project aims to develop and deploy a multi-platform file analysis system, operating similarly to VirusTotal. The system will allow users to upload suspicious files for scanning and analysis by multiple antivirus engines and various other analysis services. The primary goal is to provide a powerful, user-friendly tool for detecting and assessing potential file-based threats, thereby enhancing cybersecurity.

The system will be entirely built and deployed on the Amazon Web Services (AWS) cloud platform, leveraging AWS's leading managed services, scalability, and security features to ensure high performance, reliability, and availability.

### 2. Problem Statement
#### The Challenge
In an increasingly sophisticated and prevalent cybersecurity threat landscape, the demand for effective file analysis tools is paramount. VirusTotal has proven its value as a critical community service, helping users and cybersecurity professionals quickly identify malicious files. This project is initiated with the desire to create a similar, customizable, and extensible solution, serving research, educational, or specific application purposes.

#### The Solution
This platform provides a centralized web interface where users can:
- Upload suspicious files (executables, scripts, office documents).  
- Submit domains, IP addresses, or URLs for analysis.  
- Automatically scan data with multiple antivirus engines, sandbox environments, and OSINT sources.  
- Correlate results with threat intelligence feeds to improve detection accuracy.  
- Share anonymized reports with the research community to foster collaboration.  

Analysis requests are processed in two different ways:  

1. **Fast Query (Query Service):**  
   - If the file/URL has already been analyzed, the Web Server sends its hash to the Query Service.  
   - The Query Service checks DynamoDB for existing results.  
   - If found, results are instantly returned to the user via the web interface.  

2. **New Processing (Processing Service):**  
   - If the data is not yet available, the system forwards the raw file/URL to the Processing Service.  
   - The Processing Service performs in-depth analysis, scanning, and report generation.  
   - Results are sent back to the Web Server for user delivery and simultaneously stored in DynamoDB for future queries.  

This hybrid approach reduces response times for repeated requests while ensuring scalability for new analyses.  

#### Objectives
- Develop Core Functionality: Build a user interface (UI) that allows file uploads, a query service to check for existing analysis results, and a processing service to perform new file analyses.
- Multi-Engine Integration: Enable integration with various scanning and analysis tools (e.g., APIs of antivirus engines, static/dynamic analysis sandboxes).
- AWS Deployment: Design and deploy the system architecture on AWS to ensure high availability, flexible scalability, and robust security.
- Cost Optimization: Build a cost-effective architecture, leveraging appropriate AWS services for the project's scale.
- Establish CI/CD Pipeline: Set up a Continuous Integration/Continuous Delivery (CI/CD) pipeline to automate application development and deployment.

### 3. Solution Architecture

#### Summary

![High Level View 2](/images/high-level-view-2.drawio.png)


  1. User sends analysis request for file.
  2. Web Server sends request to Query Service. (Query using file hash)
  3. Query Service communicates with Database.
  4. Database responds to Query Service.
  5. Query Service responds to Web Server.
  6. If query successful send HTML response to User.
  7. If not then send file to Processing Service. (Web Server should hold raw file data)
  8. Processing Service sends report back to Web Server.
  9. Also stores report in Database.
  10. Database syncs.

#### Diagram for AWS

![High Level View](/images/high-level-view.drawio.png)

- **Web App Layer (UI):**  
  Users access the system through the ALB, which distributes requests to EC2 instances in the Auto Scaling Group. This layer handles the interface and request intake.  

- **Services Layer:**  
  Includes the **Query Service** and **Processing Service**, both deployed in Auto Scaling Groups within private subnets.  
  - **Query Service:** Connects to DynamoDB to handle hash-based queries.  
  - **Processing Service:** Receives raw data, performs malware analysis, generates reports, and stores results in DynamoDB.  

- **Data Layer:**  
  **Amazon DynamoDB** stores two categories of data:  
  - **View:** Analysis results ready for user consumption.  
  - **Event Store:** Logs and raw data for deeper investigations.  

- **Scalability:**  
  Both the Web App and Services layers use Auto Scaling Groups to ensure the system can handle high request volumes without compromising performance.  

#### AWS Services Used
The system leverages the following key AWS services:  

- **Amazon VPC (Virtual Private Cloud):** Creates an isolated virtual network, divided into multiple subnets (10.0.100.0/24, 10.0.101.0/24, 10.0.102.0/24, 10.0.103.0/24) across Availability Zones for high availability.  
- **Elastic Load Balancer (ALB):** Distributes incoming traffic to Web App (UI) instances running on EC2.  
- **Amazon EC2:** Hosts the Web App and backend services.  
- **Auto Scaling Group:** Dynamically scales the number of EC2 instances to maintain performance and cost efficiency.  
- **Amazon DynamoDB:** A NoSQL database that stores analysis reports, query results, and service synchronization data.  

### 5. Roadmap & Development Milestones

#### Project Plan:
- **During internship (Months 1–3):** 3 months in total.  
  - Month 1: Research AWS and upgrade hardware.  
  - Month 2: Design and refine the system architecture.  
  - Month 3: Deploy, test, and launch the system.  

### 6. Budget Estimation
#### Infrastructure Costs
- AWS Services:
    - EC2 instances:	6 × t4g.nano (6 × 0.0042 × 720h): $18.14
    - EBS	6 × 20 GB  = 120 GB × $0.10/GB:	$12.00
    - Load Balancer (ALB)	1 ALB, light traffic:	$15.00
    - NAT instance	1 × t4g.nano (replacement NAT Gateway):	$3.02
    - DynamoDB	10 GB, small on-demand:	$5.00
    - Data transfer OUT	100 GB × $0.09/GB:	$9.00
    - CloudWatch & misc	Logs + basicmetrics:	$3.00

Total		≈ $69.14 / month

