---
tags: [ "eclipse" , "che" ]
title: Runtime Machines
excerpt: ""
layout: docs
permalink: /:categories/machines/
---
{% include base.html %}

A machine is part of an environment, which are in turn part of a {{site.product_mini_name}} workspace. The [workspace administration introduction]({{base}}{{site.links["ws-admin-intro"]}}) explains the details of this relationship.

A machine is created from a [runtime stack]({{base}}{{site.links["ws-stacks"]}}). {{site.product_mini_name}} supports both single-machine environments and multi-machine environments. The {{site.product_mini_name}}  server manages the lifecycle of environments and the machines inside, including creating snapshots of machines.  Additionally, the {{site.product_mini_name}}  server can inject [workspace agents]({{base}}{{site.links["ws-agents"]}}) into a machine to provide additional capabilities inside the machine.

## Add / Remove Libraries and Tools To Runtime Machines
You can use the terminal and command line to install additional libraries and tools. After you have created a workspace, open a terminal in the IDE.  You can then perform commands like `npm` or `yum install` to add software into your workspace.  These changes will only exist for the lifespan of this single workspace. You can capture these changes permanently by creating a snapshot (which can then be used as a [recipe]({{base}}{{site.links["ws-recipes"]}})), or writing a custom stack that includes these commands in a Dockerfile (this method will make your workspace shareable with others).

![install-jetty8.png]({{base}}{{site.links["install-jetty8.png"]}})

The following example takes a Java ready-to-go stack and adds Jetty8 in the workspace runtime configuration.

```shell  
# apt-get update
sudo apt-get update

# Upgrade existing tools
sudo apt-get upgrade

# Install Jett8
sudo apt-get install jetty8

# Jetty8 installed at path /usr/share/jetty8
```

## Machine Snapshot
Machines can have their internal state saved into a Docker image with a snapshot.

Snapshots are important to preserve the internal state of a machine that is not defined by the recipe. For example, you may define a recipe that includes maven, but your project may require numerous dependencies that are downloaded and locally installed into the internal maven repository of the machine. If you stop and restart the machine without a snapshot, that internal state will be lost.

Note that once you've snapshotted a workspace, changing the environment or machine names inside the workspace will result in the snapshot being lost.

Snapshots image a machine and then it is committed, tagged, and optionally pushed into a Docker registry. You can use a local Docker registry or a remote one. See [Configuration]({{base}}{{site.links["setup-configuration"]}}) for information on how to setup a docker registry.

By default machines are automatically snapshotted when they are stopped. By default, {{site.product_mini_name}} does not need a local/remote Docker registry to create snapshots. If no registry is used, a container is committed into an image which is then tagged, so that next time a workspace is started with this image. The behavior is regulated with the following environment variables:

```shell  
# Provide in {{site.data.env["filename"]}}

# If false, snaps are saved to disk. If true, snaps are saved in a registry.
# The namespace is how the snapshots will be organized in the registry.
{{site.data.env["DOCKER_REGISTRY__FOR__SNAPSHOTS"]}}=false
{{site.data.env["DOCKER_NAMESPACE"]}}=NULL

# Docker Registry for Workspace Snapshots
{{site.data.env["DOCKER_REGISTRY"]}}=<your_private_registry_url>:5000

# Enable/Disable auto snapshotting and auto restoring from a snapshot
{{site.data.env["WORKSPACE_AUTO__SNAPSHOT"]}}=true
{{site.data.env["WORKSPACE_AUTO__RESTORE"]}}=true
```

If you turn off auto-snapshotting it is still possible to snapshot workspaces by going to the machine perspective by clicking the button on the far right of the menu bar. Then choose Machines > Snapshot from the top menu bar. See our other docs [for details on setting up a local or remote Docker Registry]({{base}}{{site.links["setup-configuration"]}}).

![che-create-snapshot.jpg]({{base}}{{site.links["che-create-snapshot.jpg"]}})

## Machine Information
Information on each machine is provided by {{site.product_formal_name}} server.

### Operations Perspective Machine Information
Information on each machine can be viewed in the operations perspective in the IDE. You can toggle between the code perspective and operations perspective by clicking the pair of buttons in the top-right of the menu bar.

The operations perspective provides general meta information such as name, status, id, etc. Also, there is externally exposed machine port information in the server tab section. Exposed ports are used in in various ways such as [priview urls]({{base}}{{site.links["ide-previews"]}}), allowing SSH into a machine, debug server ports, and various ports used by {{site.product_formal_name}} server to interact with the machine.

![Che-machine-information.jpg]({{base}}{{site.links["Che-machine-information.jpg"]}})

### Dashboard Machine Information
Information on each machine can be viewed in the in the `Dashboard`. Information on each machine in a workspace can be viewed after clicking on the workspace name, selecting the `Runtime` tab, and clicking the expand button located next to each machine. Machine information includes source, ram, [agents]({{base}}{{site.links["ws-agents"]}}), exposed ports, and environment variables. All of these configuration items can be changed on a stopped workspace/machine. If the workspace/machine is running, the workspace will be stopped automatically. The saved configuration items will be used the next time the work space is started and will supersede any values given in the original workspace stack. Changes made to the runtime configuration will only effect the workspace and will not be saved to original stack to be create other workspaces from.

![Che-machine-information-edit.jpg]({{base}}{{site.links["Che-machine-information-edit.jpg"]}})

