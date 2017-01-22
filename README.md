# azure-docker4azureoms
Docker for Azure with OMS and some more stacks

[Docker for Azure Release Notes](https://docs.docker.com/docker-for-azure/release-notes/)
This template has additions on top of [Template - Docker for Azure v 1.13.0-1](https://download.docker.com/azure/stable/Docker.tmpl)

#### Deploy and Visualize
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docker4azureoms%2Fmaster%2Fazuredeploy.json" target="_blank"><img alt="Deploy to Azure" src="http://azuredeploy.net/deploybutton.png" /></a>

<a href="http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docker4azureoms%2Fmaster%2Fazuredeploy.json" target="_blank">  <img src="http://armviz.io/visualizebutton.png" /> </a> 

#### Tips

* Post Deployment, one can ssh to the manager using the id_rsa.pub as mentioned during swarm creation: 
<code>ssh docker@sshlbrip -p 50000</code>

* Transfer the keys to the swarm manager to use it as a jumpbox to workers: 
<code>scp -P 50000 ~/.ssh/id_rsa ~/.ssh/id_rsa.pub docker@sshlbrip:/home/docker/.ssh</code>

* For Deploying a stack in v3 docker-compose file: 
<code>docker stack deploy -c --path to docker-compose.yml file-- --stackname-- </code>

* To update stack: 
<code>docker stack up deploy -c --path to docker-compose.yml file-- --stackname--</code>

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
* Then one can login to https://OMSWorkspaceName.portal.mms.microsoft.com and check all containers running for Docker for Azure and use Log Analytics and if Required perform automated backups of the APK Based sys using the corresponding Solutions for OMS.
