+++
title = "Mattermost"
date = 2019-01-04T16:16:15+05:30
draft = false
weight = 20
toc = true
+++

<h3 class="section-head" id="h-install-BotKube-mattermost"><a href="#h-install-BotKube-mattermost">Install BotKube to the Mattermost team</a></h3>

<p>Follow the steps below to install BotKube in your Mattermost Team.</p>

<h4>1. Enable Personal Access Token</h4>
Login with System Admin account, and in the Menu proceed to System console -> Integrations -> Custom Integrations and enable Personal Access Token.
<h4>2. Create BotKube user</h4>
To create a Botkube user, if not already created, proceed to menu and Get team invite link. Logout from admin account and paste the link in the address bar and create a user with the username BotKube.
<h4>3. Manage Roles for BotKube user</h4>
Login as System Admin, in the Menu proceed to System console -> Users. For BotKube user, Manage Roles and allow tokens and post_all access.
<h4>4. Create Token for BotKube user</h4> 
Login as BotKube user, in the Menu proceed to Account Settings -> Security -> Personal Access Token -> Create and save the token.

<h3 class="section-head" id="h-install-BotKube-k8s"><a href="#h-install-BotKube-k8s">Install BotKube in Kubernetes cluster</a></h3>

<h4>BotKube install: Using helm</h4>

- We will be using [helm](https://helm.sh/) to install BotKube in Kubernetes. Follow [this](https://docs.helm.sh/using_helm/#installing-helm) guide to install helm if you don't have it installed already
- Clone the BotKube github repository.

```bash
$ git clone https://github.com/infracloudio/botkube.git
```

- Update default **config** in **helm/botkube/values.yaml** to watch the resources you want (by default you will receive **create**, **delete** and **error** events for all the resources in all the namespaces).
If you are not interested in events about particular resource, just remove its entry from the config file.
- Deploy BotKube backend using **helm install** in your cluster.

```bash
$ helm install --name botkube --namespace botkube \
--set config.communications.mattermost.enabled=true \
--set config.communications.mattermost.url=<MATTERMOST_SERVER_URL> \
--set config.communications.mattermost.token=<MATTERMOST_TOKEN> \
--set config.communications.mattermost.team=<MATTERMOST_TEAM> \
--set config.communications.mattermost.channel=<MATTERMOST_CHANNEL> \
--set config.settings.clustername=<CLUSTER_NAME> \
--set config.settings.allowkubectl=<ALLOW_KUBECTL> \
helm/botkube
```
where,<br>
- **MATTERMOST_ENABLED** set true to enable Mattermost support for BotKube<br>
- **MATTERMOST_SERVER_URL** is the URL where Mattermost is running<br>
- **MATTERMOST_TOKEN** is the Token received by creating Personal Access Token for BotKube user<br>
- **MATTERMOST_TEAM** is the Team name where BotKube is added<br>
- **MATTERMOST_CHANNEL** is the Channel name where BotKube is added and used for communication<br>
- **CLUSTER_NAME** is the cluster name set in the incoming messages<br>
- **ALLOW_KUBECTL** set true to allow kubectl command execution by BotKube on the cluster<br>

   Configuration syntax is explained [here](/configuration).

- Send **@BotKube ping** in the channel to see if BotKube is running and responding.

<br>
<h4>Using kubectl</h4>

- Make sure that you have kubectl cli installed and have access to Kubernetes cluster
- Download deployment specs yaml

```bash
$ wget -q https://raw.githubusercontent.com/infracloudio/botkube/master/deploy-all-in-one.yaml
```

- Open downloaded **deploy-all-in-one.yaml** and update the configuration.<br>
Set *MATTERMOST_ENABLED*, *MATTERMOST_SERVER_URL*, *MATTERMOST_TOKEN*, *MATTERMOST_TEAM*, *MATTERMOST_CHANNEL*, *clustername*, *allowkubectl* and update the resource events configuration you want to receive notifications for in the configmap.<br>

where,<br>
- **MATTERMOST_ENABLED** set true to enable Mattermost support for BotKube<br>
- **MATTERMOST_SERVER_URL** is the URL where Mattermost is running<br>
- **MATTERMOST_TOKEN** is the Token received by creating Personal Access Token for BotKube user<br>
- **MATTERMOST_TEAM** is the Team name where BotKube is added<br>
- **MATTERMOST_CHANNEL** is the Channel name where BotKube is added and used for communication<br>
- **clustername** is the cluster name set in the incoming messages<br>
- **allowkubectl** set true to allow kubectl command execution by BotKube on the cluster<br>

   Configuration syntax is explained [here](/configuration).

- Create **botkube** namespace and deploy resources

```bash
$ kubectl create ns botkube && kubectl create -f deploy-all-in-one.yaml -n botkube
```

- Check pod status in botkube namespace. Once running, send **@BotKube ping** in the Slack channel to confirm if BotKube is responding correctly.


<br>
<h3 class="section-head" id="h-uninstall-BotKube-mattermost"><a href="#h-uninstall-BotKube-mattermost">Remove BotKube from Mattermost Team</a></h3>
- Deactive or remove BotKube user from Mattermost Team. Login as System Admin, in the Menu proceed to System console -> Users ->  Deactivate<br>
- Archive Channel created for BotKube communication if required.

<h3 class="section-head" id="h-uninstall-BotKube-k8s"><a href="#h-uninstall-BotKube-k8s">Remove BotKube from Kubernetes cluster</a></h3>
<h4>Using helm</h4>
<p>If you have installed BotKube backend using **helm**, execute following command to completely remove BotKube and related resources from your cluster</p>

```bash
$ helm delete --purge botkube
```

<h4>BotKube install: Using kubectl</h4>

```bash
$ kubectl delete -f https://raw.githubusercontent.com/infracloudio/botkube/master/deploy-all-in-one.yaml -n botkube
```

