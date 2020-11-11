# Amazon-EKS-CLuster

What this builds...


## Main Steps
<details>
  <summary> **Build out the an Amazon PVC** </summary>
  
  ## Heading
## Build a VPC
[Amazon EKS Cloudformation VPC template](https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2018-11-07/amazon-eks-sample.yaml) 

```
https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2018-11-07/amazon-eks-sample.yaml
```

  1. A numbered
  2. list
     * With some
     * Sub bullets
</details>

<details>
  <summary> Install **Kubectl**</summary>

     * Sub bullets
</details>

<details>
  <summary> Build a Amazon EKS Cluster </summary>
3.  ($0.20/hr)
     * Sub bullets
</details>

<details>
  <summary> Create Worker Nodes </summary>
#### Estimated Cost -  hourly cost of running the ec2 servers

[Check here!](https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami.html#gpu-ami) to ensure you are using appropriate versions 

The AWS CloudFormation node template:  
```
https://amazon-eks.s3.us-west-2.amazonaws.com/cloudformation/2020-10-29/amazon-eks-nodegroup.yaml  
```


|Kubernetes version 1.18.8 |
|:-|:--:|
|Region	|x86- AMI ID	 |
|US East (Ohio) (us-east-2)  | ami-0dc6bc43da1b962d8	|
|US East (N. Virginia) (us-east-1) | ami-0fae38e27c6113140	|
|US West (Oregon) (us-west-2)	 | ami-04f0f3d381d07e0b6 |
US West (N. California) (us-west-1)	| ami-002e04ca6d86d255e |

|Kubernetes version 1.17.11 |
|:-|:--:|
|Region	|x86- AMI ID	 |
|US East (Ohio) (us-east-2)  | ami-0135903686f192ffe	|
|US East (N. Virginia) (us-east-1) |	ami-07250434f8a7bc5f1 |
|US West (Oregon) (us-west-2)	 | ami-0c62450bce8f4f57f |
US West (N. California) (us-west-1)	| ami-05bfd72ad17ebedb8 |

</details>

  

