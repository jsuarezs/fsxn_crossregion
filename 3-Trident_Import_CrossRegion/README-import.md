## Stablish an AWS FSx for ONTAP replica between AWS FSx for ONTAP system in Sweden (eu-central-1) and Canada (ca-central-1) regions and test the DR solution

There are several reasons which could apply looking for an Astra Trident import feature, for example:

- Migrate existing monolithic applications into containers – Volume Import allows you to test and migrate current applications into containers without losing your data.

- Volume migration – Migrate your data from one Kubernetes cluster to another.

- Kubernetes re-build – If the Kubernetes cluster has a catastrophe and needs to be rebuilt from scratch, don’t lose your data! Keep the data and use volume import to retrieve the data and bring into the new re-built cluster.

- Data Management disaster recovery – When using NetApp SnapMirror, volume import can be used to import volumes from the backup site into the Kubernetes cluster.

In this scenario you will create a NetApp SnapMirror replica between both regions to have a DR solution like this. Then you will testing that thanks to NetApp SnapMirror and AWS FSx for ONTAP technology you could recover your MySQL persistent data in Canada (ca-central-1).

![short multi region diagram](https://user-images.githubusercontent.com/59535705/207064981-1c3121f6-5085-4ee5-a89a-100aeff46c7c.jpg)

We need to create an AWS FSx for ONTAP Data Protection (DP) volume in Canada (ca-central-1) specifying the same capacity as the origin AWS FSx for ONTAP volume in the source in Sweden (eu-north-1). The capacity pool Tiering Policy could be set to All or Auto depends on our RTO SLAs.

![Screenshot 2022-12-14 at 10 46 49](https://user-images.githubusercontent.com/59535705/207562286-2b1d2f53-e02d-45ba-b264-1b2511f7aed0.png)

Now you need to create a cluster relationship using the following documentation: 
- https://docs.netapp.com/us-en/ontap/peering/create-cluster-relationship-93-later-task.html - 
- https://docs.netapp.com/us-en/ontap/peering/create-intercluster-svm-peer-relationship-93-later-task.html

These operations are done using AWS FSx for ONTAP CLI accesing by SSH to the Management Endpoint you may find in Administration FSX for ONTAP tab.

In order to do this it's necessary to have in mind the Intercluster IP addresses which you may find in Administration AWS FSx for ONTAP tab. In this case is called ````mysql_dr````.

![Screenshot 2022-12-14 at 10 55 54](https://user-images.githubusercontent.com/59535705/207564284-86c5f83e-8d74-4d94-8b9f-cb54c8ba501d.png)
 
Once the AWS FSx for ONTAP relationship is already stablished you should initialize it to transfer the data from one region to the source one, break it and you will be ready to import the new PV volume to AWS EKS cluster in Sweden region (eu-north-1).

````
snapmirror initialize -destination-path eks-canada:mysql_dr

snapmirror show (idle)

snapmirror quiesce -destination-path eks-canada:mysql_dr

snapmirror break -destination-path eks-canada:mysql_dr

````
To complete the DR process once the AWS FSx for ONTAP SnapMirror replica was broken you will import the PV in the destination in Sweden (eu-north-1) AWS EKS cluster.

`````
./tridentctl import volume canada-backend mysql_dr -f ../pvc-dr.yaml -n trident
``````

And then create the MySQL DR deployment using the previous PV volume coming from Sweden region (eu-north-1) and check that the same peristent data is already in the destination AWS EKS cluster.
`````
kubectl create -f ../mysqldr.yaml

kubectl exec -it pod -- mysql -u root -p (Netapp1!)

mysql> use swedenmysql;

mysql> select * from swedenmysqlregs;
`````

## CONCLUSIONS

Using AWS FSx for NetApp ONTAP in cobination with NetApp Astra Trident you will get these benefits:

- Reduce complexity
Provision stateful containerized applications taking advantages of ONTAP Enterprise-grade features and simplify DevOps workflows using NetApp Astra Trident.

- Move persistent data to everywhere
Multi-Cloud and on-premises data portability thanks to ONTAP technology.

- Save time…
AWS FSx for NetApp ONTAP deployment and configuration can be automated using Terraform, Ansible, AWS CloudFormation and ONTAP REST API.

- …And money!
Profit from AWS FSx for NetApp ONTAP savings with built-in storage efficiency and tiering technologies.
