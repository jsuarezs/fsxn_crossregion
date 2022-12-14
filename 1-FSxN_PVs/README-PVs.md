## Deploy a MySQL deployment inside AWS EKS cluster in Sweden (eu-central-1).

In order to deploy MySQL deployment with data persistency you need to create ````StorageClasses```` to map PVCs in backend configuration. Thanks to ONTAP technology you can also define the best QoS fit for your application (see backend yaml definition to check these labels in [Initial  Setup).](/README-initial.md)

Each StorageClass is pointing to a different QoS on AWS FSx for ONTAP.
````
kubectl apply -f sc-extreme.yaml 

kubectl apply -f sc-low.yaml
````
Now it's time to create the PVC which is used by MySQL deployment using a Extreme QoS category for production purposes.

`````
kubectl create -f pvc-nas.yaml
``````
Creation of MySQL deployment.
`````
kubectl create -f mysql.yaml
`````

In order to test AWS FSx for ONTAP features it's recommended to ingest some data inside MySQL deployment.

`````
kubectl exec -it pod -- mysql -u root -p

mysql> create database swedenmysql;

mysql> use swedenmysql;

mysql> create table swedenmysqlregs (event varchar(20), session varchar(20), location varchar(20));

mysql> insert into swedenmysqlregs (`event`, `session`, `location`) values ('DevOps','automation', 'California'),('CaaS', 
'Provisioning k8s', 'Paris'),('Openshift', 'orchestration', 'Madrid'); 
`````

This is something can be done by ROSA GUI indeed:

To play around with AWS FSx for ONTAP efficient Snapshots and CLones let's go to the next section [here.](/2-CSI_Snapshots/README-CSI.md)