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

