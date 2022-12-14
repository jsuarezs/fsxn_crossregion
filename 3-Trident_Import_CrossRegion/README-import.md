## Stablish an AWS FSx for ONTAP replica between AWS FSx for ONTAP system in Sweden (eu-central-1) and Canada (ca-central-1) regions and test the DR solution

There are several reasons which could apply looking for an Astra Trident import feature, for example:

- Migrate existing monolithic applications into containers – Volume Import allows you to test and migrate current applications into containers without losing your data.

- Volume migration – Migrate your data from one Kubernetes cluster to another.

- Kubernetes re-build – If the Kubernetes cluster has a catastrophe and needs to be rebuilt from scratch, don’t lose your data! Keep the data and use volume import to retrieve the data and bring into the new re-built cluster.

- Data Management disaster recovery – When using NetApp SnapMirror, volume import can be used to import volumes from the backup site into the Kubernetes cluster.

In this scenario you will create a NetApp SnapMirror replica between both regions to have a DR solution like this. Then you will testing that thanks to NetApp SnapMirror and AWS FSx for ONTAP technology you could recover your MySQL persistent data in Canada (ca-central-1).

![short multi region diagram](https://user-images.githubusercontent.com/59535705/207064981-1c3121f6-5085-4ee5-a89a-100aeff46c7c.jpg)




Once the volume is in AWS FSx for ONTAP is possible to import it to ROSA cluster using Astra Trident Import feature.

````
./tridentctl import volume BackendForNAS trident_pvc_34a9dcc0_e4cd_4d81_9248_8ad5f5bfd19e -f vol-nas-cloud.yaml -n trident
````

Getting started with NetApp Astra Trident Import feature [Getting started.](https://netapp-trident.readthedocs.io/en/stable-v20.07/kubernetes/operations/tasks/volumes/import.html)