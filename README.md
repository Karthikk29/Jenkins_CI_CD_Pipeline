1\. Create an EC2 Instance Action: First, create an EC2 instance on AWS.
You can choose an Ubuntu-based instance as the operating system. This
instance will serve as the host machine for Jenkins and the Docker
containers running your application.

How to Do It:

Launch an EC2 instance from the AWS Management Console.

Choose Ubuntu as the operating system.

Configure security groups to allow SSH access and open the required
ports for your application (e.g., port 8080 for Jenkins, port 80 or 443
for the application).

Get the public IP address of the instance for accessing the application
after deployment.

2\. Install Jenkins on EC2 Action: After your EC2 instance is set up,
install Jenkins on the EC2 instance. Jenkins will serve as the
automation server to build, test, and deploy your application.

How to Do It:

SSH into your EC2 instance:

bash Copy Edit ssh -i your-key.pem ubuntu@\<your-ec2-public-ip\> Install
Jenkins:

Update the package list:

bash Copy Edit sudo apt update -y Install Java (since Jenkins requires
it):

bash Copy Edit sudo apt install openjdk-11-jdk -y Add Jenkins repository
key and repository:

bash Copy Edit wget -q -O - https://pkg.jenkins.io/keys/jenkins.io.key
\| sudo apt-key add - sudo sh -c \'echo deb
http://pkg.jenkins.io/debian/ stable main \>
/etc/apt/sources.list.d/jenkins.list\' Install Jenkins:

bash Copy Edit sudo apt update -y sudo apt install jenkins -y Start
Jenkins service:

bash Copy Edit sudo systemctl start jenkins sudo systemctl enable
jenkins Access Jenkins in browser:

Open your browser and navigate to http://\<your-ec2-public-ip\>:8080 to
complete the setup process.

Retrieve the Jenkins initial setup password:

bash Copy Edit sudo cat /var/lib/jenkins/secrets/initialAdminPassword 3.
Generate SSH Key for GitHub Access Action: Generate an SSH key pair for
Jenkins to securely access your GitHub repository.

How to Do It:

Generate SSH key pair:

bash Copy Edit ssh-keygen -t rsa -b 4096 -C \"your_email@example.com\"
This command generates an SSH key pair. Follow the prompts to save it to
the default location (/home/ubuntu/.ssh/id_rsa).

Add public key to GitHub:

Copy the content of the public key:

bash Copy Edit cat \~/.ssh/id_rsa.pub Go to GitHub → Settings → SSH and
GPG keys → New SSH key and paste the public key there.

4\. Configure GitHub Webhook Action: Set up a GitHub webhook to notify
Jenkins whenever new code is pushed to the repository. This ensures
Jenkins automatically triggers the build process after each commit.

How to Do It:

Go to your GitHub repository → Settings → Webhooks.

Add the Jenkins webhook URL:
http://\<your-ec2-public-ip\>:8080/github-webhook/.

Select \"Push events\" to notify Jenkins whenever there are code
changes.

5\. Create a Jenkins Job (Pipeline) Action: Create a Jenkins pipeline
job using the GUI to automate the CI/CD process. The pipeline will
consist of multiple stages: pulling code from GitHub, building the
Docker image, and deploying the application.

How to Do It:

In Jenkins, go to the Dashboard and click New Item.

Choose Freestyle project and name your project (e.g., CI/CD-Pipeline).

Under Source Code Management, select Git.

Enter the repository URL:
git@github.com:your-username/your-repository.git.

Under Credentials, select the SSH key you added earlier.

Under Build, add the following build steps:

Build Docker Image:

Choose Execute shell and enter the following command to build the Docker
image:

bash Copy Edit docker build -t your-image-name . Run Docker Container:

Add another Execute shell step and enter the following command to run
the Docker container:

bash Copy Edit docker run -d -p 8080:80 your-image-name Save the job.

6\. Build and Deploy Docker Image Action: Once Jenkins pulls the latest
code, it uses Docker to build the application into an image and then
deploys it to an EC2 instance.

How to Do It:

Jenkins will build the Docker image using the Dockerfile located in the
project repository.

Build Docker image command:

bash Copy Edit docker build -t your-image-name . Run Docker container:

bash Copy Edit docker run -d -p \<host-port\>:\<container-port\>
your-image-name This command runs the Docker container in detached mode
(-d), mapping the host port (\<host-port\>) to the container port
(\<container-port\>).

7\. Monitor Jenkins and Application Action: Jenkins monitors the entire
pipeline, making sure each stage (build, test, deploy) completes
successfully.

What Happens:

Jenkins provides real-time logs of each step, allowing you to monitor
any issues during the pipeline.

You can monitor the status of the Docker container:

bash Copy Edit docker ps 8. Access Live Application Action: After the
Docker container is deployed, users can access the live application
running on the EC2 instance through the public IP address or a domain
name.

How to Do It:

Access application in browser:

bash Copy Edit http://\<your-ec2-public-ip\>:\<host-port\> This should
show the deployed application running inside the Docker container.
