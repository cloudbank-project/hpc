# Introduction

This document is notes on building an HPC cluster on AWS.

# Procedural notes

## Stepping to the starting point


* Started at [this page](https://aws.amazon.com/hpc/getting-started/) as the root of AWS HPC
* Sub-selected [this page](https://workshops.aws/categories/HPC) as categorized types of HPC
* Sub-sub to the [CFD tutorial](https://cfd-on-pcluster.workshop.aws/) to focus on computational fluid dynamics (CFD).

This CFD tutorial breaks down into eight sections. The first two are introductory so I skip them. This narrative begins with topic 3, "Create a HPC Cluster".

## Section 3: [Create a HPC Cluster](https://cfd-on-pcluster.workshop.aws/hpccluster/hpc-ssh.html)

### Page 1 of section 3: Getting an ssh key

* A "cloud shell" is available on the AWS Console so I will use that. 
    * The idea here is that the aws command line application **`aws`** can be installed and run in a `bash` environment
    * So they built this into the console to save us the bother... 
    * Environment includes the AWS cli, Python, nodejs; 1GB storage; persistent memory for this region: Sounds good
    * I confirmed that my IAM User account has an active key that I can access: Check
* I created an ssh key as a `.pem` file. I named it specific to my working region and the topic 'CFD'
    * I used `chmod 400` on it
    * I downloaded a local copy

### Page 2 of section 3: Installing `pcluster`

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
> See more [here](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html)

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
   
   
There follows some explanation of these additions to `config`. This is really our first insight into "what the switches and dials do"
and how we could scale up this cluster implementation to apply some serious computer power to a computation. 
Of particular interest is the section onf **FSx for Lustre**
with more content referenced [here](https://aws.amazon.com/fsx/lustre/). **FSx for Lustre** is high-performance scalable storage.
FSx filesystems can also be linked to S3 buckets. 
   
### Page 3 of Section 3: Configure for Graviton2

   
[Graviton2](https://aws.amazon.com/ec2/graviton/)
refers to (second generation) custom ARM processor cores native to AWS. These are intended to optimize spend/compute.
   
   
Copy the `config` file to a new file called `config-arm` and modify this as directed. As previously: For the config
file modification, the notes provided help unpack what's going on, what the modifications accomplish. In this case note
that both references to hyperthreading are deleted. Also there is a change for `sanity_check` to `false`.
   
   
***Concerning hyperthreading***: On Graviton2 the number of vCPUs equals the physical CPUs so there is no need to 
switch off hyperthreading. 
   
   
### Page 4 of Section 3: Create cluster
   
   
Per the `pcluster create cfd -c ~/.parallelcluster/config` command. This takes 10 minutes or so. The same process 
can be run using the second (Graviton2) config file. 


   

   

   

   
   



