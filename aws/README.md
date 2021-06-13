# Introduction

This document is notes on building an HPC cluster on AWS.

# Get started notes

* Started at [this page](https://aws.amazon.com/hpc/getting-started/)
* Sub-selected [this page](https://workshops.aws/categories/HPC)
* Sub-sub to the [CFD tutorial](https://cfd-on-pcluster.workshop.aws/)

This CFD tutorial breaks down into eight sections. The first two are introductory. This narrative skips to topic 3, "Create a HPC Cluster".

# Topic 3: Create a HPC Cluster](https://cfd-on-pcluster.workshop.aws/hpccluster/hpc-ssh.html)

* A "cloud shell" is available on the AWS Console so I will use that. 
    * The idea here is that the aws command line application **`aws`** can be installed and run in a `bash` environment
    * So they built this into the console to save us the bother... 
    * Environment includes the AWS cli, Python, nodejs; 1GB storage; persistent memory for this region: Sounds good
    * I confirmed that my IAM User account has an active key that I can access: Check
* I created an ssh key as a `.pem` file. I named it specific to my working region and the topic 'CFD'
    * I used `chmod 400` on it
    * I downloaded a local copy

Moving on to page 2 of topic 3.

* I installed the necessary packages `botocore, s3transfer, boto3, tabulate, ipaddress, aws-parallelcluster`.

