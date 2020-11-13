# Amazon-EKS-Cluster-Buildout instructions



## Main Steps
 <details>&nbsp;
  <summary> 1. Create an Amazon VPC infrastructure with Cloudformation </summary>
  
#### Build a VPC for EKS using AWS EKS VPC Sample template
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
  <summary> 2. Create an EKS service role </summary>
  
 #### In this Section we Create an IAM ROLE to manage EKS service access
 
* Open the IAM console at https://console.aws.amazon.com/iam/ .
* Choose Roles, then **``` Create role ```** .
* Choose **EKS** from the list of services then **EKS - Cluster** for your use case, and then  ```Next: Permissions``` .
* Choose  ``` Next: Tags ``` , ``` Next: Review ``` 
* Enter **Role name** \<Your EKS Role Name\>  and then ``` Create role ``
* On the Roles main page Choose the newly created \<Your EKS Role Name\>
* Choose ``` Attach Policies ```. Add permissions to \<**Your EKS Role Name**\>
* Filter policies for  ``` AmazonEKSServicePolicy  ```  add a Check next to ** AmazonEKSServicePolicy**  then ``` Attach policy```  

  <details>
  <summary> <B> Create an EKS service role (AWS CLI)</B> </summary>
 
  You can do this in a single step using the AWS CLI instead of the AWS Console:

  ```sh
  # get your account ID
  ACCOUNT_ID=$(aws sts get-caller-identity --output text --query 'Account')

  # define a role trust policy that opens the role to users in your account (limited by IAM policy)
  POLICY=$(echo -n '{"Version":"2012-10-17","Statement":[{"Effect":"Allow","Principal":{"AWS":"arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n     ':root"},"Action":"sts:AssumeRole","Condition":{}}]}')

  # create a role named KubernetesAdmin (will print the new role's ARN)
  aws iam create-role \
    --role-name KubernetesAdmin \
    --description "Kubernetes administrator role (for AWS IAM Authenticator for Kubernetes)." \
    --assume-role-policy-document "$POLICY" \
    --output text \
    --query 'Role.Arn'
  ```

  </details>

</details>

<details>
  <summary> 3. Installing <B>Kubectl</B> and <B>AWS IAM authenticator </B>for EKS </summary>


  <details><summary> For <B>linux</B> Systems </summary>

  #### To install kubectl on linux systems
  **Kubernetes 1.18**
  ```
  {
    mkdir $HOME/bin
    curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.18.9/2020-11-02/bin/linux/amd64/kubectl
    chmod +x .kubectl $HOME/bin/kubectl
    export PATH=$HOME/bin:$PATH
    echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc
  }
  ```

  #### To install aws-iam-authenticator on Linux

  A tool to use AWS IAM credentials to authenticate to a Kubernetes cluster. If you are building a Kubernetes installer on AWS, AWS IAM Authenticator for Kubernetes can simplify your bootstrap process. You won't need to somehow smuggle your initial admin credential securely out of your newly installed cluster. Instead, you can create a dedicated 
  ```KubernetesAdmin``` role at cluster provisioning time and set up Authenticator to allow cluster administrator logins.


  Download the Amazon EKS vended aws-iam-authenticator binary from Amazon S3.

  ```
  {
    curl -o aws-iam-authenticator https://amazon-eks.s3.us-west-2.amazonaws.com/1.18.8/2020-09-18/bin/linux/amd64/aws-iam-authenticator
    chmod +x ./aws-iam-authenticator
    cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator
    aws-iam-authenticator help
  }
  ```

  #### Configure kubectl for EKS 
  
  Update AWS CLI to the latest version direct from AWS
  1. Pull down pip installer

    ```
    python get-oio.py -user
    pip install awscli --upgrade --user
    export PATH=$HOME/.local/bin:$PATH
    echo 'export PATH=$HOME/.local/bin:$PATH' >> ~/.bshrc
    aws eks update-kubeconfig --name <clustername>
    kubectl config view
    kubectl get svc
    ```

  </details>

  <details><summary> For <B>Windows</B> systems  </summary>

  ### To install kubectl on Windows
  * Open a PowerShell terminal window and download the Amazon EKS vended kubectl binary for your cluster's Kubernetes 1.18 version from Amazon S3:
  * The PowerShell script does the following:
  1. Downloads the kubectl.exe executable 
  2. If needed, it creates a new ``` bin``` directory for your kubernetes command line binaries in the currently logged on users home directory. 
  3. Copy the ```kubectl.exe``` executable the ```bin``` directory.
  4. If needed, appends system PATH environment variable with the bin directory and adds the directory if it's missing from the path.
  ```
  curl -o kubectl.exe https://amazon-eks.s3.us-west-2.amazonaws.com/1.18.9/2020-11-02/bin/windows/amd64/kubectl.exe
  
  if (!(Test-Path $env:HOMEPATH/bin)) {mkdir $Env:HOMEPATH + '\bin'} # check if bin exists. create bin if it does not
  Move-Item .\kubectl.exe .\bin\
  $newPath = $Env:HOMEPATH+'\bin'
  if (!(($Env:path).Replace("\","_") -match (($Env:HOMEPATH + '\bin')).Replace("\","_"))) {
      if ($Env:path -notmatch ';\$') { $Semi=";" } else { $Semi="" }
      Set-Item -Path Env:Path -Value ($Env:Path + $Semi + $Env:HOMEPATH + '\bin;')  
      SGet-ItemProperty -Path 'Registry::HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager\Environment' -Name PATH -Value $newPath 
  } 
  ```

  After you install kubectl , you can verify its version with the following command:
  ```
  kubectl version --short --client
  ```
  
  ### To install aws-iam-authenticator on Windows
  []: # (original source - https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html#install-iam-authenticator-windows)

  * Open a PowerShell terminal window and download the Amazon EKS vended aws-iam-authenticator binary from Amazon S3 using the command that corresponds to the Region that your cluster is in.
  * The PowerShell script does the following:
  1. Downloads the aws-iam-authenticator command line binaries 
  2. If needed, it creates a new ``` bin``` directory for your kubernetes command line binaries in the currently logged on users home directory. 
  3. Copy the ```aws-iam-authenticator.exe``` binary to your new directory.
  4. If needed, appends system PATH environment variable with the bin directory and adds the directory if it's missing from the path.
  
  ```
  curl -o aws-iam-authenticator.exe https://amazon-eks.s3.us-west-2.amazonaws.com/1.18.8/2020-09-18/bin/windows/amd64/aws-iam-authenticator.exe
 
  if (!(Test-Path $env:HOMEPATH/bin)) {mkdir $Env:HOMEPATH + '\bin'} # check if bin exists. Create bin if it does not
  Move-Item .\aws-iam-authenticator.exe .\bin\
  $newPath = $Env:HOMEPATH+'\bin'
  if (!(($Env:path).Replace("\","_") -match (($Env:HOMEPATH + '\bin')).Replace("\","_"))) {
      if ($Env:path -notmatch ';\$') { $Semi=";" } else { $Semi="" }
      Set-Item -Path Env:Path -Value ($Env:Path + $Semi + $Env:HOMEPATH + '\bin;')  
      SGet-ItemProperty -Path 'Registry::HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager\Environment' -Name PATH -Value $newPath 
  } 
  ```

  Test that the aws-iam-authenticator binary works.
  ```
  aws-iam-authenticator help
  ```
  #### Configure kubectl for EKS 
  Update AWS CLI to the latest version direct from AWS
      Download and run the MSI installer at [https://awscli.amazonaws.com/AWSCLIV2.msi](https://awscli.amazonaws.com/AWSCLIV2.msi)
  From a powershell command prompt enter the following:
  ```
  aws eks update-kubeconfig -name <cluster name>
  kubectl config view
  kubectl get svc
  ```

  </details>
  
  <details><summary> For <B>Apple </B>systems  </summary>

  **Yea Right!** - Like I'd do directions on how to do this for a MAC

  </details>
</details>

<details>
  <summary> 4. Build a Amazon EKS Cluster (AWS Management Console)</summary>
  
   #### Steps to Create the EKS Cluster
 
   * Log into the AWS Console
   * On the AWS Console go to [Elastic Kubernetes Services](https://us-east-2.console.aws.amazon.com/eks/home?region=us-east-2#/home)
   * At __Create EKS cluster__ enter your cluster name \<EKS-Cluster\>, then   ```  Next Step ```
   1. Cluster Configuration 
      + Pick the Kubernetes Version  ``` 1.18 ```
      + Cluster Service Role   **\<Project\>-eksrole**
   2. Networking
      + **VPC info**  Pick the VPC you made in step #1
      + **Subnets**  Pick all three of the subnets created with the VPC
      + leave **Public** for Cluster endpoint access
      +   ```Next```  ```Next```  ```Create``` 


  <details>
    <summary> <B> (Optional) Create the EKS Cluster using eksctl</B> </summary>
  some stuffs gotta be here
    <details>
      <summary> Install eksctl on windows </summary>

   **To install or upgrade eksctl on Windows using Chocolatey**

   If you do not already have Chocolatey installed on your Windows system, see [Installing Chocolatey.](https://chocolatey.org/install)

   Install or upgrade eksctl .

   Install the binaries with the following command:
    ```
    chocolatey install -y eksctl 
    ```
   If they are already installed, run the following command to upgrade:
    ```
    chocolatey upgrade -y eksctl 
    ```
   Test that your installation was successful with the following command.
    ```
    eksctl version
    ```
    </details>
  more stuff here  
  </details>
</details>



<details>
  <summary> 5. Create Elastic IPs for you worker Nodes </summary>

#### To allocate an Elastic IP address
**Note:** By default you're limited to 5 elastic IP's per region. 

1. Open the Amazon EC2 console at [https://console.aws.amazon.com/ec2/.](https://console.aws.amazon.com/ec2/.)
2. In the navigation pane, choose **Elastic IPs**.
3. Choose **Allocate Elastic IP address**.
4. For **Scope**, choose **VPC**.
5. (VPC scope only) For **Public IPv4 address pool** choose the following:
    * **Amazon's pool of IP addresses**—If you want an IPv4 address to be allocated from Amazon's pool of IP addresses.
6. Choose **Allocate.**

#### associate an Elastic IP address with an instance

1. Open the Amazon EC2 console at [https://console.aws.amazon.com/ec2/.](https://console.aws.amazon.com/ec2/)
2. In the navigation pane, choose **Elastic IPs.**
3. Select the Elastic IP address to associate and choose **Actions, Associate Elastic IP address.**
4. For **Resource type**, choose **Instance**.
5. For instance, choose the instance with which to associate the Elastic IP address. You can also enter text to search for a specific instance.
6. (Optional) For **Private IP address**, specify a private IP address with which to associate the Elastic IP address.
7. Choose **Associate**.
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

