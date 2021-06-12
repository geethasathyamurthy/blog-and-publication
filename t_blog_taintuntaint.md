# Timeout failures results as tainted or untainted of IBM Cloud resources

The user sets up IBM Cloud resources by using IBM Cloud Schematic workspace. Through the workspace, user generates a plan, and apply the plan to execute the Terraform for provisioning the resources. During the process of the provisioning the resources, the Terraform apply takes a longer time to fully provision, such as Event Streams or Cloud Databases and timeouts. When the user reruns the Schematics apply action, the action fails due to timeouts resulting in a tainted resource. By default the IBM Cloud Schematics apply plan is set to timeout every 8 hours during provisioning the resources.

The recovery of the resources during the re-executing the plan timeout of 8 hours is reprovisioning the resources such as Event Streams, Cloud Databases, from the start as all the resources were marked as tainted. The duplicating of the reprovisioning the resources, was a costlier affair and dissatisfied the user.

The IBM Cloud Provider plug-in for Terraform sets certain timeouts when the provisioning, update, or deletion of a resource must be completed before it is considered failed. Because some resources such as Event Streams or Cloud Databases take longer to fully provision, they might exceed these timeouts. If the provisioning cannot be completed before the timeout is reached, the IBM Cloud Provider plug-in marks the provisioning process as failed and taints the resource. However, the actual provisioning of the resource is not canceled and continues in the background which can result in a successfully provisioned resource after all. Because the resource is tainted, the resource is automatically deleted and re-created when you run the next Schematics apply action.


To avoid that a successfully provisioned resource is deleted and re-created, you must untaint the resource.

List the workspaces in your account and note the ID of the workspace that includes the failed resource.


ibmcloud schematics workspace list
Refresh your workspace. A refresh action validates the IBM Cloud resources in your account against the state that is stored in the Terraform statefile of your workspace. This process might take a few minutes to complete.


ibmcloud schematics refresh --id <workspace_ID>
Retrieve the template ID of your workspace. To template ID is shown as a string after the Template Variables for: section of your CLI output.


ibmcloud schematics workspace get --id <workspace_ID>
Retrieve the Terraform statefile for your workspace and note the name of the resource that is tainted.


ibmcloud schematics state pull --id <workspace_ID> --template <template_ID>
Verify that the tainted resource is successfully provisioned and in a healthy state by using the IBM Cloud console, CLI, or API. For example, if you tried to provision an IBM Cloud Kubernetes Service cluster, check that the cluster is in a Normal state and that you can successfully connect to the cluster.

Untaint the resource. Enter the name of the tainted resource that you retrieved from the statefile in the --address parameter. For example, a cluster resource name from a statefile might look like this: ibm_container_vpc_cluster.mycluster.


ibmcloud schematics workspace untaint --id <workspace_ID> --address <resource_name>
Retrieve the Terraform statefile for your workspace again and verify that your resource is marked as untainted.


ibmcloud schematics state pull --id <workspace_ID> --template <template_ID>

Why do timeout failures result in tainted IBM Cloud resources?
In IBM Cloud Schematics, apply executes the resource provisioning and defaults the timeout occurrence for every 8 hours. 


