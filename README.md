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

So we can modify it in the security tab of the EC2 instance. Then click on the existing security groups. There is a default security group that is attached to our instance. And then edit the inbound rules.

This is the traffic coming to our EC2 instance. And we add a rule there. We choose custom TCP for the type and TCP for the protocol. And for 
the source we can choose anywhere (We choose my IP if we want it to be accessible only from our PC). For the type we can choose “All traffic” 
if we want our EC2 instance to be accessible by everyone.

We can verify if jenkins is accessible on this port. On the browser we go to public_IP_address_of_the_EC2:8080. And we should we Jenkins there 
if it runs. We get the initial password from the link it shows on this page. We do it with “sudo cat the_link_there”. And we sign into Jenkins 
with this password. And then we install the suggested plugins. 

Then we can create our admin user. Then we have installed Jenkins.

Jenkins master is the EC2 instance that we are using right now.

We should only use Jenkins master for the scheduling purposes. Running everything in the Jenkins is not a good idea for 2 things. First, we 
cannot load it too much. Seconds, some teams might need python 2 and some teams might need python 3. So we should not use Jenkins master for 
everything also because dependency issues.

So we use various Jenkinds nodes for different purposes. For example Jenkins node 1 runs Linux aplications and Jenkinds node 2 runs Windows 
applications.

But the problem here is that if there is no task for a specific Jenkins node (EC2 instance) it will sit idle. This is not the best practice to 
let it sit idle. Auto scaling groups are not enough for this. As we cannot predict every workload we should come up with another solution other 
than using EC2 instances.  

As a solution, we use Jenkins with Docker as agents. So we run Jenkins pipelines on Docker containers. Docker containers are light in weight. 
Containers are easily built and destroyed. For leight-weight applications we can go for Docker containers.

About Docker: Containers include everything needed to run an application (code, dependencies, runtime, libraries). Each container runs in its 
own environment, independent of the host system and other containers. Containers can be easily replicated and scaled up/down based on demand. 
Uses fewer resources compared to traditional virtual machines (VMs) since containers share the host OS kernel. Developers can work in the same 
environment as production, reducing "works on my machine" issues. Containers operate in isolated environments, reducing the impact of security 
vulnerabilities. Containers can be updated, rolled back, and replaced seamlessly. Docker fits perfectly with microservices architecture.

Why should we use Docker?
✅ Faster deployments & testing
✅ Reduces infrastructure costs
✅ Works across different platforms & environments
✅ Simplifies dependency management
✅ Great for CI/CD & DevOps

Now we will install Docker on our EC2 instance.
sudo su -     → we switch to the root user.
usermod -aG docker jenkins→ this allows Jenkins to run Docker commands without requiring sudo.
usermod -aG docker ubuntu → This allows the ubuntu user to run Docker commands without sudo.
systemctl restart docker → Restarts the Docker service to apply any changes made (like group modifications).
-a → append (keep existing group memberships).
-G → Add the user to a new group.

Now with “su – jenkins” we switch to the Jenkins user. This user was created when we installed Jenkins. Then we can run “docker run hello-
world”. If this is successful, that means now Jenkins user can run Docker containers.

Then we restart our Jenkins. For that, we just add /restart at the end of the Jenkins url and click on yes. We do this because Jenkins 
sometimes might not pick up the changes. 

Now we can install the Docker Pipeline plugin in Jenkins. We need Jenkins to execute our pipeline on the Docker agent. For this required 
configuration must be provided in the Jenkins file.

Then in the plugins, we install Docker Pipeline. Then we restart our Jenkins again. We click on New Item. Then we see various ways of creating 
Jenkins Pipelines there.

We use Freestyle for small, quick automation tasks and Pipeline for full-scale DevOps workflows. But as DevOps engineers we should always go 
with Pipelines.

Now let’s create a small project. A simple jenkins pipeline to verify if the docker slave configuration is working as expected. 

Jenkins is all about picking up the code from our local or version control and delivering it to production or any stating environment while 
automating all the stages inbetween. If there are 10 stages, Jenkins acts as an orchestrator. SCM: Source Control Management.

Jenkins files are written in Groovy-based syntax. If we need an extra step we can just add it into the steps in jenkins file.

We can click on Pipeline Syntax and see sample steps. There is a pipeline script generator there that we can use to create pipelines.

For our example we “Pipeline script from SCM” and fill other spaces as well. Enter the repositoy etc. and then we click on “build now”. Now it 
is fetching the code from GitHub.

In “Console Output” we can see what Jenkins has done. 
