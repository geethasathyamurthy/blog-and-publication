# Timeout failures results as tainted or untainted of IBM Cloud resources

## User scenario

The user sets up IBM Cloud resources by using IBM Cloud Schematic workspace. Through the workspace, user generates a plan, and apply the plan to provision the resources. During the process of the provisioning the resources, users resource such as Event Streams or Cloud Databases take longer time to fully provision. The time to provision few resource such as Event Stream failed as timeout.  When the user reruns the Schematics apply action, the action reprovisions by deleting the partial provisioned resources, which frustrated the user.

## Out of the box behaviour.
The IBM Cloud Provider plug-in for Terraform set eight hours timeouts when the provisioning, update, or deletion of a resource must be completed before it is considered failed. 

## User issue 
The duplicating of reprovisioning the resources such as Event Stream, was a costlier affair to the user, and frustrated the user.

## Experitise solution
If the provisioning cannot be completed before the timeout is reached, the IBM Cloud Provider plug-in marks the provisioning process as failed and taints the resource. However, the actual provisioning of the resource is not canceled and continues in the background which can result in a successfully provisioned resource after all. Because the resource is tainted, the resource is automatically deleted and re-created when you run the next Schematics apply action.

Following steps showcases the implmentation by using API, command line and the console.

**Prerequisites**
a. User authentication to acces IBM Cloud
b. Authorization and refresh_token details. Execute vi ~/.bluemix/config.json in the terminal to view the authorization and refresh_token.

**API implementation**

The end to end flow for the provisioning resources by using any RESTAPI client application are:
1. [Create the workpace](https://cloud.ibm.com/apidocs/schematics#create-workspace).
2. [Get the workspace ID](https://cloud.ibm.com/apidocs/schematics#get-workspace).
3. Generate an [apply action](https://cloud.ibm.com/apidocs/schematics#apply-workspace-command).
4. [Apply plan action](https://cloud.ibm.com/apidocs/schematics#plan-workspace-command) to the workspace.
5. [Fetch the activity ID](https://cloud.ibm.com/apidocs/schematics#refresh-workspace-command) of the applied plan by using the get operation
6. Perform the get operation with the workspace activity ID.
7. Finally, apply resources only when both plan and apply are successfully executed.

**Command line implementation**

For the end to end flow for provising resource by using command line, see [How to fix by using command line?](https://cloud.ibm.com/docs/schematics?topic=schematics-tainted-resources).

!(Sample command line for taint and untaint status)[/images/cli_taint.png]

**What's next**

You can also create a script to automate the process of creating the workspace till provisioning the resources.

**support**

**Author**
