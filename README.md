## Ansible playbook for deploying mysql-server 5.7 galera cluster with HAproxy as load balancer
### features of Mysql Galera Cluster
* True Multi-master,  Active-Active Cluster Read and write to any node at any time.
* Synchronous Replication No slave lag, no data is lost at node crash.
* Tightly Coupled All nodes hold the same state. No diverged data between nodes allowed.
* Multi-threaded Slave For better performance. For any workload.
* No Master-Slave Failover Operations or Use of VIP.
* Hot Standby No downtime during failover (since there is no failover).
* Automatic Node Provisioning No need to manually back up the database and copy it to the new node.
* Supports InnoDB.
* Transparent to Applications Required no (or minimal changes) to the application.
* No Read and Write Splitting Needed.
* Easy to Use and Deploy

### Requirements

This playbook requires minimum 3 ubuntu 16.04 nodes for installing mysql 5.7 galera cluster and 1 ubuntu 16.04 node for installing HAproxy. cluster nodes and proxy node must be defined inside the included inventory.yml file.
Note: Do not chnage the group names defined inside `inventory.yml` file

![mysql galera cluster](https://i.imgur.com/YSR0Pnul.png )

### Running Playbook

Before running the playbook you have to configure below variables inside the playbook : `deploy-mysql-cluster.yml`

`mysql_cluster_name: demo_mysql_cluster`  <= name of your mysql cluster without spaces 

`mysql_root_password: your_mysql_root_password` <= a strong password for mysql root user

To run the playbook 

`ansible-playbook deploy-mysql-cluster.yml -i inventory.yml`

After successful completion of playbook, you can access the cluster by `load_balancer_IP:3306`

### Verifying cluster status

You can can verfiy the status of mysql cluster by running below query   
~~~~sql
mysql -u root -p your_password 

`mysql> SHOW STATUS LIKE 'wsrep_cluster_size';
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| wsrep_cluster_size | 3     |
+--------------------+-------+
1 row in set (0.00 sec)`

~~~~
Which means we have 3 active nodes 
 

### Stop Mysql Cluster

You can use the --tag option "stop_cluster" in ansible to safely stop the mysql cluster

`ansible-playbook deploy-mysql-cluster.yml -i inventory.yml --tags "stop_cluster"`

### Start Mysql Clsuter


You can use the --tag option "start_cluster" in ansible to safely start/bootstrap the mysql cluster

`ansible-playbook deploy-mysql-cluster.yml -i inventory.yml --tags "start_cluster"`


### To-DO List

* Convert this playbook into an ansible role
* Add tasks to verify/test cluster status 
* add tasks to secure nodes
