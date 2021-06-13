# Introduction

This document is notes on building an HPC cluster on AWS.

# Get started notes

* Started at [this page](https://aws.amazon.com/hpc/getting-started/)
* Sub-selected [this page](https://workshops.aws/categories/HPC)
* Sub-sub to the [CFD tutorial](https://cfd-on-pcluster.workshop.aws/)

This CFD tutorial breaks down into eight sections. The first two are introductory. This narrative skips to topic 3, "Create a HPC Cluster".

# Topic 3: [Create a HPC Cluster](https://cfd-on-pcluster.workshop.aws/hpccluster/hpc-ssh.html)

* A "cloud shell" is available on the AWS Console so I will use that. 
    * The idea here is that the aws command line application **`aws`** can be installed and run in a `bash` environment
    * So they built this into the console to save us the bother... 
    * Environment includes the AWS cli, Python, nodejs; 1GB storage; persistent memory for this region: Sounds good
    * I confirmed that my IAM User account has an active key that I can access: Check
* I created an ssh key as a `.pem` file. I named it specific to my working region and the topic 'CFD'
    * I used `chmod 400` on it
    * I downloaded a local copy

Moving on to page 2 of topic 3.

* I installed **`pcluster`**; necessary packages `botocore, s3transfer, boto3, tabulate, ipaddress, aws-parallelcluster`. One warning.
    * `pcluster version` gives 2.10.2 and `pcluster -h` gives a lengthy help message: ok
    * `pcluster configure` is a bit involved; here are my process notes
        * Enter the ***ssh key name***, not the corresponding ***filename*** (so for example just `cfd_uswest2`)
        * Head node: Changed to `c5n.large`
        * Compute instance: Changed to `c5n.18xlarge` (expensive! Don't leave the cluster running!)
        * Automate VPC creation: We override the default "no" with **`y`**
        * Choosing 1 or 2 for network configuration enter the single character **`1`**. 
            * This placed the head node on a public subnet (accessible) and the compute instances on a private subnet in the VPC

> Jargon <BR>
> **VPC**: Virtual Private Cloud, a logical construct on AWS that acts as an isolated set or collection of resources.<BR>
> **Cloud formation stack**: A collection of AWS resources that you manage as a single unit. 
See more [here](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html)

The `pcluster configure` process has created a Cloud formation stack file called `config` that resides in `~/.parallelcluster`. 
It is a good idea to read through this (brief) file to see the correspondence between content and the values given in the previous step.
The procedural now instructs us to modify this file using a text editor to include some additional parameters.
   
* Edit the CloudFormation template file `config` (warning this is a bit arduous)
   * Add three lines under `[cluster default]` and also modify `queue_settings` to be `computer,mesh`
   * Add an entire section called `[fsx fsxshared]`
   * Add a short section called `[dcv default]`
   * Modify/extend `[queue compute]` as shown
   * Modify `[compute_resource default]`
   * Add `[queue mesh]`
   * Add `[compute_resource defaultmesh]`
   
   
There follows some explanation of these additions to `config`. Of particular interest is the section onf **FSx for Lustre**
with more content referenced [here](https://aws.amazon.com/fsx/lustre/). **FSx for Lustre** is high-performance scalable storage.
FSx filesystems can also be linked to S3 buckets. 
   


   

   
   



