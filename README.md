## An ansible playbook for deploying mysql-server 5.7 galera cluster with HAproxy as load balancer

###requirements

This playbook requires minimum 3 ubuntu 16.04 nodes for mysql cluster and 1 ubuntu 16.04 node for HAproxy. cluster nodes and proxy node must be defined inside the included inventory.yml file.
Note: Do not chnage the group names defined inside `inventory.yml` file



###Running Playbook

Before running the playbook you have to configure below variables inside the playbook `deploy-mysql-cluster.yml`

mysql_cluster_name: demo_mysql_cluster  <= name of your mysql cluster without spaces 

mysql_root_password: your_mysql_root_password <= a strong password for mysql root user


ansible-playbook deploy-mysql-cluster.yml -i inventory.yml

After successful completion of playbook, you can access the cluster by `load_balancer_IP:3306`

###Verifying cluster status

You can can verfiy the status of mysql cluster by running below query   

mysql -u root -p 

mysql> SHOW STATUS LIKE 'wsrep_cluster_size';
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| wsrep_cluster_size | 3     |
+--------------------+-------+
1 row in set (0.00 sec)

Which means we have 3 active nodes 
 

###Stop Mysql Cluster

You can use the --tag option "stop_cluster" in ansible to safely stop the mysql cluster

`ansible-playbook deploy-mysql-cluster.yml -i inventory.yml --tags "stop_cluster"`

###Start Mysql Clsuter


You can use the --tag option "start_cluster" in ansible to safely start/bootstrap the mysql cluster

`ansible-playbook deploy-mysql-cluster.yml -i inventory.yml --tags "start_cluster"`


###To-DO List

* Convert this playbook into a ansible role
* Add tasks to verify/test cluster status 
