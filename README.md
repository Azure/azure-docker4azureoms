---
title: Deploy Docker for Azure with OMS

description: Learn how to deploy Docker for Azure with one click along with Operational Management Suite Container monitoring, using an ARM (Azure Resource Manager) Template and deploy service stacks

keywords: docker, docker for azure , install, orchestration, management, azure, swarm, OMS, monitoring, scaling
---

# azure-docker4azureoms
Docker for Azure with OMS and some more stacks

Table of Contents
=================

   * [Docker for Azure with OMS](#azure-docker4azureoms)
      * [Prerequisites](#prerequisites)
      * [Deploy and Visualize](#deploy-and-visualize)
      * [MSFT OSCC](#msft-oscc)
      * [Tips](#tips)
      * [Some Samples](#some-samples)
      * [Spring Cloud Netflix Samples](#spring-cloud-netflix-samples)
       * [Monitor dashboard](#monitor-dashboard)
      * [Simplest Topology Specs](#simplest-topology-specs)
       * [Multi Manager with autoscaled worker](#multi-manager-with-autoscaled-worker)
       * [Raft HA](#raft-ha)
      * [Reporting Bugs](#reporting-bugs)
      * [Patches and pull requests](#patches-and-pull-requests)
      * [Usage of Operational Management Suite](#usage-of-operational-management-suite)
      * [Scaling](#scaling)
         
#### Prerequisites

* [Containerized helper-script to help create the Service Principal](https://docs.docker.com/docker-for-azure/#service-principal)
 * <code> docker run -ti docker4x/create-sp-azure --spname-- </code>
    * Obtain App ID
    * Obtain App Secret
* [Obtain Workspace ID and Key for OMS Solutions](https://github.com/Azure/azure-docker4azureoms/blob/master/README.md#usage-of-operational-management-suite)
 * Deploy the above mentioned solutions.
 * Obtain OMS Workspace ID
 * Obtain Workspace Key

#### Deploy and Visualize
<a href="https://preview.portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docker4azureoms%2Fmaster%2Fazuredeploy.json" target="_blank"><img alt="Deploy to Azure" src="http://azuredeploy.net/deploybutton.png" /></a>

<a href="http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docker4azureoms%2Fmaster%2Fazuredeploy.json" target="_blank">  <img src="http://armviz.io/visualizebutton.png" /> </a> 

**[Docker for Azure Release Notes](https://docs.docker.com/docker-for-azure/release-notes/). This template has two simple additions for pushing OMS dockerized agent in each of the nodes (managers and workers) on top of [Template - Docker for Azure v1.13.1-2 (stable)](https://download.docker.com/azure/stable/Docker.tmpl)**

#### MSFT OSCC
This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

#### Tips

* Post Deployment, one can ssh to the manager using the id_rsa.pub as mentioned during swarm creation: 
 <code>ssh docker@sshlbrip -p 50000</code>


* Transfer the keys to the swarm manager to use it as a jumpbox to workers: 
<code>scp -P 50000 ~/.ssh/id_rsa ~/.ssh/id_rsa.pub docker@sshlbrip:/home/docker/.ssh</code>

* For Deploying a stack in v3 docker-compose file: 
<code>docker stack deploy -c --path to docker-compose.yml file-- --stackname-- </code>

* To update stack: 
<code>docker stack up deploy -c --path to docker-compose.yml file-- --stackname--</code>

#### Some Samples
<code> wget  https://raw.githubusercontent.com/Azure/azure-docker4azureoms/master/docker-compose-ekv3.yml && docker stack deploy -c docker-compose-ekv3.yml elasticsearchkibana </code>

*  ElasticSearch Service: http://Docker4AzureRGExternalLoadBalance:9200/ 
*  Kibana Service: http://Docker4AzureRGExternalLoadBalance:5601/ \

<code>wget https://raw.githubusercontent.com/Azure/azure-docker4azureoms/master/docker-compose-votingappv3.yml && docker stack deploy -c docker-compose-votingappv3.yml votingapp</code>

*  Vote: http://Docker4AzureRGExternalLoadBalance:5002/ 
*  Voting Results: http://Docker4AzureRGExternalLoadBalance:5003
*  @manomarks Swarm Visualizer: http://Docker4AzureRGExternalLoadBalance:8083

#### Spring Cloud Netflix Samples
Forked from https://github.com/sqshq/PiggyMetrics, this example demonstrates the use of Netlix OSS API with Spring. The docker-compose file has been updated to make use of the latest features of Compose 3.0; it's still a work in progress. The service container logs are drained into OMS.

<code> wget https://raw.githubusercontent.com/Azure/azure-docker4azureoms/master/docker-compose-piggymetricsv3.yml && docker stack deploy -c docker-compose-piggymetricsv3.yml piggymetrics </code>

*  Rabbit MQ Service: http://Docker4AzureRGExternalLoadBalancer:15672/ (guest/guest)
*  Eureka Service: http://Docker4AzureRGExternalLoadBalance:8761/ 
*  Echo Test Service: http://Docker4AzureRGExternalLoadBalance:8989/ 
*  PiggyMetrics Sprint Boot Service: http://Docker4AzureRGExternalLoadBalance:8081/ 
*  Hystrix: http://Docker4AzureRGExternalLoadBalance:9000/hystrix

##### Monitor dashboard

In this project configuration, each microservice with Hystrix on board pushes metrics to Turbine via Spring Cloud Bus (with AMQP broker). The Monitoring project is just a small Spring boot application with [Turbine](https://github.com/Netflix/Turbine) and [Hystrix Dashboard](https://github.com/Netflix/Hystrix/tree/master/hystrix-dashboard).

Let's see our system behavior under load: Account service calls Statistics service and it responses with a vary imitation delay. Response timeout threshold is set to 1 second.

<img width="880" src="https://cloud.githubusercontent.com/assets/6069066/14194375/d9a2dd80-f7be-11e5-8bcc-9a2fce753cfe.png">

<img width="212" src="https://cloud.githubusercontent.com/assets/6069066/14127349/21e90026-f628-11e5-83f1-60108cb33490.gif">	| <img width="212" src="https://cloud.githubusercontent.com/assets/6069066/14127348/21e6ed40-f628-11e5-9fa4-ed527bf35129.gif"> | <img width="212" src="https://cloud.githubusercontent.com/assets/6069066/14127346/21b9aaa6-f628-11e5-9bba-aaccab60fd69.gif"> | <img width="212" src="https://cloud.githubusercontent.com/assets/6069066/14127350/21eafe1c-f628-11e5-8ccd-a6b6873c046a.gif">
--- |--- |--- |--- |
| `0 ms delay` | `500 ms delay` | `800 ms delay` | `1100 ms delay`
| Well behaving system. The throughput is about 22 requests/second. Small number of active threads in Statistics service. The median service time is about 50 ms. | The number of active threads is growing. We can see purple number of thread-pool rejections and therefore about 30-40% of errors, but circuit is still closed. | Half-open state: the ratio of failed commands is more than 50%, the circuit breaker kicks in. After sleep window amount of time, the next request is let through. | 100 percent of the requests fail. The circuit is now permanently open. Retry after sleep time won't close circuit again, because the single request is too slow.

#### Simplest Topology Specs
The Simplest topology spec of 1 Manager and 2 worker nodes is as follows for [Docker for Azure v 1.13.0-2](https://docs.docker.com/docker-for-azure/release-notes/)

![Simplest Topology](https://raw.githubusercontent.com/Azure/azure-docker4azureoms/master/Docker4AzurebyRancher.png)

##### Multi Manager with autoscaled worker
3 Swarm Managers with Autoscaled 4th Worker as below: 

![3 Swarm Managers with Autoscaled 4th Worker](https://raw.githubusercontent.com/Azure/azure-docker4azureoms/master/Docker4AzurebyRancherAutoScale.png)

##### Raft HA
The consensus algorithm must ensure that if any state machine applies set x to 3 as the nth command, no other state machine will ever apply a different nth command. Raftscope as below for 5 Swarm Managers.
![Raft HA](https://raw.githubusercontent.com/Azure/azure-docker4azureoms/master/raft.gif)

#### Reporting bugs

Please report bugs  by opening an issue in the [GitHub Issue Tracker](https://github.com/Azure/azure-docker4azureoms/issues)

#### Patches and pull requests

Patches can be submitted as GitHub pull requests. If using GitHub please make sure your branch applies to the current master as a 'fast forward' merge (i.e. without creating a merge commit). Use the `git rebase` command to update your branch to the current master if necessary.

#### Usage of Operational Management Suite
**OMS Setup is via the OMS Workspace Id and OMS Workspace Key as per the steps below.**

[Create a free account for MS Azure Operational Management Suite with workspaceName](https://login.mms.microsoft.com/signin.aspx?signUp=on&ref=ms_mms)

* Provide a Name for the OMS Workspace.
* Link your Subscription to the OMS Portal.
* Depending upon the region, a Resource Group would be created in the Sunscription like "mms-weu" for "West Europe" and the named OMS Workspace with portal details etc. would be created in the Resource Group.
* Logon to the OMS Workspace and Go to -> Settings -> "Connected Sources"  -> "Linux Servers" -> Obtain the Workspace ID like <code>ba1e3f33-648d-40a1-9c70-3d8920834669</code> and the "Primary and/or Secondary Key" like <code>xkifyDr2s4L964a/Skq58ItA/M1aMnmumxmgdYliYcC2IPHBPphJgmPQrKsukSXGWtbrgkV2j1nHmU0j8I8vVQ==</code>
* Add The solutions "Agent Health", "Activity Log Analytics" and "Container" Solutions from the "Solutions Gallery" of the OMS Portal of the workspace.
* While Deploying the Docker for Azure Template just the WorkspaceID and the Key are to be mentioned and all will be registered including all containers in any nodes of the Docker for Azure auto cluster.
* Then one can login to https://OMSWorkspaceName.portal.mms.microsoft.com/#Workspace/overview/solutions/details/index?solutionId=Containers and check all containers running for Docker for Azure and use Log Analytics and if Required perform automated backups of the APK Based sys using the corresponding Solutions for OMS.
* OMS Monitoring 3 Swarm Managers with 4 workers and all their containers.

![OMS Docker 4 Azure](https://raw.githubusercontent.com/Azure/azure-docker4azureoms/master/OMSDocker4Azure.png)
#### Scaling

VMSS auto scale is by default off to be addressed in a [later version](https://forums.docker.com/t/autoscaling-azure-virtual-machine-scale-sets/26030/4)

* For now manual scaling as per the pic below, and automatic OMS Registering of new workers in the swarm as in the pic last. (Manually added 2 new workers  as per the next below pic and hence 3 Managers + 3 Workers initially deployed via this template + 2 manually scaled worker nodes = Total 8).

![Manual VMSS Scaling](https://cdn-enterprise.discourse.org/docker/uploads/default/original/2X/8/849dfcceb32f5c89eddfc90a58008c6bb17a0043.png)

![OMS Auto Post Scaling](https://raw.githubusercontent.com/Azure/azure-docker4azureoms/master/ScaledWorkers.png)

