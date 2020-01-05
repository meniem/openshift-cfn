# CloudFormation template to provision RedHat OpenShift Container Platform 3.7 on AWS infrastructure


This CloudFormation template provision RedHat OpenShift Container Platform version 3.7

The CloudFormation template will use AWS Lambda to generate a dynamic SSH key pair that is loaded into an Auto Scaling group, and then pass that environment to Ansible playbooks to build out the OpenShift environment. The Ansible inventory file is auto-generated accordingly.


# RedHat OpenShift system architecture:


![N|Solid](https://raw.githubusercontent.com/meniem/openshift-cfn/master/diagram.jpg)

 - VPC will span three Availability Zones within Ireland region (eu-west-1a, eu-west-1b and eu-west-1c), with one private and one public subnet in each Availability Zone. Besides, An internet gateway (IGW) is used to provide internet access to the public subnet.
 - In one of the Public subnets, an Ansible config server instance will be provisioned.
 - For the Private subnet, we will provision the below instances:
	 - 3 OpenShift Master instances in an Auto Scaling group
	 - 3 OpenShift etcd instances in an Auto Scaling group
	 - 1 OpenShift Node instance in an Auto Scaling group
 - All the instances (master, etcd and node) types are: m4.xlarge, which has the below specs:
	 - CPU: 4 virtual CPUs per instance.
	 - RAM: 16 GB per instance.
	 - Storage: EBS only (80 GB and 110 GB), with high EBS-Optimized available.
	 - Network performance: High.
 - RedHat OpenShift Container Platform version 3.7 is installed.
 - The RedHat subscription username, password and Pool ID are provided to be configured on the instances.
 - An AWS S3 bucket is created to host the template and all the project files.


# Essential requirements:
 - RedHat account with a subscription to an evaluation or a premium versions of RedHat OpenShift Container Platform.
 https://www.redhat.com/wapps/ugc/register.html

 - A working AWS account to provision the infrastructure.


# Implementation  steps:
 - Register on redhat website for the subscription and get a VALID RedHat subscription for the “OpenShift Container Platform”, 
 https://www.redhat.com/wapps/ugc/register.html
 - Obtain the ***Pool ID*** of the subscription, it’s not available in the account settings. Therefore, connect to the “subscription manager” from the CLI and run the below command to get the Pool ID:

  ```sh
sudo subscription-manager register
```
 - After determining the Pool ID, unregister the host from the system used to get thr Pool ID.
 - Start provisioning the cfn template, and fill in the below parameters:
 	- VPC and cubnet CIDR ranges.
 	- Master, etcd, and node instances type and count.
 	- Redhat subscription username/password and the pool ID as obtained in the above steps.
 - Once created, connect to the Ansible-ConfigServer (the public instance), and ssh into the master instance with its private IP. Then check the cluster pods using:
```sh
sudo oc get pods
```
 - check the GUI connection, go to “AWS CloudFormation” console and select the OpenShift stack, then note down the Public URL mentioned in the “OpenShiftUI” in “Outputs” tab.