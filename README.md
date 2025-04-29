# AWS Cloud Resume Challenge

Welcome to my Cloud Resume Challenge project! This project demonstrates my ability to design, build, secure, and deploy a cloud-native serverless web application using AWS services and built my site https://george-gian.com.

Motivation: I undertook this project to deepen my understanding of AWS and put my knowledge into practice. As someone preparing for AWS certification, I wanted hands-on experience with real services. Building my resume in the cloud was the perfect way to learn by doing.  The challenge pushed me to solve problems independently and taught me how various AWS services integrate. My goal was to create a professional portfolio site that also proves my cloud skills to potential employers, and this project delivered exactly that.

## 🛠 Services Used
- **Amazon S3**: Hosts the static website content (HTML/CSS/JS) for my resume. The S3 bucket is configured for static site hosting and acts as the origin for CloudFront.
- **Amazon CloudFront**: Distributes the website globally via a CDN, reducing latency for users. CloudFront is configured with an Origin Access Identity to securely fetch content from the private S3 bucket, and it uses an AWS Certificate Manager certificate for HTTPS on my custom domain.
- **AWS Certificate Manager**: Issued an SSL/TLS certificate for my domain and its subdomain. This certificate is attached to the CloudFront distribution so that my site is accessible securely via HTTPS.
- **Amazon Route 53**: Manages the DNS for my custom domain (e.g., myresume.com). Route 53 provides an Alias A record to connect my domain and subdomain to the CloudFront distribution. I also enabled DNSSEC on the hosted zone for this domain to ensure DNS queries are authenticated.
- **AWS Lambda**: Contains the back-end application logic. I wrote an AWS Lambda function (in Python) that increments and retrieves a visitor count in the database. This function is invoked by the API Gateway in response to HTTP requests from the website.
- **Amazon DynamoDB**: Serves as the persistent data store for the project. I set up a DynamoDB table to keep track of the visitor counter (with a simple key-value item storing the current view count). DynamoDB’s low-latency and scalability make it ideal for this serverless use case.
- **(Optional) GitHub Actions**: Although not an AWS service, I used GitHub Actions CI/CD to automate deployments. Whenever I push updates to the website code, an Actions workflow synchronizes the S3 bucket with the new content. This ensures that any changes to my resume (HTML/CSS or Lambda code) get deployed seamlessly.

**Technical Challenge - Custom Domain on CloudFront**: One challenge I faced was configuring my custom domain name for the CloudFront distribution. Initially, after purchasing the domain and setting up Route 53, I created an alias record pointing my domain to the CloudFront distribution. However, I kept getting an “DNS address could not be found” error when trying to access the site. I discovered that the issue was due to **CloudFront’s Alternate Domain Name (CNAME) settings** – I had not added my domain there. To resolve this, I updated the CloudFront distribution:
- In CloudFront’s settings, I added george-gian.com and www.george-gian.com as Alternate Domain Names for the distribution and associated my ACM certificate with the distribution.
- After saving that, I confirmed that the Route 53 A records (aliases) for the root and www subdomain were pointing to the CloudFront distribution. (Route 53 can alias to CloudFront distributions, but the distribution must be configured to recognize the domain name.)
- Once the distribution settings were in place and propagated, the custom domain started working over HTTPS.

**Solution**: This experience taught me the importance of configuring both sides of the equation: the DNS (Route 53) and the CloudFront distribution. CloudFront needs to be explicitly told about any custom domain names through the Alternate Domain Names field, otherwise it will not serve traffic for that host header. The fix was successful – I can now access my resume site at **https://george-gian.com** and **https://www.george-gian.com** confidently, and the CloudFront distribution serves it with the correct certificate.

## 🔥 Project Highlights
- Built a fully serverless resume website distributed globally via CloudFront.
- Purchased and configured a custom domain using Route 53.
- Implemented HTTPS using ACM certificates attached to CloudFront.
- **Enabled DNSSEC** to secure domain name resolution.
- Wrote a **Lambda function** to count page visits and store them in **DynamoDB**.
- Troubleshot and resolved custom domain alias issues with CloudFront Alternate Domain Names (CNAMEs).
- Verified end-to-end security with DNSSEC Debugger by Verisign Labs.

**Security Enhancements – DNSSEC and HTTPS**: In addition to basic HTTPS, I wanted to follow best practices to secure my project’s DNS. After getting the site working, I enabled DNSSEC signing on my Route 53 hosted zone. DNSSEC adds a layer of authentication to DNS lookups by using digital signatures. Enabling DNSSEC ensures that DNS resolvers can verify the responses for george-gian.com actually come from Route 53 and haven’t been tampered with​ 
[docs.aws.amazon.com](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-configuring-dnssec.html#:~:text=Domain%20Name%20System%20Security%20Extensions,and%20has%20not%20been%20tampered). Setting it up in Route 53 involved creating a key-signing key (KSK) via AWS KMS and publishing a DS record in the parent domain (which AWS handled since my domain is registered with Route 53). I used the DNSSEC Debugger tool from Verisign Labs to double-check everything was configured correctly – this tool validates the entire “chain of trust” for a DNSSEC-enabled domain and highlights any problems. The debugger showed that my domain’s DNSSEC chain was intact, meaning my DNS records are now verifiable end-to-end.
https://dnssec-debugger.verisignlabs.com/george-gian.com

![DNSSEC](/img/dnssec.jpg)

On the web side, all traffic to my resume site is encrypted with HTTPS. I enforced HTTP to HTTPS redirection in CloudFront to ensure no one can access the site over an insecure connection. Between TLS encryption and DNSSEC, I’m confident that the site follows security best practices for a personal website.

## 🌍 Architecture Overview
The resume website follows a classic serverless architecture with a static front end and a dynamic back end for tracking visitors. The diagram below illustrates the high-level design.
*Architecture diagram of the AWS Cloud Resume Challenge project. The frontend is a static website stored in S3 and distributed via CloudFront (using an ACM TLS certificate for HTTPS and Amazon Route 53 for custom DNS). The backend is powered by  AWS Lambda, with data stored in DynamoDB. A CI/CD pipeline (e.g., GitHub Actions) automatically deploys website updates to S3, ensuring smooth and continuous delivery.*
![Cloud Resume Challenge Architecture](/img/diagram.jpg)

## 📈 Visitor Counter
JavaScript embedded on the frontend makes a secure call to an AWS Lambda function to update and retrieve visitor counts, which are persisted in DynamoDB.

## 🚀 Future Improvements
- Implement full Infrastructure as Code using Terraform or AWS CDK.
- Set up CloudWatch logging and alarms for Lambda errors.
- Expand CI/CD workflows using GitHub Actions for automated deployments.
- Enhance Lambda security with signed URLs or additional API Gateway layer if scaling up.

## 🎯 Goals
This project has deepened my AWS knowledge and is helping me prepare for the **AWS Certified Solutions Architect** exam. It also demonstrates my focus on **cloud security best practices**.
I didn't use AWS WAF (Web Application Firewall), AWS Shield and AWS GuardDuty as would surpass my budget limit for this project.
---

> If you have feedback, ideas, or opportunities you'd like to discuss, feel free to connect!
