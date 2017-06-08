# Ansible Couchbase playbooks

## Rolling upgrade

### Prerequisites
* Ansible >= 2.3
* python requests installed
* An up and running Couchbase cluster
* Ansible role to patch a single node


### Notes
I know that Couchbase recommends to swap-rebalance nodes out, patch them, and swap-rebalance them in. 
Though sometimes you just don't have the HW needed. Or you'd like to patch many clusters in parallel. Or you don't have time to run the rebalance twice for each node.
In my experience (running pretty large Couchbase clusters up to 18 fairly big bare metal nodes, with up to 500m active items on each node in the cluster), the following procedure works just fine.
Basically all you have to do for each node is:
* Gracefully fail over a node
* Re-add it to the cluster for the full recovery
* Patch it
* Rebalance the cluster 

I reflected this in the `couchbase_rolling_upgrade.yml` playbook. What is needed is a role to patch a single node. Of course it's possible to add other roles if needed.
It has been proven to work on RHEL6 and Couchbase 4.x

### Example
This is how I patch one cluster:
`ansible-playbook -i <my_clusters.inv> -l <'my_important_cluster'> couchbase_rolling_upgrade.yml`

`my_clusters.inv` is an inventory file containing your cluster(s)

`'my_important_cluster'` is limiting the scope down to one cluster only -- which is VERY important. ALWAYS limit the scope to one cluster only!
