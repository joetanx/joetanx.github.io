## 1. GCC PIM Vault failure

### 1.1. Applicable questions

- Describe a situation in which you were able to use persuasion to successfully convince someone to see things your way.
- Describe a time when you were faced with a stressful situation that demonstrated your coping skills.
- Give me a specific example of a time when you used good judgment and logic in solving a problem.
- Tell me about a time when you had too many things to do and you were required to prioritize your tasks.
- Give me an example of a time when you had to make a split second decision.
- Give me an example of a time when you used your fact-finding skills to solve a problem.
- Tell me about a time when you missed an obvious solution to a problem.
- Describe a time when you anticipated potential problems and developed preventive measures.

### 1.2. Situation

- Tenants of the GCC PIM service were reporting inability to login to the system
- Investigation revealed that the Vault has crashed

### 1.3. Task

- Objective was to rescue existing Vault service
- Vault has an embedded database that requires L3 support expertise to troubleshoot
- Troubleshooting the embedded database is intrincate and can lead to extended down time, unable to forecast estimated time to restore service

### 1.4. Action

- Identified that the cause of failure was the platform quantity that was configured by customer beyond the supported limit
- Convince both customer and internal support team to restore from EBS snapshot instead of trying to salvage a service that would like fail again even if it is restored
- Orchestrated communications to stakeholders such as sales and customer and CyberArk senior management - provided periodic current status and next step updates of the triage

### 1.5. Result

- GCC PIM service was restored within 4 hours
- Some data was lost for the duration until the point of snapshot - further post incident actions taken to work with affected tenants
- Stakeholder confidence in CyberArk was retained because of the swift decision and recovery

## 2. GCC PIM Operational Governance

### 2.1. Applicable questions

- Tell me about a time when you had to go above and beyond the call of duty in order to get a job done.
- Give me an example of when you showed initiative and took the lead.
- Tell me about a recent situation in which you had to deal with a very upset customer

### 2.2. Situation

- GovTech compliance requires IaC
  - CyberArk software doesn't work well with auto-scaling and IaC
  - Management did not see this as priority: direction is to ask GovTech to work it out themselves
- Small team running the GCC PIM system already stretched

### 2.3. Task

- Help GovTech work outIaC methodology on CyberArk

### 2.4. Action

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

#### 2.4.1. Controls Implemented:

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

### 2.5. Result

- Reduced GovTech cost of running the GCC PIM central service: implementing auto scaling and resource monitoring enabled continual right sizing of resources
- Accurate metrics and utilization tracking enabled:
  - Evidence-based decision making on performance and capacity mgmt
  - Cloud governance with consistent performance
  - Predictable consumption model for continuous improvement
- GovTech feedback on operational efficiency numbers:
  - Reduced the number of outstanding compliance issues by 20 lines items through automation
  - Reduced 90% of operational tickets related to deployment 
  - Reduced 90% of incident tickets on configuration errors attributable to human error by adopting Infrastructure as Code principles
  - Saved approximately 96-120 man-hours per month required for monitoring, manual deployment, and configuration adjustments
- Empower governance with evidence to prove that security policies are being enforced
  - PAM reporting and continuous monitoring hooked up AWS via API integration

## 3. Kubernetes extension for CyberArk PAM

### 3.1. Applicable questions

- Give me an example of when you showed initiative and took the lead.
- Tell me about a recent situation in which you had to deal with a very upset customer

### 3.2. Situation

- Several agencies, including GovTech, enquired on secure acces to Kubernetes, which CyberArk does not have
- Teleport was actively engaging customers and advertising about their capabilities on secure Kubernetes access
- At risk: GovTech swapping out CyberArk PAM common services

### 3.3. Task

- Achieve a viable solution
  - Option A: using Linux jumphost method - works out of the box, have session recording, but credentials embedded on the Linux jumphost
  - Option B: develop custom extension - long development timeline, but fulfil everything

### 3.4. Action

- Extension development
  - Dissected Teleport to figure out how they did it
  - Self-learned C# so that the logic can be implemented on CyberArk
  - Wrote Kubernetes extensions for access credential management and session management - **published on GitHub**
- Stakeholder management
  - Sales and customer: manage timeline and expections while working on the extensions
  - Product management: review extensions and obtain approval to publish

### 3.5. Result

- Customer renewed CyberArk PAM for another 2 years
- 2,000+ users, 20+ agencies

## 4. DevOps SME

### 4.1. Applicable questions

- Give me an example of a time when you set a goal and were able to meet or achieve it.

### 4.2. Situation

- Conjur was a new product in CyberArk
- Limited competency in CyberArk for DevOps and application development
- Pushing new product adoption is a key priority for sales and management
- Gap between driving sales on new product and competency to support the sales activities

### 4.3. Task

- Step up to help the region on DevOps

### 4.4. Action

- Establish myself as DevOps expert for both internal SEs and customer facing
- Consistent perserverance over 4 years in CyberArk
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

### 4.5. Result

- Over 20 repositories published on GitHub
- APJ SME: winning cases in India, ANZ, Vietnam, Taiwan
  - IRAS, DSTA and JTC Conjur win, total up to 2 mil revenue
- Internal regional SME quality survey correspondents reflected:
  - 72-96 hours of presales effort save by having me as the Regional DevOps SME
  - 60% of requests reduced to global SME team
  - compared to average of 24-48 hours and 30% requests for other SME pillars (identity, access, endpoint)

## 5. Public Sector Events

### 5.1. Applicable questions

- Tell me about a time when you had to use your presentation skills to influence someone's opinion.
- Give me a specific example of a time when you had to conform to a policy with which you did not agree.

### 5.2. Situation

- Cost cutting: management considering to reduce marketing event and focus on the annual flagship Impact event
  - AWS Public Sector Day and NCS Red Hat joint event were at risk of being aborted
- Presentation topic:
  - Management wanted to showcase product slides to drive product awareness
  - I believed that speaking on concepts rather than specific products would be more engaging in events

### 5.3. Task

- Maintain CyberArk presence and engagement level with SG Gov't customers
- Establish CyberArk brand recognition as the identity security company

### 5.4. Action

- Stakeholder management
  - Sales: tabulate GCC and NCS revenue and forecast potential revenue to justify event sponsorship
  - Marketing: liason on content to be presented
  - SDR: Qualify lead and support to translate leads to opportunities
- Presentation content:
  - Built industry trends and practices-based content from scratch
  - Focused on IM8, NIST 800-53, CISA ZTMM standards and regulations as background and drivers
  - Showcased concepts and industry best practices on identity, least privilege, secure cloud and machine access, secrets and machine identity

### 5.5. Result

- Represented CyberArk as speaker for AWS Public Sector Day and NCS Red Hat joint event
- Over 100 leads generated in the 2 events - queries focused on fulfilling the industry trends and practices presented
- Translated to sales:
  - NDI: 250 users onboarding to GCC, approx 500k revenue
  - IRAS, DSTA and JTC Conjur win, total up to 2 mil revenue

## 6. Negative Example

### 6.1. Applicable questions

- What is your typical way of dealing with conflict? Give me an example.
- Tell me about a time you were able to successfully deal with another person even when that individual may not have personally liked you (or vice versa).
- Tell me about a recent situation in which you had to deal with a very upset co-worker.
- Give me an example of a time when you motivated others.
- Describe a time when you set your sights too high (or too low).

### 6.2. Situation

- Personally a high achiever and insist on highest standards
- Lead to ocassional confrontation and some time escalation with sales, channels and service team on competency and quality
- Give me an example of a time when something you tried to accomplish and failed.

### 6.3. Task

- Achieve high standards without escalation

### 6.4. Action

- Realize that not everyone is or wants to be a high achiever, black sheeps are inherent to any organization
- Changed approach in communicating expectations:
  - Dropped confrontational and demanding approach
  - Changed to providing guidance and inspire other through personal experience
- Listening and understanding team members stories, then providing personalize feedback to help grow as a team

### 6.5. Result

- Recognized as peer leader in fostering workplace harmony
- CyberArk Spotlight award 2020

## Work in progress

- Please discuss an important written document you were required to complete.
- Tell me about a difficult decision you've made in the last year.
- Tell me about a time when you delegated a project effectively.
- Tell me about a time when you were forced to make an unpopular decision.
- Please tell me about a time you had to fire a friend.
