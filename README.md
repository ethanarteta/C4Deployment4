<p align="center">
<img src="https://github.com/kura-labs-org/kuralabs_deployment_1/blob/main/Kuralogo.png">
</p>
<h1 align="center">C4_deployment-4<h1> 

# Monitoring and Enhancing Web Server Performance

## Purpose

The primary objective of this documentation is to outline the steps taken to monitor a web server's performance and resource utilization. Additionally, it covers the enhancements made in this build, including the implementation of both CloudWatch and DataDog for monitoring and alerting on abnormal CPU usage. Furthermore, the deployment is set up within a manually configured Virtual Private Cloud (VPC) to increase security.

## Setup



## Steps

### 1. Infrastructure Configuration

In this deployment, the infrastructure is manually created to provide greater control over the environment. Instead of configuring subnets, route tables, and availability zones separately, they can all be set up in one step:

- Create a VPC with additional components:
  - Select 2 Availability Zones (AZ).
  - Choose 2 public subnets.
  - Opt for 2 private subnets.
  
### 2. EC2 Configuration

The EC2 instance will host several applications, and because of the manual infrastructure setup, we'll configure the security group manually:

- Launch an EC2 instance with an upgraded `t2.Medium` instance type.
- Use existing keys.
- In the network settings, select the previously created VPC.
- Choose the earlier created `public1` subnet.
- Ensure "Auto-assign public IP" is enabled, making the EC2 visible.
- Configure the instance's security group with the following inbound rules:
  - Port 22 (SSH)
  - Port 80 (HTTP)
  - Port 8000 (Custom TCP)
  - Port 8080 (Custom TCP)

### 3. Git Repository Cloning
```bash
git clone "repo URL"
cd "repo directory"
git checkout "branch name"
# make any updates to new branch repo
git add .
git commit "updated Jenkinsfile"
git push origin "branch name"
```
-Review the changes and merge.

### 4. Jenkins Installation on EC2

Execute the following commands to update and upgrade the instance:
```bash
sudo apt update
sudo apt updgrade
```

Next, install the following prerequisites:
```bash
sudp apt install python3.10-venv
sudo apt install python-pip
sudo apt install python3-pip
sudo apt install nginx
```
- Install the Jenkins server following the provided instructions.
  ```bash
     # Update local package index
     sudo apt-get update
     # Install required packages
     sudo apt-get install fontconfig openjdk-17-jre
     # Install Jenkins
     sudo apt-get install jenkins
     ```
  - Start the Jenkins service:
     ```bash
     sudo systemctl start jenkins
     ```
   - Enable Jenkins to start on boot:
     ```bash
     sudo systemctl enable jenkins
     ```
- Access Jenkins through port 8080 and install the suggested plugins.
- Additionally, install the "Pipeline Keep Running Step" plugin.

### 5. Connecting GitHub to Jenkins

- Create a new repository.
- Paste the downloaded files into the repository.   
- Create a new item and select "Multi-branch pipeline."
- Configure Jenkins Credentials Provider.
- Copy and import the Repository URL where the application source code is located.
- Provide the GitHub username and the generated key as the password.

### 6. Nginx Configuration

After installing Nginx, configure it as follows:

- Edit the Nginx configuration file using the command:
```bash
  sudo nano /etc/nginx/sites-enabled/default
```
Use the provided settings to configure Nginx.

### 7. Installing CloudWatch

To minimize downtime and optimize application performance, implement a monitoring system using CloudWatch:

- Download the CloudWatch agent installation package:
```bash
  wget https://amazoncloudwatch-agent.s3.amazonaws.com/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
```
- Install the package:
```bash
  sudo dpkg -i -E ./amazon-cloudwatch-agent.deb
```
- Navigate to the application location:
```bash
  cd /opt/aws/amazon-cloudwatch-agent/bin/
```
-Run the configuration wizard to set up CloudWatch in greater detail:
```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
```
### 8. AWS Permissions

To grant CloudWatch access to the EC2 instance and log files, follow these steps:

- Select the current working instance.
- Navigate to "Actions" > "Security" > "Modify IAM role" > "CloudWatchAgentServer Role."

### 9. Setting up CloudWatch Alerts

While configuring alerts, monitor three resources. Due to our instance's higher resources, set up alarms for RAM:

- Create an alarm for high Memory usage.
- Configure the statistic to "max" for more detailed monitoring.
- Adjust time intervals and thresholds (e.g., 15% and 5 minutes).

### 10. Installing DataDog

- Go to the [Datadog website](https://www.datadoghq.com/).
- Sign up for a Datadog account and follow first time user instructions.
- Install the Datadog Agent on Your EC2 Instance following the instruction provided by DataDog.
- Start and enable the Datadog Agent to run at boot:
```bash
   sudo systemctl start datadog-agent
   sudo systemctl enable datadog-agent
```
### 11. Set up Monitor Alerts and Jenkins monitoring on DataDog

- Go back to your Datadog account dashboard in the web browser.
- Click on "Infrastructure" in the left sidebar to see your EC2 instance listed.
- Navigate to monitors and then create a metric.
- Configured a alarm that sends email alerts at 85% CPU consumption
- Navigate to CI and select Pipeline Setup
- Follow the instructions provided by DataDog for Jenkins
- Trigger a Jenkins build to observe the process on DataDog

## Observations

- CPU usage during idle periods did not exceed 3.0%.
- Stress testing with two branches running simultaneously resulted in a maximum CPU usage spike of 40.2%.


## Troubleshooting

- CloudWatch issues regarding permissions to my student account encountered. Completed documentation to reflect what should happen but was unable to complete it. Therefore DataDog was introduced instead.

## Optimizations

- Developing scripts to install dependenancies, and to automate most CLI interactions are possible. 
- Add more alerts and increase monitoring to observe application behavior and resource management.

## Conclusion

In conclusion, the T.2 Medium server handled everything smoothly with 40% resource usage. If this process was done on a T.2 Micro instance it might not be able to handle everything smoothly and quickly as it might experience a resource bottleneck. Everytime a another build was scheduled the CPU saw an increase in resource consumption from a 3% idle usage to 40% when executing the build. An email alert was made on DataDog to notify if higher than average usage is encountered.

# Diagram and Images
![image](DocumentationImgDeploy3/Deployment_3_diagram.png)
![image](DocumentationImgDeploy3/Deployment_3_Jenkins.png)
![image](DocumentationImgDeploy3/Deployment_3_ElasticBeanstalk.png)
![image](DocumentationImgDeploy3/Deployment_3_Instances.png)
![image](DocumentationImgDeploy3/Deployment_3_URL_Shortener.png)








































