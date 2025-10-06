---
title: "Proposal"
date: "2025-10-02"
draft: false
pre: " <b> 1. </b> "
---

## Unified Web Platform for Suspicious File and URL Analysis

### 1. Executive Summary
The Malware Analysis & Threat Intelligence Platform was developed for a research group at a university in Ho Chi Minh City, with the goal of enhancing malware detection and sharing cyber threat intelligence. The platform allows users to upload suspicious files, domains, IP addresses, and URLs for automated analysis. Inspired by VirusTotal, the system integrates multiple scanning tools and open-source intelligence (OSINT) feeds, but is deployed at a smaller scale to better suit academic and research purposes.  

The system is designed to support 10–15 regular researchers, providing real-time detection, result sharing, and secure access with role-based permissions.

### 2. Problem Statement
#### The Challenge
Traditional malware analysis methods often rely on separate tools, require manual effort, and lack centralized result aggregation. Meanwhile, commercial services such as VirusTotal, although powerful, face limitations related to privacy, restricted access, or high cost. This creates obstacles for students and researchers who need a controlled environment to conduct experiments and study cybersecurity.  

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

#### Benefits & ROI
The platform serves as a **practical tool for cybersecurity education and research**, enabling students and researchers to experience real-world malware detection workflows while broadening their understanding of threat intelligence. By consolidating multiple tools into a single system, the platform minimizes manual steps and improves both accuracy and reliability.  

**Key Benefits:**  
- Provides hands-on learning opportunities for students and researchers.  
- Accelerates detection with real-time automated scanning.  
- Lays the groundwork for future integration of AI-driven detection models.  
- Supports safe and anonymized report sharing to strengthen collaboration.  

By streamlining processes and unifying data, the platform is expected to achieve ROI within 6–12 months while delivering long-term value to both research and education.  

### 3. Solution Architecture

<div style="display: flex; justify-content: center;">
  <iframe src="/high-level-view-2.drawio.html?v=1" width="900" height="300" style="border:none;"></iframe>
</div>

<div style="display: flex; justify-content: center; margin-top:20px;">
  <iframe src="/high-level-view.drawio.html" width="800" height="1100" style="border:none;"></iframe>
</div>

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

**Project Plan:**  
- **Pre-internship (Month 0):** 1 month for planning and assessing the existing infrastructure.  
- **During internship (Months 1–3):** 3 months in total.  
  - Month 1: Research AWS and upgrade hardware.  
  - Month 2: Design and refine the system architecture.  
  - Month 3: Deploy, test, and launch the system.  
- **Post-launch:** Continue research and iterative improvements over the next 12 months.  

### 6. Budget Estimation
