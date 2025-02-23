# CI/CD

CI/CD is continuous integration/continuous delivery. CI is the process where we integrate a set of tools before delivering the application the the customer. And CD is the process where we deploy 
our application. 

Steps for delivering an application using CI/CD practices:

1- Unit Testing: We ensure that individual components (functions, classes, modules) work as expected. We catch bugs early before integration. For example pytest for Python or JUnit for Java.

2- Static Code Analysis: We analyze our code without executing it to find potential errors, bugs, or security issues. This helps with coding standards and best practices. For example Pylint. 
Static code analysis is different than unit testing. Unit Testing ensures that your code works correctly by executing it. Static Code Analysis helps maintain code quality by detecting issues 
without running the code. Both are essential for a reliable and secure DevOps process.

3- Code Quality/Vulnerability: We measure maintainability, readability, and security risks in the codebase. We identifiy potential vulnerabilities (e.g., SQL injection, insecure dependencies). 
Tools: Checkmarx, SonarQube, Snyk.

4- Automation: We automate repetitive tasks like builds, testing, and deployments. We ensure consistency and speed in the development pipeline. Tools: Jenkins, GitHub Actions, GitLab CI/CD.

5- Reports: We provide insights into test results, coverage, security risks, and deployment status. We help teams track issues and improve processes. Tools: Allure Reports, ELK Stack, Grafana.

6- Deployment: We move code from testing/staging to production environments. Deployment can be manual (approval-based) or automated (continuous deployment). Tools: Terraform, Kubernetes, Ansible, 
AWS CodeDeploy.

When we submit our changes to the version control system, CI/CD pipelines will take place.

We need CI/CD tools deployed in our organization. Let’s say we have Jenkins. And we should tell Jenkins that whenever we commit something to a particular repo, it should run a set of actions. 
Jenkins will act as an orchestrator or pipe or tunnel. Jenkins will build the application, run static code analysis and unit tests, do the reporting etc. We as DevOps engineers should configure 
these tools within Jenkins. These tools will run whenever our code is committed to the GitHub repository. Jenkins is an orchestrator that facilitates all these tools. Jenkins integrates all these 
tools in it by using Jenkins pipelines. 

Jenkins deploys our application into multiple stages like dev, stage, and production.
