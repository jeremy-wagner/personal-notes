# Automate deployment and configuration of resources 

[Azure Resource Manager - Azure Docs](https://docs.microsoft.com/en-us/azure/azure-resource-manager/)

## save a deployment as an Azure Resource Manager template

[Download the template for a VM](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/download-template)

## modify Azure Resource Manager template

- [Extend Azure Resource Manager template functionality](https://docs.microsoft.com/en-us/azure/architecture/building-blocks/extending-templates)
- [Tutorial: Create and deploy your first Azure Resource Manager template](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

### [Azure Resource Manager templates overview](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/overview)

#### Template file

Within your template, you can write template expressions that extend the capabilities of JSON. These expressions make use of the functions provided by Resource Manager.

The template has the following sections:

- Parameters - Provide values during deployment that allow the same template to be used with different environments.
- Variables - Define values that are reused in your templates. They can be constructed from parameter values.
- User-defined functions - Create customized functions that simplify your template.
- Resources - Specify the resources to deploy.
- Outputs - Return values from the deployed resources.

#### Template deployment process

When you deploy a template, Resource Manager converts the template into REST API operations. For example, when Resource Manager receives a template with the following resource definition:

    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2016-01-01",
        "name": "mystorageaccount",
        "location": "westus",
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": {}
      }
    ]

It converts the definition to the following REST API operation, which is sent to the Microsoft.Storage resource provider:

    PUT
    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
    REQUEST BODY
    {
      "location": "westus",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    }

#### [Template Design](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/overview#template-design)

You don't have to define your entire infrastructure in a single template. Often, it makes sense to divide your deployment requirements into a set of targeted, purpose-specific templates. You can easily reuse these templates for different solutions. To deploy a particular solution, you create a master template that links all the required templates. The following image shows how to deploy a three tier solution through a parent template that includes three nested templates.

If you envision your tiers having separate lifecycles, you can deploy your three tiers to separate resource groups. Notice the resources can still be linked to resources in other resource groups.

## evaluate location of new resources

### [Set resource location in Resource Manager template](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/resource-location)

### [Conditional deployment in Resource Manager templates](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/conditional-resource-deployment)

Sometimes you need to optionally deploy a resource in an Azure Resource Manager (ARM) template. Use the condition element to specify whether the resource is deployed. The value for this element resolves to true or false. When the value is true, the resource is created. When the value is false, the resource isn't created. The value can only be applied to the whole resource.

#### New or existing resource

You can use conditional deployment to create a new resource or use an existing one. The following example shows how to use condition to deploy a new storage account or use an existing storage account.

    {
      "condition": "[equals(parameters('newOrExisting'),'new')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2017-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {}
    }

When the parameter **newOrExisting** is set to **new**, the condition evaluates to true. The storage account is deployed. However, when **newOrExisting** is set to **existing**, the condition evaluates to false and the storage account isn't deployed.

For a complete example template that uses the condition element, see VM with a new or existing Virtual Network, Storage, and Public IP.

#### Allow condition

You can pass in a parameter value that indicates whether a condition is allowed. The following example deploys a SQL server and optionally allows Azure IPs.

    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "version": "12.0"
      },
      "resources": [
        {
          "condition": "[parameters('allowAzureIPs')]",
          "type": "firewallRules",
          "apiVersion": "2015-05-01-preview",
          "name": "AllowAllWindowsAzureIps",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "properties": {
            "endIpAddress": "0.0.0.0",
            "startIpAddress": "0.0.0.0"
          }
        }
      ]
    }

#### Runtime functions

If you use a reference or list function with a resource that is conditionally deployed, the function is evaluated even if the resource isn't deployed. You get an error if the function refers to a resource that doesn't exist.

Use the if function to make sure the function is only evaluated for conditions when the resource is deployed. See the if function for a sample template that uses if and reference with a conditionally deployed resource.

You set a resource as dependent on a conditional resource exactly as you would any other resource. When a conditional resource isn't deployed, Azure Resource Manager automatically removes it from the required dependencies.

## configure a virtual disk template

### [Create a VM from a VHD by using the Azure portal](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/create-vm-specialized-portal)

There are several ways to create a virtual machine (VM) in Azure:

- If you already have a virtual hard disk (VHD) to use or you want to copy the VHD from an existing VM to use, you can create a new VM by attaching the VHD to the new VM as an OS disk.
- You can create a new VM from the VHD of a VM that has been deleted. For example, if you have an Azure VM that isn't working correctly, you can delete the VM and use its VHD to create a new VM. You can either reuse the same VHD or create a copy of the VHD by creating a snapshot and then creating a new managed disk from the snapshot. Although creating a snapshot takes a few more steps, it preserves the original VHD and provides you with a fallback.
- Take a classic VM and use the VHD to create a new VM that uses the Resource Manager deployment model and managed disks. For the best results, Stop the classic VM in the Azure portal before creating the snapshot.
- You can create an Azure VM from an on-premises VHD by uploading the on-premises VHD and attaching it to a new VM. You use PowerShell or another tool to upload the VHD to a storage account, and then you create a managed disk from the VHD. For more information, see Upload a specialized VHD.

Don't use a specialized disk if you want to create multiple VMs. Instead, for larger deployments, create an image and then use that image to create multiple VMs.

We recommend that you limit the number of concurrent deployments to 20 VMs from a single snapshot or VHD.

### Copy a disk

Create a snapshot and then create a disk from the snapshot. This strategy allows you to keep the original VHD as a fallback:

1. From the Azure portal, on the left menu, select All services.
2. In the All services search box, enter disks and then select Disks to display the list of available disks.
3. Select the disk that you would like to use. The Disk page for that disk appears.
4. From the menu at the top, select Create snapshot.
5. Enter a Name for the snapshot.
6. Choose a Resource group for the snapshot. You can use either an existing resource group or create a new one.
7. For Account type, choose either Standard (HDD) or Premium (SSD) storage.
8. When you're done, select Create to create the snapshot.
9. After the snapshot has been created, select Create a resource in the left menu.
10. In the search box, enter managed disk and then select Managed Disks from the list.
11. On the Managed Disks page, select Create.
12. Enter a Name for the disk.
13. Choose a Resource group for the disk. You can use either an existing resource group or create a new one. This selection will also be used as the resource group where you create the VM from the disk.
14. For Account type, choose either Standard (HDD) or Premium (SSD) storage.
15. In Source type, ensure Snapshot is selected.
16. In the Source snapshot drop-down, select the snapshot you want to use.
17. Make any other adjustments as needed and then select Create to create the disk.

### Create a VM from a disk

After you have the managed disk VHD that you want to use, you can create the VM in the portal:

1. From the Azure portal, on the left menu, select All services.
2. In the All services search box, enter disks and then select Disks to display the list of available disks.
3. Select the disk that you would like to use. The Disk page for that disk opens.
4. In the Overview page, ensure that DISK STATE is listed as Unattached. If it isn't, you might need to either detach the disk from the VM or delete the VM to free up the disk.
5. In the menu at the top of the page, select Create VM.
6. On the Basics page for the new VM, enter a Virtual machine name and either select an existing Resource group or create a new one.
7. For Size, select Change size to access the Size page.
8. Select a VM size row and then choose Select.
9. On the Networking page, you can either let the portal create all new resources or you can select an existing Virtual network and Network security group. The portal always creates a new network interface and public IP address for the new VM.
10. On the Management page, make any changes to the monitoring options.
11. On the Guest config page, add any extensions as needed.
12. When you're done, select Review + create.
13. If the VM configuration passes validation, select Create to start the deployment.

## deploy from a template

[Download the template for a VM](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/download-template)

## manage a template library

### [Azure Resource Manager templates overview](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/overview)

### [Using linked and nested templates when deploying Azure resources](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/linked-templates)

## create and execute an automation runbook

### [Start a runbook in Azure Automation](https://docs.microsoft.com/en-us/azure/automation/start-runbooks)

The following table helps you determine the method to start a runbook in Azure Automation that is most suitable to your particular scenario. This article includes details on starting a runbook with the Azure portal and with Windows PowerShell. Details on the other methods are provided in other documentation that you can access from the links below.

#### [Azure Portal](https://docs.microsoft.com/en-us/azure/automation/start-runbooks#start-a-runbook-with-the-azure-portal)

- Simplest method with interactive user interface.
- Form to provide simple parameter values.
- Easily track job state.
- Access authenticated with Azure sign in.

#### [Windows Powershell](https://docs.microsoft.com/en-us/powershell/module/azurerm.automation/start-azurermautomationrunbook)

- Call from command line with Windows PowerShell cmdlets.
- Can be included in automated feature with multiple steps.
- Request is authenticated with certificate or OAuth user principal / service principal.
- Provide simple and complex parameter values.
- Track job state.
- Client required to support PowerShell cmdlets.

#### [Azure Automation API](https://docs.microsoft.com/en-us/rest/api/automation/)

- Most flexible method but also most complex.
- Call from any custom code that can make HTTP requests.
- Request authenticated with certificate, or Oauth user principal / service principal.
- Provide simple and complex parameter values. If you're calling a Python runbook using the API, the JSON payload must be serialized.
- Track job state.

#### [Webhooks](https://docs.microsoft.com/en-us/azure/automation/automation-webhooks)

- Start runbook from single HTTP request.
- Authenticated with security token in URL.
- Client can't override parameter values specified when webhook created. Runbook can define single parameter that is populated with the HTTP request details.
- No ability to track job state through webhook URL.

#### [Respond to Azure Alert](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-overview)

- Start a runbook in response to Azure alert.
- Configure webhook for runbook and link to alert.
- Authenticated with security token in URL.

#### [Schedule](https://docs.microsoft.com/en-us/azure/automation/shared-resources/schedules)

- Automatically start runbook on hourly, daily, weekly, or monthly schedule.
- Manipulate schedule through Azure portal, PowerShell cmdlets, or Azure API.
- Provide parameter values to be used with schedule.

#### [From Another Runbook](https://docs.microsoft.com/en-us/azure/automation/automation-child-runbooks)

- Use a runbook as an activity in another runbook.
- Useful for functionality used by multiple runbooks.
- Provide parameter values to child runbook and use output in parent runbook.

#### Work with runbook parameters

When you start a runbook from the Azure portal or Windows PowerShell, the instruction is sent through the Azure Automation web service. This service doesn't support parameters with complex data types. If you need to provide a value for a complex parameter, then you must call it inline from another runbook as described in [Child runbooks in Azure Automation](https://docs.microsoft.com/en-us/azure/automation/automation-child-runbooks).

The Azure Automation web service provides special functionality for parameters using certain data types as described in the following sections:

- [Named values](https://docs.microsoft.com/en-us/azure/automation/start-runbooks#named-values)
- [Arrays](https://docs.microsoft.com/en-us/azure/automation/start-runbooks#arrays)
- [Credentials](https://docs.microsoft.com/en-us/azure/automation/start-runbooks#credentials)

#### Start a runbook with the Azure portal

1. In the Azure portal, select Automation and then click the name of an Automation account.
2. On the Hub menu, select Runbooks.
3. On the Runbooks page, select a runbook, and then click Start.
4. If the runbook has parameters, you're prompted to provide values with a text box for each parameter. For more information on parameters, see Runbook Parameters.
5. On the Job pane, you can view the status of the runbook job.

#### Start a runbook with PowerShell

You can use the `Start-AzAutomationRunbook` to start a runbook with Windows PowerShell. The [following sample code](https://docs.microsoft.com/en-us/azure/automation/start-runbooks#start-a-runbook-with-powershell) starts a runbook called Test-Runbook.
