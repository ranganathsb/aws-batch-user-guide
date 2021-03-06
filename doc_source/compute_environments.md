# Compute Environments<a name="compute_environments"></a>

Job queues are mapped to one or more compute environments\. Compute environments contain the Amazon ECS container instances that are used to run containerized batch jobs\. A given compute environment can also be mapped to one or many job queues\. Within a job queue, the associated compute environments each have an order that is used by the scheduler to determine where to place jobs that are ready to be executed\. If the first compute environment has free resources, the job is scheduled to a container instance within that compute environment\. If the compute environment is unable to provide a suitable compute resource, the scheduler attempts to run the job on the next compute environment\.


+ [Managed Compute Environments](#managed_compute_environments)
+ [Unmanaged Compute Environments](#unmanaged_compute_environments)
+ [Compute Resource AMIs](compute_resource_AMIs.md)
+ [Creating a Compute Environment](create-compute-environment.md)
+ [Compute Environment Parameters](compute_environment_parameters.md)
+ [Compute Resource Memory Management](memory-management.md)

## Managed Compute Environments<a name="managed_compute_environments"></a>

Managed compute environments enable you to describe your business requirements\. In a managed compute environment, AWS Batch manages the compute resources within the environment, based on the compute resources that you specify\. Instances launched into a managed compute environment use a recent, approved version of the Amazon ECS\-optimized AMI\. You can choose to use Amazon EC2 On\-Demand Instances or Spot Instances in your managed compute environment\. Spot Instances only launch when the Spot bid price is below a specified percentage of the On\-Demand price\.

Managed compute environments launch Amazon ECS container instances into the VPC and subnets that you specify when you create the compute environment\. Amazon ECS container instances need external network access to communicate with the Amazon ECS service endpoint\. If your container instances do not have public IP addresses \(because the subnets you've chosen do not provide them by default\), then they must use network address translation \(NAT\) to provide this access\. For more information, see [NAT Gateways](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\. For help creating a VPC, see [Tutorial: Creating a VPC with Public and Private Subnets for Your Compute Environments](create-public-private-vpc.md)\.

By default, AWS Batch managed compute environments use a recent, approved version of the Amazon ECS\-optimized AMI for compute resources\. However, you may want to create your own AMI to use for your managed compute environments for various reasons\. For more information, see [Compute Resource AMIs](compute_resource_AMIs.md)\.

## Unmanaged Compute Environments<a name="unmanaged_compute_environments"></a>

In an unmanaged compute environment, you manage your own compute resources\. You must ensure that the AMI you use for your compute resources meets the Amazon ECS container instance AMI specification\. For more information, see [Compute Resource AMI Specification](compute_resource_AMIs.md#batch-ami-spec) and [Creating a Compute Resource AMI](create-batch-ami.md)\.

After you have created your unmanaged compute environment, use the [DescribeComputeEnvironments](http://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeComputeEnvironments.html) API operation to view the compute environment details\. Find the Amazon ECS cluster that is associated with the environment and then manually launch your container instances into that Amazon ECS cluster\.

The following AWS CLI command also provides the Amazon ECS cluster ARN:

```
aws batch describe-compute-environments --compute-environments unmanagedCE --query computeEnvironments[].ecsClusterArn
```

For more information, see [Launching an Amazon ECS Container Instance](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/launch_container_instance.html) in the *Amazon Elastic Container Service Developer Guide*\. When you launch your compute resources, specify the Amazon ECS cluster ARN that the resources should register with the following Amazon EC2 user data\. Replace *ecsClusterArn* with the cluster ARN you obtained with the previous command\.

```
#!/bin/bash
echo "ECS_CLUSTER=ecsClusterArn" >> /etc/ecs/ecs.config
```