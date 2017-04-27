# MoodleAzure
Moodle deployment using Azure Resource Manager Template

[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fpateixei%2FMoodleAzure%2Fv2%2Fazuredeploy.json)  [![Visualize](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.png)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2Fpateixei%2FMoodleAzure%2Fv2%2Fazuredeploy.json)

This Azure Resource Manager template creates a clustered, multi-layered moodle environment. 
With this template we have three main components being deployed: 
a) a web application layer with VMSS and auto-scale enabled
b) a database layer composed of a MariaDb Galera cluster 
c) a shared filesystem layer, for the "moodledata" content.

Main differences from other existing Moodle templates:
a) web layer uses a VMScale Set with auto-scale configured, allowing better usage of resources (02 to 10 web nodes possible)
b) database layer was built using MariaDb Galera Cluster, in a high-available setup, providing 99.95% SLA
c) filesystem layer (MoodleData) was built on top of VMs with Premium Disks, supporting very intensive IO scenarios; also built on top of GlusterFS, a high scalable storage solution from RedHat (see www.glusterfs.org for details), in a High Available setup (data replication accross cluster nodes, also providing a 99.95% SLA).
d) Customer can define the size (small, medium, large) for database and filesystem layers
e) Azure Redis Cache is deployed in the solution, to be used as Moodle Session Cache backend (manual setup required in moodle)
f) it was built for Moodle 3.x deployments 

Summarizing, the following resources will be created during this process:

- a Virtual Machine Scale Set (up to 10 instances) for the web tier, with auto-scale configured
- 02 nodes Gluster Cluster  (2 Premium disks attached, raid0, a gluster brick in each virtual machine), data replicated accross nodes in a HA setup for the filesystem layer
- 02 nodes MariaDb 10 Active-Active Cluster (Galera Cluster), in a HA setup scenario for the database layer
- an Internal Load Balancer in front of the MariaDb cluster
- an public Load Balancer in front of the Virtual Machine Scale Set (web layer)
- a virtual machine used as a JumpBox for the environment, acessible via SSH and http
- a redis cache to be used for Moodle Session Cache (manual setup required in Moodle)
- a lot of underlying resources need for the environment (virtual network, storage accounts, etc)

![Moodle On Azure](./images/moodle-on-azure.jpg)

The setup script will ask you about the 't-shirt size' for database & gluster layers.
Here's an explanation for each one of these: 

Gluster t-shirt sizes: 
		tshirt | VM Size         | Disk Count | Disk Size | Total Size
		Small  | Standard_DS2_v2 |  4         |  127 Gb   | 512 Gb
		Medium | Standard_DS3_v2 |  2         |  512 Gb   | 1 Tb
		Large  | Standard_DS4_v2 |  2         | 1023 Gb   | 2 Tb

MariaDb t-shirt sizes: 

		tshirt | VM Size         | Disk Count | Disk Size | Total Size
		Small  | Standard_DS2_v2 |  2         |  127 Gb   | 256 Gb
		Medium | Standard_DS3_v2 |  2         |  512 Gb   | 1 Tb
		Large  | Standard_DS4_v2 |  2         | 1023 Gb   | 2 Tb

This template is aimed to have constant updates, and would include other improvements in the future. 

Hope it helps.

Feedbacks are welcome.


