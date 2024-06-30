## 1. GCC PIM Vault Failure

### 1.1. Situation

- Tenants of the GCC PIM service were reporting inability to login to the system
- Investigation revealed that the Vault has crashed

### 1.2. Task

- Objective was to rescue existing Vault service
- Vault has an embedded database that requires L3 support expertise to troubleshoot
- Troubleshooting the embedded database is intrincate and can lead to extended down time, unable to forecast estimated time to restore service

### 1.3. Action

- Balancing act: achieving zero data loss at the cost of prolonged vault service down time
  - Reviewed customer vault EBS schedule and estimated maximum data loss of 24 hours
  - Reviewed customer password policy and session usage: concluded that session recordings may be lost, but password out-of-sync issues would be minimal
    - Password change frequency are set to 90 days, which is way less frequent than the EBS snapshot frequency
- Convince both customer and internal support team to restore from EBS snapshot instead of trying to salvage a service that would like fail again even if it is restored
  - Identified that the cause of failure was the platform quantity that was configured by customer beyond the supported limit
- Stakeholder management between sales, partner, customer and CyberArk senior management
  - Provided periodic current status and next step updates of the triage

### 1.4. Result

- GCC PIM service was restored within 4 hours
- Some data was lost for the duration until the point of snapshot - further post incident actions taken to work with affected tenants
- Stakeholder confidence in CyberArk was retained because of the swift decision and recovery

## 2. GCC PIM Operational Governance

### 2.1. Situation

- GovTech compliance requires IaC
  - CyberArk software doesn't work well with auto-scaling and IaC
  - Management did not see this as priority: direction is to ask GovTech to work it out themselves
- Small team running the GCC PIM system already stretched

### 2.2. Task

- Help GovTech work outIaC methodology on CyberArk

### 2.3. Action

- Corporate guidance is on shared responsibility - GovTech should be responsible to resolve out their own GRC requirements
- I chose to **listen** more to the customer and uncovered business risks to CyberArk:
  - The GCC PIM was a single-purpose central service offered to fulfill IM8 requirements
  - IM8 reform is in progress and PAM may be up to agency discretion to implement
  - High costs and risks on operating the PIM central service
    - Cost in Manpower overhead from manual processes
    - Risks of misconfiguration from manual processes
    - These costs and risks are beginning to outweigh the benefits of running the PIM central service
  - GovTech had a CE change from Kok Ping Soon to Goh Wei Boon: new CE is skeptical about operating central services
- Made a judgement call to convince management for resources to work on this, personally also went the extra mile to build relationship and support GovTech team

#### 2.3.1. Controls Implemented:

- AWS Auto Scaling to dynamically scale CyberArk instances based on demand
  - Instance level use **CloudFormation** deploy CyberArk components
  - **AWS Container Registry** to host CyberArk Conjur and HTML5 Gateway images
  - Guest level use Amazon EC2 Auto Scaling **lifecycle hooks** to:
    - Deploy custom scripts to register components to vault
    - Clean-up actions with lambda to trigger on instance termination to clean-up vault records
- **CloudTrail** to monitor configuration actions on the CyberArk deployments
- **SSM** to automate patch management of PAM Windows OS and Conjur Linux OS
- **CloudWatch Logs** to aggregate PAM and Conjur logs - early warning if any automated activities failed
- **CloudWatch Metrics** to monitor PAM and Conjur utilization - provide insights to GovTech management on the central service

### 2.4. Result

Reduced costs and risks of operating the GCC PIM central service

#### 2.4.1. Governance

- Increased IaC methods from 0% to 70% of the GCC PIM components
- Enabled consistent performance with automated controls

#### 2.4.2. Operations management

- Reduced 90% of operational tickets related to deployment scaling
- Reduced 90% of incident tickets on configuration errors attributed to human errors by adopting IaC principles
- Reduced approximately 96-120 man-hours per month required for monitoring, manual deployment, and configuration adjustments

#### 2.4.3. Monitoring and observability

- Reduced MTTD on failures and resource contention from 3-5 days to within 24 hours
  - Immediately identify failures from CloudTrail and CloudWatch Logs
  - Observablity of utilization from CloudWatch Metrics

#### 2.4.4. Compliance and audting

- Reduced the number of outstanding compliance issues by 20 lines items through automation
- Reduced time required to gather PAM audit evidence from 3-4 weeks to 5 business days
  - PAM reporting and continuous monitoring hooked up AWS via API integration
- Enabled evidence-based decision making on performance and capacity mgmt

#### 2.4.5. Cloud financial management

- IaC + auto scaling enabled continual resource right-sizing
- Estimated reduction of compute costs by 30%

## 3. Kubernetes extension for CyberArk PAM

### 3.1. Situation

- Several agencies, including GovTech, enquired on secure acces to Kubernetes, which CyberArk does not have
- Teleport was actively engaging customers and advertising about their capabilities on secure Kubernetes access
- At risk: GovTech swapping out CyberArk PAM common services

### 3.2. Task

- Achieve a viable solution
  - Option A: using Linux jumphost method - works out of the box, have session recording, but credentials embedded on the Linux jumphost
  - Option B: develop custom extension - long development timeline, but fulfil everything

### 3.3. Action

- Extension development - logical development flow
  - Research Kubernetes authentication mechanisms: token-based and certificate-based
  - Dissected Teleport to figure out how they did it - reduced development time needed compared to building from scratch
  - Self-learned C# so that the logic can be implemented on CyberArk
  - Wrote Kubernetes extensions for access credential management and session management - **published on GitHub**
- Stakeholder management
  - Sales and customer: manage timeline and expections while working on the extensions
  - Product management: review extensions and obtain approval to publish

### 3.4. Result

- Customer renewed CyberArk PAM for another 2 years
- 2,000+ users, 20+ agencies

## 4. DevOps SME

### 4.1. Situation

- Conjur was a new product in CyberArk
- Limited competency in CyberArk for DevOps and application development
- Pushing new product adoption is a key priority for sales and management
- Gap between driving sales on new product and competency to support the sales activities

### 4.2. Task

- Step up to help the region on DevOps

### 4.3. Action

- Consistent perserverance over 4 years in CyberArk to establish myself as DevOps expert for both internal SEs and customer facing
- Acquired expertise on industry trends and practices in DevOps
- Self-driven initiative to research competitor or possible integration technologies to understand how industry uses them
  - Container: Kubernetes, Openshift
    - Deployment patterns, mTLS, service mesh
    - Authentication mechanism in Kubernetes
    - Show differences of using push-to-file, push-to-secrets (vol and env), secretless
  - Programming: PHP, Node.js, Python, C#
    - Application architecture, database connection
  - Config mgmt: Ansible, Puppet, Terraform
    - How each config mgmt tool work to manage systems, how secrets are used by them
  - CI/CD tools: GitLab, Jenkins
    - How each CI/CD tool work to run pipelines, how secrets are used by them
  - Serverless: Lambda, API Gateway, RDS
    - To relate to developers on serverless applications, AJAX method
    - Show differences of using IAM authencation, Secrets Manager and Secrets Hub
  - Competitor technology: Hashicorp Vault, Delinea, Teleport

### 4.4. Result

- Over 20 repositories published on GitHub
- APJ SME: winning cases in India, ANZ, Vietnam, Taiwan
  - IRAS, DSTA and JTC Conjur win, total up to 2 mil revenue
- Internal regional SME quality survey correspondents reflected:
  - 72-96 hours of presales effort save by having me as the Regional DevOps SME
  - 60% of requests reduced to global SME team
  - compared to average of 24-48 hours and 30% requests for other SME pillars (identity, access, endpoint)

## 5. Public Sector Events

### 5.1. Situation

- Cost cutting: management considering to reduce marketing event and focus on the annual flagship Impact event
  - AWS Public Sector Day and NCS Red Hat joint event were at risk of being aborted
- Presentation topic:
  - Management wanted to showcase product slides to drive product awareness
  - I believed that speaking on concepts rather than specific products would be more engaging in events

### 5.2. Task

- Maintain CyberArk presence and engagement level with SG Gov't customers
- Establish CyberArk brand recognition as the identity security company

### 5.3. Action

- Stakeholder management
  - Sales: tabulate GCC and NCS revenue and forecast potential revenue to justify event sponsorship
  - Marketing: liason on content to be presented
  - SDR: Qualify lead and support to translate leads to opportunities
- Presentation content:
  - Built industry trends and practices-based content from scratch
  - Focused on IM8, NIST 800-53, CISA ZTMM standards and regulations as background and drivers
  - Showcased concepts and industry best practices on identity, least privilege, secure cloud and machine access, secrets and machine identity

### 5.4. Result

- Represented CyberArk as speaker for AWS Public Sector Day and NCS Red Hat joint event
- Over 100 leads generated in the 2 events - queries focused on fulfilling the industry trends and practices presented
- Translated to sales:
  - NDI: 250 users onboarding to GCC, approx 500k revenue
  - IRAS, DSTA and JTC Conjur win, total up to 2 mil revenue

## 6. Learning Communication and Leadership

### 6.1. Situation

- Personally a high achiever and insist on highest standards
- Lead to ocassional confrontation and some time escalation with sales, channels and service team on competency and quality
- Give me an example of a time when something you tried to accomplish and failed.

### 6.2. Task

- Achieve high standards without escalation

### 6.3. Action

- Realize that not everyone is or wants to be a high achiever, black sheeps are inherent to any organization
- Changed approach in communicating expectations:
  - Dropped confrontational and demanding approach
  - Changed to providing guidance and inspire other through personal experience
- Learned that empathy is not a weakness, but a method to rally people towards a common objective
  - Listening and understanding team members stories, then providing personalize feedback to help grow as a team

### 6.4. Result

- Recognized as peer leader in fostering workplace harmony
- CyberArk Spotlight award 2020

## 7. Conflict with Secrets Management SA

### 7.1. Situation

- Opportunity with JTC for secrets management
- JTC self-hosted environment is air-gapped
- Corporate direction pushes for SaaS: Conjur Cloud and Secrets Hub

### 7.2. Task

- Pitch CyberArk secrets management to JTC

### 7.3. Action

- Leveraged on existing PAM conversation to gain deeper insights on JTC environment
- Discovered customer key concern is secrets in Kubernetes (Nutanix)
- Escalated to management to reverse pitch direction
  - Built up justification with detailed customer architecture on requirements
  - Convinced why SaaS solution would not fit, going against management and corporate wishes
- Advocated doing what's best for the customer
- Built bespoke Kubernetes integration use cases to demonstrate capabilities to JTC

### 7.4. Result

- JTC Conjur win - 1 mil AEB

## 8. Discussion Topics

### 8.1. Monitoring vs Observability

Monitoring collects data on individual components; observability looks at the distributed system as a whole

- Monitoring (when and what): process of collecting data and generating reports on different metrics that define system health
- Observability (why and how): investigative approach that looks closely at distributed system component interactions and data collected by monitoring to find the root cause of issues
  - Metrics + Events + Logs + Traces

### 8.2. Site Reliability Engineering

- Practice of using software tools to automate IT infrastructure tasks such as system management and application monitoring
- Ensure their software applications remain reliable amidst frequent updates from development teams
- Improves collaboration between development and operations team
  - Balancing between rapid changes to an application to release new features or fix critical bugs and ensuring seamless service delivery
- Improves operations planning by accepting that there's a realistic chance for software to fail
  - Plans for the appropriate incident response to minimize the impact of downtime
  - Chaos engineering

#### 8.2.1. Key Principles

- Application monitoring
- Gradual change implementation: release of frequent but small changes to maintain system reliability
- Automation for reliability improvement

#### 8.2.2. Monitoring and Observability in SRE

- **Latency**: the delay when the application responds to a request
- **Traffic**: volume of requests concurrently accessing your service
- **Errors**: condition where the application fails to perform or deliver according to expectations
- **Saturation**: real-time capacity of the application, high saturation = degraded performance, monitor saturation level and ensure it is below threshold

#### 8.2.3. Key Principles

- Service-level objectives (SLOs): specific and quantifiable goals that the software should achieve (uptime, system throughput, system output, download rate)
- Service-level indicators (SLIs): actual measurements of the metric an SLO defines
- Service-level agreements (SLAs): legal documents that state what would happen when one or more SLOs are not met

#### 8.2.4. SRE vs DevOps

- SRE is the practical implementation of DevOps
  - DevOps → philosophical foundation about maintaining software quality amidst increasingly shortened development timeline
  - SRE offers the answers to how to achieve DevOps success
- SRE ensures that the DevOps team strikes the right balance between speed and stability

#### 8.2.5. SRE Tools

- Automation: container orchestrator (EKS)
- Configuration management: SSM, AWS Config, CloudFormation, Control Tower
- Monitoring: CloudWatch (metrics + logs), CloudTrail, X-Ray, Prometheus/Grafana
- Incident response: Security Hub (automated response: EventBridge + Firehose + Lambda)
- Governance/cost: Audit Manager, Budgets, Cost Explorer, Trusted Advisor

### 8.3. Security Operations

CloudOps and SecOps are siblings in GRC

CloudOps keeps the lights on: managing and optimizing cloud infrastructure and services to ensure the efficient operation of cloud environments
- Deployment and maintenance
- Monitoring and Observability
- Scalability and Elasticity
- Cost Management
- Automation

SecOps prevents malicious attempts to meddle with the lights
- Threat Detection and Response
- Vulnerability Management
- Incident Response
- Compliance and Auditing

### 8.4. AI Usage

- Automated repetitive tasks
- Augment operator or analyst efforts: provide real-time insights and intelligent suggestions for issue resolution
- Using historical data to find patterns and trends
  - Prediction, trending, capacity planning: enable smarter and more accurate decisions
  - Optimzation: look at performance data, resource usage, and user behavior to find bottlenecks, optimize configurations, and suggest ways to improve performance
  - Anormally detection: reduce mean time to detect
  - Automate analysis and reaction to problems: reduce mean time to respond

### 8.4.1. AI Challenges

- Data privacy
- Bias in AI models or AI hallucinations
- Over-reliance on AI

## 9. Questions to Ask

- Based on what you observed while engaging customers in the field, what is the synergy or relationship between CloudOps, SRE and SecOps?
- Based on my answers so far, can you highlight areas where I've got wrong? I'd like to learn from them
- What are the areas that you think I'd struggle with if I take on this role?

## 10. Question References

### 10.1. GCC PIM Vault Failure

- Describe a situation in which you were able to use persuasion to successfully convince someone to see things your way.
- Describe a time when you were faced with a stressful situation that demonstrated your coping skills.
- Give me a specific example of a time when you used good judgment and logic in solving a problem.
- Tell me about a time when you had too many things to do and you were required to prioritize your tasks.
- Give me an example of a time when you had to make a split second decision.
- Give me an example of a time when you used your fact-finding skills to solve a problem.
- Tell me about a time when you missed an obvious solution to a problem.
- Describe a time when you anticipated potential problems and developed preventive measures.

### 10.2. GCC PIM Operational Governance

- Tell me about a time when you had to go above and beyond the call of duty in order to get a job done.
- Give me an example of when you showed initiative and took the lead.
- Tell me about a recent situation in which you had to deal with a very upset customer

### 10.3. Kubernetes extension for CyberArk PAM

- Give me an example of when you showed initiative and took the lead.
- Tell me about a recent situation in which you had to deal with a very upset customer

### 10.4. DevOps SME

- Give me an example of a time when you set a goal and were able to meet or achieve it.

### 10.5. Public Sector Events

- Tell me about a time when you had to use your presentation skills to influence someone's opinion.
- Give me a specific example of a time when you had to conform to a policy with which you did not agree.

### 10.6. Negative Example

- What is your typical way of dealing with conflict? Give me an example.
- Tell me about a time you were able to successfully deal with another person even when that individual may not have personally liked you (or vice versa).
- Tell me about a recent situation in which you had to deal with a very upset co-worker.
- Give me an example of a time when you motivated others.
- Describe a time when you set your sights too high (or too low).

### 10.7. Uncategorized

- Please discuss an important written document you were required to complete.
- Tell me about a difficult decision you've made in the last year.
- Tell me about a time when you delegated a project effectively.
- Tell me about a time when you were forced to make an unpopular decision.
- Please tell me about a time you had to fire a friend.
