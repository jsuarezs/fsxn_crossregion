# Using AWS FSx for NetApp ONTAP for MySQL cross regional workloads
Succesfully use AWS FSx for NetApp ONTAP as persistent storage for your MySQL workloads offering a cost-efective cross-region DR solution taking advantage of the NetApp efficient replica between ONTAP systems.

## Introduction

At the begining of the times, we used to work with stateless applications running in containers. Now, we find containers in the critical business applications so they need to use persistent storage to keep the business up and running.

NetApp is a CNCF co-funder member and this implies the maturity of NetApp solutions offering persistent storage for containers. In this use case it's deployed AWS FSx for ONTAP solution giving containers a fully ONTAP data management features plus a cost-effective and efficient cross-region DR solution using ONTAP technologies.

## Architecture

- In this scenario I'm deploying the following services and components for the complete solution:
    - AWS FSx for ONTAP deployed in Sweden region (eu-north-1).
    - AWS FSx for ONTAP deployed in Canada region (ca-central-1).
    - There is a NetApp ONTAP SnapMirror replica between both FSx for ONTAP systems.
    - AWS EKS cluster in Sweden region (eu-north-1) using MySQL database for retaining data.
    - AWS EKS cluster in Canada region (ca-central-1) as a MySQL cross-region DR solution.
    - NetApp Astra Trident as persistent storage provisioner deployed inside AWS EKS clusters.

### Reference architecture high-level view

![short multi region diagram](https://user-images.githubusercontent.com/59535705/207064981-1c3121f6-5085-4ee5-a89a-100aeff46c7c.jpg)


## Setup and testing

My recommendation is going around AWS docs to deploy AWS FSx for ONTAP service so you will understand all the concepts before going steps further -> [AWS FSx for ONTAP docs](https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/getting-started.html)

For better understanding about how AWS EKS works take a look around [AWS EKS docs](https://docs.aws.amazon.com/es_es/eks/latest/userguide/getting-started.html)

And finally go around Trident resources before deploying it so you will understand all the concepts before going steps further -> [Trident docs](https://github.com/NetApp/trident)

Click [here](0-Initial_setup/README-inital.md) to follow the steps in order to do the initial setup and deploying Trident inside AWS EKS cluster and next steps.
