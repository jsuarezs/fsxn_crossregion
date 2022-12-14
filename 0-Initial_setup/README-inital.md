# Initial setup

These are the steps to start working with AWS EKS and AWS FSx for ONTAP as persistent storage:

At the time of this repository creation the following versions were used:
- AWS EKS v1.22.
- NetApp Astra Trident v22.07.0.

Initial steps will be looking as follow:
- Deploy AWS FSx for ONTAP file system as persistent storage service for k8s (MySQL in this case) workloads in Sweden (eu-north-1) and Canada (ca-central-1).
- Deploy AWS EKS in Sweden (eu-north-1) and Canada (ca-central-1) to provide k8s (MySQL) workloads.
- Setting up NetApp Trident as CSI driver and configure storage backends in both regions.


## Deploying AWS FSx for ONTAP

AWS FSx for ONTAP is a native AWS service so in order to deploy the service is as simple as accesing to AWS console using a privileged user to deploy FSx for ONTAP in just several clicks: [Getting started.](https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/getting-started.html)

<img width="1320" alt="Screen Shot 2021-11-12 at 12 59 52" src="https://user-images.githubusercontent.com/59535705/142220610-1aa18f6e-c036-4c54-8bd1-5f0cac2ec31b.png">


## Deploying AWS EKS clusters

AWS EKS  is a managed service that makes it easy ti use k8s on AWS without needing to install and operate your own k8s control plane. You should follow the AWS EKS getting started guide to proceed: https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html

Have in mind that AWS EKS worker nodes need to communicate properly with AWS FSx for ONTAP file systems in order to success providing persistent storage for AWS EKS workloads.

Have in mind that AWS VPC Peering or AWS TGW needs to be configured between Sweden (eu-north-1) and Canada (ca-central-1) regions: https://docs.aws.amazon.com/devicefarm/latest/developerguide/amazon-vpc-cross-region.html

<img width="1018" alt="Screenshot 2022-12-13 at 12 08 58" src="https://user-images.githubusercontent.com/59535705/207302376-f6aaf572-042f-4ab8-8825-91656a4c52b0.png">

## Setting up NetApp Trident as CSI driver and configure storage backends in both regions

IMPORTANT NOTE: Take into consideration that you need to do these steps in both AWS EKS clusters in Sweden (eu-north-1) and Canada (ca-central-1) region in order to provide AWS FSx for ONTAP storage persistency to AWS EKS workloads.

Once AWS EKS clusters are deployed remmember to create or update your ````kubeconfig```` file to allow the communication with the AWS EKS clusters.

`````
aws eks update-kubeconfig --region region-code --name my-cluster
`````

NetApp Astra Trident will be deployed on its own namespace and on every worker node as a ````DaemonSet```` providing at least one Astra Trident pod is up and running all the time.

The firsts steps before downloading and install NetApp Astra Trident will be install the Kubernetes Snapshot CRDs and the Snapshot Controller in order to operate with CSI external-snapshotter sidecar to create FSx for ONTAP Snapshots.

`````
git clone https://github.com/kubernetes-csi/external-snapshotter 

cd external-snapshotter/ 

kubectl kustomize client/config/crd | kubectl create -f - 

kubectl -n kube-system kustomize deploy/kubernetes/snapshot-controller | kubectl create -f - 

kubectl kustomize deploy/kubernetes/csi-snapshotter | kubectl create -f - 
`````

Let's go ahead and download and install NetApp Astra Trident in AWS EKS clusters.


````
wget wget https://github.com/NetApp/trident/releases/download/v22.07.0/trident-installer-22.07.0.tar.gz

tar xvf trident-installer-22.07.0.tar.gz

./tridentctl install -n trident 
`````

Congrats! Astra Trident was deployed succesfully in AWS EKS clusters. See the next steps to configure AWS FSx for ONTAP as persistent storage backend:

````
./tridentctl create backend --filename nas-backend-sweden.yaml -n trident
+---------------+----------------+--------------------------------------+--------+---------+
|     NAME      | STORAGE DRIVER |                 UUID                 | STATE  | VOLUMES |
+---------------+----------------+--------------------------------------+--------+---------+
| sweden-backend | ontap-nas      | b0c3e754-21aa-4bd0-8930-21bc6cc7113c | online |       0 |
+---------------+----------------+--------------------------------------+--------+---------+
````

Ready! 

Now AWS FSx for ONTAP service is available as persistent stogare so the next step will be create StorageClasses and the first MySQL deployment using different QoS [here.](/1-FSxN_PVs/README-PVs.md)