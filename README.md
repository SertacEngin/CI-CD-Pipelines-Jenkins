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

When we add computer power to our Jenkins instances, the setup doesn’t only get more expensive but also the maintenance overhead gets bigger. So 
we need setup that scales up and down automatically. But auto scaling groups are not enough for this. For example there are times like weekends 
and nobody makes any changes to the existing code during these times. So there should be no pipelines executed in these times. In case of 1000s 
of microservices and 20 to 30 Jenkins setups, we have many master and worker nodes. If we don’t configure them well that means 100s of virtual 
machines created but not used. But we should have 0 node workers when we are not making any changes. In such cases Jenkins is not the tool we 
should use.

Let’s look at GitHub Kubernetes now to see how they handled this. In these cases Kubernetes doesn’t waste any VMs. Because they configured 
GitHub actions. GitHub actions is another way of running CI/CD pipelines. Whenever a change is made it will spin up a Kubernetes pod or a Docker 
container and everything gets executed in a Docker container. And we are not using these resources they will be used for some other projects as 
these are shared resources.

So instead of wasting resources for each and every project or instead of creating Jenkins instances for every project, we can create one common 
GitHub actions which can be used by multiple projects in the organization and we can save compute resources. And when there is no code change 
nothing is executed so we save our compute resources. This is a modern day CI/CD setup. So we can also use GitHub Actions, GitLab CI/CD, AWS 
CodePipeline, or some other tools instead of Jenkins.

We will install Jenkins and use Docker as the agent. We won’t create multiple EC2 instances and configure them as worker nodes to this Jenkins 
master. We will use Docker instead. And at the end we will deploy this application into a Kubernetes cluster.

Jenkins is a Java application so we need to install Java. And then we install Jenkins. We can follow the official documentation for these. 

By default our EC2 instances won’t accept external traffic because of the default inbound traffic rules.

With “ps -ef | grep jenkins” we can see that Jenkins runs on this EC2 instance on the port 8080. This port 8080 is not accessible from the 
external world.

So we can modify it in the security tab of the EC2 instance. Then click on the existing security groups. There is a default security group that is attached to our instance. And then edit the inbound
