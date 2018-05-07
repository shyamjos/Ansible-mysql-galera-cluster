## Ansible playbook for deploying mysql-server 5.7 galera cluster with HAproxy as load balancer
### Features of Mysql 5.7 Galera Cluster
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


![mysql galera cluster](https://i.imgur.com/YSR0Pnul.png )

### Running Playbook

add hosts to `inventory.yml`

Note: Do not modify the host group names [mysql_cluster] and [load_balancer] when adding hosts to `inventory.yml` file since it has dependencies inside the playbook. 

```
[mysql_cluster]
mysql_node_1 ansible_host=192.168.10.2 ansible_port=22 ansible_user=ubuntu
mysql_node_2 ansible_host=192.168.10.3 ansible_port=22 ansible_user=ubuntu
mysql_node_3 ansible_host=192.168.10.4 ansible_port=22 ansible_user=ubuntu
[load_balancer]
haproxy_load_balancer ansible_host=192.168.10.5 ansible_port=22 ansible_user=ubuntu
```
Before running the playbook you have to configure below variables inside the playbook : `deploy-mysql-cluster.yml`

`mysql_cluster_name: demo_mysql_cluster`  <= name of your mysql cluster without spaces 

`mysql_root_password: your_mysql_root_password` <= a strong password for mysql root user

To run the playbook 

`ansible-playbook deploy-mysql-cluster.yml -i inventory.yml`

After successful completion of playbook, you can access the cluster by `mysql -h load_balancer_IP -P 3306 -u root -p your_mysql_root_password`

### Verifying cluster status

check the last part of playbook output 

```
ok: [haproxy_load_balancer] => {
    "msg": [
        " Test connection successfull", 
        " Total number of active mysql nodes in cluster: '3 '", 
        " Setup Completed!"
    ]
}


```

You can also verfiy the status of mysql cluster by manually running below query   
~~~~sql
`mysql -h load_balancer_IP -P 3306 -u root -p your_mysql_root_password`

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

You can use the --tag option "stop_cluster" to safely stop the mysql cluster

`ansible-playbook deploy-mysql-cluster.yml -i inventory.yml --tags "stop_cluster"`

### Start Mysql Clsuter


You can use the --tag option "start_cluster" to safely start/bootstrap the mysql cluster

`ansible-playbook deploy-mysql-cluster.yml -i inventory.yml --tags "start_cluster"`


### To-DO List

* Convert this playbook into an ansible role
