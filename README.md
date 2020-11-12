# Amazon-EKS-Cluster-Buildout instructions



## Main Steps
<details>
  <summary> 1. Create an Amazon VPC infrastructure with Cloudformation </summary>
  
## Build a VPC for EKS using AWS EKS VPC Sample template
What this section builds using AWS Cloudformation...

![](images/EKS-Cluster-VPC.PNG)

* Open the IAM console at https://console.aws.amazon.com/cloudformation/home?region=us-east-2#

      (you choose whatever region you want - I chose Ohio)
* Choose **Create stack**
* On the create a stack page, find **Amazon S3 URL*** adn enter the URL below & next
```
    https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2018-11-07/amazon-eks-vpc-sample.yaml
```
* Enter a **Stack Name** like  \<EKS-sample-vpc\> then  ``` Next ```      ``` Next ```     ``` Create Stack ```

to check that this is the latest verion of the template [Amazon EKS Cloudformation VPC template](https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2018-11-07/amazon-eks-vpc-sample.yaml) 
</details>

<details>
  <summary> 2. Create an EKS service role (AWS Console)</summary>
  
 #### In this Section we Create an IAM ROLE to manage EKS service access
 **Estimated Cost:**  not really any here. 
* Open the IAM console at https://console.aws.amazon.com/iam/ .
* Choose Roles, then ``` Create role ``` .
* Choose **EKS** from the list of services then **EKS - Cluster** for your use case, and then  ```Next: Permissions ``` .
* Choose  ``` Next: Tags ```  ``` Next: Review ``` 
* Enter **Role name** \<Your EKS Role Name\>  and then ``` Create role ``
*
* On tole Rolse main page Choose the newly created \<Your EKS Role Name\>
* Choose ``` Attach Policies ```
Add permissions to \<**Your EKS Role Name**\>
* Filter policies for  ``` AmazonEKSServicePolicy  ```  add a Check next to ** AmazonEKSServicePolicy**  then ``` Attach policy```  

.

</details>


<details>
  <summary> 3. Installing & configuring Kubectl for EKS </summary>

  <details>
    <summary> 3. Installing & configuring Kubectl for EKS </summary>
{
  mkdir $HOME/bin
  https://amazon-eks.s3-us-west-2.amazonaws.com/1.12.10/2019-08-14//bin/linux/amd64/kubectl
  chmod +x .kubectl $HOME/bin/kubectl
  export PATH=$HOME/bin:$PATH
  echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc
}
</details>



</details>


<details>
  <summary> 4. Build a Amazon EKS Cluster (AWS Management Console)</summary>
  
  **Estimated Cost:**  $0.20/hr while running
                 
   #### Steps to Create the EKS Cluster
 
   * Log into the AWS Console
   * On the AWS Console go to Elastic Kubernetes Services - https://us-east-2.console.aws.amazon.com/eks/home?region=us-east-2#/home
   * At __Create EKS cluster__ enter your cluster name <EKS-Cluster>, then   ```  Next Step ```
   1. Cluster Configuration 
      * Pick the Kubernetes Version  ``` 1.18 ```
      * Cluster Service Role   ``` \<Project\>-eksrole ```
   2. Networking
     * VPC info  - Pick the VPC you made in step #1
     * Subnets - Pick all three of the subnets created with the VPC
     * leave **Public** for Cluctere endpoint access
     * ``` Next ```  ``` Next ```  ``` Create ``` 
</details>

<details>
  <summary> 5. Create Elastic IPs for you worker Nodes </summary>

  **Estimated Cost:**  your basically charged for them when they aren't attached to any running worker nodes.
                 $0.005 per IP address associated with a running instance per hour on a pro rata basis
</details>

<details>
  <summary> 6. Create Worker Nodes </summary>
  
  **Estimated Cost:** Hourly cost of running the ec2 servers

#### This section builds worker nodes in the VPC using a Cloudformation Script,  then attaches them to the EKS CLuster

* Open the IAM console at https://console.aws.amazon.com/cloudformation/home?region=us-east-2#
* Choose **Create stack**
* On the create a stack page, find **Amazon S3 URL*** and enter the URL below & next
```
  https://amazon-eks.s3.us-west-2.amazonaws.com/cloudformation/2020-10-29/amazon-eks-nodegroup.yaml  
```

* Enter a Stack Name ```   EKS-sample-vpc    ``` ``` Next ```   ``` Next ```  ``` Create Stack ```


  [Check here!](https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami.html#gpu-ami) to see if this is still the most current version 

  The AWS CloudFormation node template:  

  
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

