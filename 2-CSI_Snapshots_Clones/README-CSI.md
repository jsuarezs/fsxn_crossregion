## Create AWS FSx for ONTAP Snapshots and FlexClones

NetApp ONTAP Snapshots are well know for their efficient instantaneous and efficient capability.

Those instant copies are not taking space until the source data start to change. Thanks to only pointers to the original blocks are created they are instantaneous and don't use the same source space at creation time.

This enables a faster DevOps/Test flow for cloning purposes.

Ir order to create a MySQL data Snapshot you need to create first the ````SnapshotClass```` and then trigger the PV Snapshot which is performed by NetApp Astra Trident as CSI driver for AWS FSx for ONTAP.

`````
kubectl create -f volume-snapshot-class.yaml

kubectl create -f volume-snapshot.yaml
`````
Once you already have a Snapshot you could use it to create a FlexClone of your data in a few seconds just to test your MySQL database before going to production. On inital creation, a FlexClone takes almost no space. 

``````
kubectl create -f pvc-from-snapshot.yaml
```````
Now it's possible to run another MySQL Clone deployment using the previous FlexClone just to see the same data is there for testing purposes.
``````
kubectl create mysql-clone.yaml

kubectl exec -it pod -- mysql -u root -p

mysql> use swedenmysql;

mysql> select * from swedenmysqlregs;
``````

So whith this process is easy and fast in order to clone PVs for DevOps/testing flows using AWS FSx for ONTAP features.

Now let's finish this guide showing how you could create a NetApp Snapmirror replica between Sweden (eu-central-1) and Canada (ca-central-1) and import your own volumes in the destination AWS EKS cluster for DR purposes [here.](/3-Trident_Import/README-import.md)