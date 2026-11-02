# CI/CD 101

## Learning Objectives 

- Distinguish the difference between Continuous Delivery and Continuous Deployment

## What is CI/CD? 

***CI*** stands for **Continuous Integration**
    
- Your code changes will be consistently and automatically integrated into its main code base through automated builds and tests. 

***CD*** can stand for ***continuous Delivery*** or ***continuous Deployment***. There is a difference.

- With continuous **delivery**, before the changes are deployed approval is required and **manual deployment is triggered by a human operator.**
    - Continuous Delivery is used i teams that practice peer reviews, a regular deployment cadence and has not yet been established, or when mission-critical deployemtns are costly and need extra review.

- Continuous **Deployment** has features pushed to their target environments **without the need for human intervention.** Code changes are integrated and then automatically deployed to end-users. 

## Why CI/CD? 

### Automation 

It's what you can automate with CI/CD tools that makes it valuable. 

Figuring out an automation strategy means answering some of these questions. A non-exhaustive list includes: 

- How many commits are developers on your team pushing per day? 
- Do you have a testing strategy for your code changes? 
    - How much code coverage do you need? 
    - Static code analysis?
- What are the metrics of success/failure for code to be approved for deployment stages? Production? 
    - How do you handle rollbacks? 
    - Secrets and sensitive data? 
    - What about monitoring and logging? 

In order to take human hands off of these operations and automate them, strategies must be established for each step so that costs are kept to a minimum, secrets are kept safe, and the process faciltiiates collaboration. 

## How does CI/CD Work? 

CI/CD pipelines employ processes on computers other than your own to take your source code and built it, test it, and deliver it to production environments whenever you push nrew changes to that repository **automatically**. 

Still, not all code projects are the same, and someone's got to make that pipeline. Further still, there are some processes that don't involve reinventing the wheel, so premade sections of pipeliens can be shared. 

Lastly, environment consistency is key. Often, the code that works on your machine doesn't work on antoher's. Pipeline environments that reflect the development and production environmens are crucial. Small changes in the environment can lead to bugs in the final deliverable. 

## GitLab 

CI/CD can be implemented in several ways. We will focus on GitLab.

# CI/CD and Ops

## Learning Objectives

- Explain how CI/CD enables GitOps and improves Developer experience (DX).
- Discuss the role of CI/CD in DevSecOps
- Identify the challenges and opportunities in adopting CI/CD practices 

## Introduction 

This lesson will provide context for CI/CD practices so you can understand some motivations behind whhy osme thigns are done the way they are. 

## A Shift in the Software Development Lifecycle 

Developers are finding themselves shouldering more responsibility in managing the Software Developement Lifecycle. 

Developers now bear the brunt of evaluating metrics like security, performance, code quality, and testing. The Merge of Pull Request emerges as the standard unit - every code addition to the main codebase requires a bevy of automated checks and thorough examination by the team to ensure its integrity. 

In recent years, a cluster of pivotal concepts has reshapred the software development landscape, including: 

- SecOps 
- DevOps
- DevSecOps
- GitOps
- Developer Experience (DX)

These frameworks epitomize the industry's shift towards automation, integartion, and efficiency, driving a cultural change aimed at minimizing errors and enhancing productivity across teams in traditionally separate domains. 

At the heart of these frameworks lies **Continuous Integration/Continuous Deployment/Delivery (CI/CD)**. CI/CD is the linchpin of modern software development, enabling teams to delivery high-quality software at a rapid pace while maintaining security, reliability, and consistency across the development lifecycle. 

## Simplifying Infrastructure and Operations in DevOps

DevOps aims to streamling collaboration between ***development*** and ***operations teams***, those whole build and maintain software and the infrastructure it runs on, respectively. 

**Infrastructure as Code (IaC) practices, coupled with CI/CD pipelines, allow teams to define infrastructure configurations as acode and deploy them automatically using CI/CD workflows.**

This approach ensures consistency, repeatability, and version control on infrastructure changes, leading to more reliable deployments and reduced operational overhead. 

Popular tools like ***Terraform, AWS CloudFormation,*** and ***Azure Resource Manager*** enable teams to define infrastructure configurations as code.While this code is not always stored in the same repository as the application code, doing so can provide a single source of truth for both application and infrastructure configurations. This approach aligns with the GitOps model. 

## How CI/CD Facilitates GitOps and Improves Developer Experience (DX)

Taking DevOps even further, GitOps stands out for its reliance on remote Git repositories as the single source of truth for managing infrastructure configurations, or even none at all, which can lead to inconsistencies and misalignments between application and infrastructure changes. By storing instrastructure configurations in the same repository as the application code, teams can ensure that changes to infrastructure are versioned, reviewed, and deployed **alongside** application changes. 

Gitops can be remembered with the following equation outlined by GitLab" 

```
IaC + MRs + CI/CD = GitOps
```
*Infrastructure as code + Merge Requests + Continuous Integration/Continuous Development = GitOps*

This approach enables temas to **declaratively** manage infrastructure and applications. Just like code, the infrastructural changes can be versioned, rolled back, audited, scanned, and tested. 

As more of the software development lifecycle becomes automated, the role of the developer requires fewer manual operations and more higher-level tasks such as designing, coding, and testing. 

To recap, DX is improved by CI/CD through the following: 

- Automated build and test processes
- Consistent environments 

Having all of these managed in a declarative fashion through GitOps gives greater visibility and control over the development process. 

## Don't Forget About Security, Start with DevSecOps

```
DevOps + SecOps = DevSecOps
```
*Development + Security + Operations = DevSecops*

**SecOps**, short for Security Operations, is a term used in the field of information security and IT operations. It refers to the practice of integrating security measures into the processes and tools that are used to managed and monitor IT systems and infrastructure. 

There are a lot of easy wins that can be achieved by integrating security practices into the software development lifecycle. This is where DevSecOps comes in, shifting security left and ensuring that security is built into applications from the outset. 

**CI/CD plays a critical role in DevSecOps by automating security checks and compliane validation throughout the pipeline.**

Security scans, vulnerability assessments, and code analysis tools can be seamlessly integrated into CI/CD workflows, allowing teams to identify and remediate common security isssues early in the development process. By making security an integral part fo the CI/CD pipeline, organizations can improve the overall security posture of their applications and reduce the risk of security breaches.

## People 

- **If you're a developer** - CI/CD practices can help you automate repetitive tasks, reduce hte time spent on manual operations, and accelerate the delivery of high-quality software. 

- **If you're an operations engineer** - CI/CD practices may help you streamline infrastructure management, reduce the risk of configuration risk, and improve the reliability of deployments. 

- **If you're a security professional** - CI/CD practices might help you integrate security into the development process, identify and remediate security issues early, and reduce the risk of security breaches. 

