Deploying AWS Services with Terraform
Using Infrastructure as Code to create AWS services like EC2, S3, and security groups.

Overview:
Deploy 1 EC2 Instances in your Default VPC.
Bootstrap the EC2 instance with a script that will install and start Jenkins.
Create and assign a Security Group to the Jenkins Security Group that allows traffic on port 22 from your IP and allows traffic from port 8080.
Create a S3 bucket for your Jenkins Artifacts that is not open to the public.
Verify that you can reach your Jenkins install via port 8080 in your browser.
Step 1: Configuration Files
With Terraform the main point is to create a configuration file that will deploy all the resources. I am taking a monolithic approach and putting all my resources in one file. When saving the file make sure to have the .tf extension.

To break down the main document the code is in different blocks. Below you can see my first is the terraform block which houses essential information so terraform knows where to build from. After specifying the source and version I created another provider block in order to choose a region; Fun fact is you technically don't need a provider block since terraform would have been smart enough to make resources based on context from other blocks. Time to break down my resource blocks.

![Alt text](1_hQZBB7Mp0YqREwtVaSoeXg-1.webp)

I started with the EC2 instance; The first string is the type of service being created followed by the name I desired for my instance. It is important to follow a certain syntax and adjust your arguments as you go. You can find Ami and instance types through the AWS console. The other 3 arguments are either made beforehand or later in the file. As you can see everything is made as strings except the security group and the user data have different formatting. You can get more details about formatting and syntax in the Terraform Registry.

Below I made my security group and S3 bucket. As you can see the resource blocks look similar to each other. Thats one of the perks to Terraform as it is very human-readable. The vpc id is just my default in my console plugged in directly to the file. Next are the inbound and outbound rules for my security group. These take the form of ingress or egress options respectively. Then finally is the S3 bucket which doesn't need much explaining. Just the name and the bucket, make sure its globally unique.

![Alt text](1_cKwsXBsrx0-vZ6fV91kjiA-1.webp)

Earlier in my user data for my EC2 instance I referenced an .sh file. This file contains all the needed commands to install Jenkins at launch. The problem I ran into was trying to include this script in my main file with an EOF tactic. I ran into so many errors I ended up just finding this approach and learned a lot. Not only that it taught me some issues with keeping everything in 1 file. A main file can reference other files so easily, like with scripts, multiple providers, or even variables.

![Alt text](1_kN2ueycQUGI-PVl54JSBAQ-1.webp)

Step 2: Terraform Workflow
The files are made and now it's time to use them. In the terminal there are going to be 5 commands I’m going to run. The first will be terraform init, which is short for initialize. This will update the backend and download provider plugins.

Init is the first required command, terraform fmt however is not required. The fmt command will simply change any file to a canonical format and style.

Next is to use the terraform validate command which will make sure the configuration is syntactically valid and internally consistent. Just know this only validates the configuration and not anything with remote services.

Another command that isn’t required and is optional is terraform plan. It’s a great way to see the proposed resources and changes before anything actually gets made.
