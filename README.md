# Amazon-EKS-CLuster-Buildout instructions



## Main Steps
<details>
  <summary> 1. Build out the an Amazon VPC </summary>
  
## Build a VPC for EKS using AWS EKS VPC Sample template
What this section builds using AWS Cloudformation...

![](images/EKS-Cluster-VPC.PNG)

* Open the IAM console at https://console.aws.amazon.com/cloudformation/home?region=us-east-2#

(you choose whatever region you want - I chose Ohio)
* Choose **Create stack**
* On the create a stack page, find **Amazon S3 URL*** adn enter the URL below & next
* Enter a Stack Name ```   EKS-sample-vpc    ``` ``` Next ```   ``` Next ```  ``` Create Stack ```

[Amazon EKS Cloudformation VPC template](https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2018-11-07/amazon-eks-vpc-sample.yaml) 
```
https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2018-11-07/amazon-eks-vpc-sample.yaml

```
</details>

<details>
  <summary> 2. Create IAM Role for EKS </summary>

 **Estimated Cost:**  not really any here. 
* Open the IAM console at https://console.aws.amazon.com/iam/ .
* Choose Roles, then Create role .
* Choose EKS from the list of services, then EKS - Cluster for your use case, and then Next: Permissions .
* Choose Next: Tags .
 * (Optional) Add metadata to the role by attaching tags as key–value pairs. For more information about using tags in...


</details>


<details>
  <summary> 3. Install Kubectl </summary>

     * Sub bullets
</details>

<details>
  <summary> 4. Build a Amazon EKS Cluster </summary>
  
  **Estimated Cost:**  $0.20/hr
  
  * Log into the AWS Console
 
  
</details>


<details>
  <summary> 5. Create Elastic IPs for you worker Nodes </summary>

  **Estimated Cost:**  your basically charged for them when they aren't attached to any running worker nodes.
                 $0.005 per IP address associated with a running instance per hour on a pro rata basis
                 
     
     
</details>

<details>
  <summary> 6. Create Worker Nodes </summary>
  
  **Estimated Cost:** Hourly cost of running the ec2 servers

#### This section builds woker nodes in the VPC using a Cloudformation Script,  then attaches them to the EKS CLuster

* Open the IAM console at https://console.aws.amazon.com/cloudformation/home?region=us-east-2#

(you choose whatever region you want - I chose Ohio)
* Choose **Create stack**
* On the create a stack page, find **Amazon S3 URL*** adn enter the URL below & next
* Enter a Stack Name ```   EKS-sample-vpc    ``` ``` Next ```   ``` Next ```  ``` Create Stack ```


  [Check here!](https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami.html#gpu-ami) to see if this is still the most current version 

  The AWS CloudFormation node template:  

  ```
  https://amazon-eks.s3.us-west-2.amazonaws.com/cloudformation/2020-10-29/amazon-eks-nodegroup.yaml  
  ```

|Kubernetes version 1.18.8  | x86 |
|:------------------------------------|:--|
| Region	|  AMI ID	 |
|US East (Ohio) (us-east-2)  | ami-0dc6bc43da1b962d8	|
|US East (N. Virginia) (us-east-1) | ami-0fae38e27c6113140	|
|US West (Oregon) (us-west-2)	 | ami-04f0f3d381d07e0b6 |
US West (N. California) (us-west-1)	| ami-002e04ca6d86d255e |


| Kubernetes version 1.17.11 | x86 |
|:------------------------------------|:--|
| Region	|  AMI ID	 |
| US East (Ohio) (us-east-2)          | ami-0135903686f192ffe	|
| US East (N. Virginia) (us-east-1)   |	ami-07250434f8a7bc5f1 |
| US West (Oregon) (us-west-2)	      | ami-0c62450bce8f4f57f |
| US West (N. California) (us-west-1)	| ami-05bfd72ad17ebedb8 | 

###  Filling out the form:
The **ClusterName** in your node AWS CloudFormation template must **exactly match** the name of the cluster you want your nodes to join


**TROUBLESHOOTING** the cloudformation script (because you do everything perfectly - everytime!)
* The node is not tagged as being owned by the cluster. Your nodes must have the following tag applied to them, where <cluster-name> is replaced with the name of your cluster.
  

|Key	|Value|
|:-|:-|
|kubernetes.io/cluster/<cluster-name> | owned|


* The nodes may not be able to access the cluster using a public IP address. Ensure that nodes deployed in public subnets are assigned a public IP address. If not, you can associate an elastic IP address to a node after it's launched.

If you STILL have problems go [Here](https://docs.aws.amazon.com/eks/latest/userguide/troubleshooting.html)

  

</details>

