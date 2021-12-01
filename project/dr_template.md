# Infrastructure

## AWS Zones

Main Zone: ``us-east-2``

DR Zone: ``us-west-1``

---

## Servers and Clusters

### Table 1.1 Summary

| Asset         | Purpose                                                                                                                     | Size        | Qty                 | DR                                                                  |
|---------------|-----------------------------------------------------------------------------------------------------------------------------|-------------|---------------------|---------------------------------------------------------------------|
| EC2 Instances | Application Servers. They are in the main region ``us-east-2``                                                              | t3.micro    | 3                   | Replicated in ``us-west-1``                                         |
| EC2 Instances | Application servers. They are in the DR region ``us-west-1``                                                                | t3.micro    | 3                   | Deployed to DR plan (``us-west-1``)                                 |
| EKS Cluster   | Kubernetes cluster used for monitoring in the main region ``us-east-2``                                                     | t3.medium   | 1 cluster - 2 Nodes | Replicated in ``us-west-1``                                         |
| EKS Cluster   | Kubernetes cluster used for monitoring in the DR region ``us-west-1``                                                       | t3.medium   | 1 cluster - 2 Nodes | Deployed to DR plan (``us-west-1``)                                 |
| VPC           | Virtual private network that has many IPs in different availability zones                                                   |             |                     | Deployed in ``us-east-2``                                           |
| VPC           | Virtual private network that has many IPs in different availability zones                                                   |             |                     | Deployed in DR plan (``us-west-1``)                                 |
| Load balancer | A load balancer that distributes incoming application traffic across multiple EC2 instances in multiple availability zones  |             |                     | Deployed in ``us-east-2``                                           |
| Load balancer | A load balancer that distributes incoming applicating traffic across the three EC2 instances in multiple availability zones |             |                     | Deployed to DR plan (``us-west-1``)                                 |
| RDS Cluster   | Distributed Relational Database service in the main region ``us-east-2``                                                    | db.t2.small | 1 cluster - 2 Nodes | Deployed in ``us-east-2``                                           |
| RDS Database  | A replicated database for the RDS database in ``us-east-2``. This database exists in the DR region ``us-west-1``            | db.t2.small | 1 cluster - 2 Nodes | Deployed to DR plan (``us-west-1``)                                 |
| GitHub Repo   | Home for the IaC used to provision the cloud, and run the application                                                       |             |                     | Reserved in another region/server other than the main, and DR sites |


### Descriptions

``EC2 Instances``: These are the application servers. Each region has three of them, this increases the availability of the app to users.

``EKS Cluster``: This cluster is used at each region to provide some metrics about the application performance. The cluster contains 
two nodes to make it highly available. 

``VPC``: Amazon VPC is the networking layer for Amazon EC2, it enables you to launch AWS resources into a virtual network that you've defined. It has many IPs in diffrenet availability zones (``us-east-2a``, ``us-east-2b``, ``us-east-2c``) & (``us-west-1a``, ``us-west-1c``) to ensure high availability.

``Load Balancer``: The Load Balancer exists in each region; it's primarily used to distribute the incoming traffic two the three available EC2 instances, based on the load each one of these instances are dealing with.


``RDS Database``: Two clusters, one in the main region ``us-east-2``, and one in the DR region ``us-west-1`` which is basically a replica for the main database cluster. Both 
have retention policy of **five days**, and they consist of **two nodes**. They also have multiple availability zones: (``us-east-2a``, ``us-east-2b``, ``us-east-2c``) & (``us-west-1a``, ``us-west-1c``).


``Github Repo``: That's the home of terraform scripts (IaC) responsible for provisioning the cloud resources, and actually deploying other assets.  



## DR Plan
### Pre-Steps:
- Make sure that the infrastructure is deployed per the configuration of the primary site: ``us-east-2``
- Deploy the infrastructure as code (IaC) to the DR site: ``us-west-1`` 

## Steps:


- Create a cloud load balancer and point DNS to the load balancer. During a failover scenario, fail over the single DNS entry at ``Amazon route 53`` to point to the DR site: ``us-west-2`` 

- Failover your database replication instances in the ``us-west-1`` region
    - Manually force the secondary region (``us-west-1``) to become primary at the database level, or automatically failover the database by health checks


 