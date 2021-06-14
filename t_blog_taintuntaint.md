# Usage of taint and untaint resource status feature in IBM Cloud Schematics workspace 

## User scenario

The user sets up IBM Cloud resources by using IBM Cloud Schematic workspace. Through the workspace, user generates a plan, and apply the plan to provision the resources. During the process of the provisioning the resources, users resource such as Event Streams or Cloud Databases take longer time to fully provision. The time to provision few resource such as Event Stream failed as timeout.  When the user reruns the Schematics apply action, the action reprovisions by deleting the partial provisioned resources, which frustrated the user.

## Out of the box behaviour

The IBM Cloud Provider plug-in for Terraform set eight hours timeouts when the provisioning, update, or deletion of a resource must be completed before it is considered failed. 

## User issue 

The duplicating of reprovisioning the resources such as Event Stream, was a costlier affair to the user, and frustrated the user. If the provisioning cannot complete before the timeout is reached, the IBM Cloud Provider plug-in marks the provisioning process as failed and taints the resource. However, the actual provisioning of the resource is not canceled as the resource is tainted, the resource is automatically deleted and re-creates when you run the next Schematics apply action.

## Solution

Is to untaint the resources and then run apply again to get a successful apply result. If there is any additional config to be performed in the resource, it would get applied on the second run instead of reprovisioning all the resources.

Following steps showcases the implementation of the solution by using API, and command line.

**Prerequisites**

- Make sure that you have access to a paid IBM Cloud account. 
- Learn more [about IBM Cloud Schematics](/docs/schematics?topic=schematics-about-schematics). 
- Familiarize yourself with the [key terms](/docs/schematics?topic=schematics-about-schematics#schematics-terms), such as workspaces, Terraform templates, and resources. 
- Find the [IBM Cloud resources](https://ibm-cloud.github.io/tf-ibm-docs/?cm_mc_uid=71885624553615726236960&cm_mc_sid_50200000=38067391572884797521) that you want to create with {{site.data.keyword.bplong_notm}}.
- Use [sample Terraform templates](https://github.com/IBM-Cloud/terraform-provider-ibm/tree/master/examples) as a basis for your own Terraform template.  
- Review how to [create a Terraform template and properly store it](/docs/schematics?topic=schematics-create-tf-config). 
- Plan your [workspace structure](/docs/schematics?topic=schematics-workspace-setup#structure-workspace). 
- Learn how to use IBM Cloud Schematics to [manage the lifecycle](/docs/schematics?topic=schematics-manage-lifecycle) of your resources. 
a. User authentication to acces IBM Cloud
b. Authorization and refresh_token details. Execute vi ~/.bluemix/config.json in the terminal to view the authorization and refresh_token.

**API implementation**

The end to end flow for the provisioning resources by using any RESTAPI client application are:
1. [Create the workpace](https://cloud.ibm.com/apidocs/schematics#create-workspace).
2. [Get the workspace ID](https://cloud.ibm.com/apidocs/schematics#get-workspace).
3. Generate an [apply action](https://cloud.ibm.com/apidocs/schematics#apply-workspace-command).
4. Check the [activity details](https://cloud.ibm.com/apidocs/schematics#get-workspace-activity) to see the plan action are completed.
5. [Apply plan action](https://cloud.ibm.com/apidocs/schematics#plan-workspace-command) to the workspace.
6. [Fetch the activity ID](https://cloud.ibm.com/apidocs/schematics#get-workspace-activity) of the applied plan by using the get operation.
7. Finally, your resources are provisioned only when both plan and apply are successfully executed.

**Command line implementation**

For the end to end flow for provisioning resources by using command line, see [How to fix by using command line?](https://cloud.ibm.com/docs/schematics?topic=schematics-tainted-resources). The image shows the sample command line for displaying the taint and untaint status, 
Wait for the taint to complete and execute the state list command

![Sample command line for taint and untaint status](/images/cli_taint.png)


## Reference

Review the following links to find more information about Schematics taint and untaint commands.

- [IBM Cloud Schematics documentation](https://cloud.ibm.com/docs/schematics?topic=schematics-schematics-cli-reference)
- [Troubleshooting guide for apply failure](https://cloud.ibm.com/docs/schematics?topic=schematics-nullresource-errors)
- [Troubleshooting guide for taint and untaint](https://cloud.ibm.com/docs/schematics?topic=schematics-tainted-resources)

## Getting help

For help and support for using this feature in IBM Cloud Schematics, see [Getting help and support](https://cloud.ibm.com/docs/schematics?topic=schematics-schematics-help).
