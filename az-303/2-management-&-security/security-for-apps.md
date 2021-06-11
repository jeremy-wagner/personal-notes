# Manage security for applications

## implement and configure KeyVault

[What is Azure Key Vault?](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-overview)

Azure Key Vault helps solve the following problems:

- **Secrets Management** - Azure Key Vault can be used to Securely store and tightly control access to tokens, passwords, certificates, API keys, and other secrets
- **Key Management** - Azure Key Vault can also be used as a Key Management solution. Azure Key Vault makes it easy to create and control the encryption keys used to encrypt your data.
- **Certificate Management** - Azure Key Vault is also a service that lets you easily provision, manage, and deploy public and private Transport Layer Security/Secure Sockets Layer (TLS/SSL) certificates for use with Azure and your internal connected resources.

Azure Key Vault has two service tiers: Standard, which encrypts with a software key, and a Premium tier, which includes HSM-protected keys. To see a comparison between the Standard and Premium tiers, see the Azure Key Vault pricing page.

### Why use Azure Key Vault?

#### Centralize application secrets

Centralizing storage of application secrets in Azure Key Vault allows you to control their distribution. Key Vault greatly reduces the chances that secrets may be accidentally leaked. When using Key Vault, application developers no longer need to store security information in their application. Not having to store security information in applications eliminates the need to make this information part of the code. For example, an application may need to connect to a database. Instead of storing the connection string in the app's code, you can store it securely in Key Vault.

Your applications can securely access the information they need by using URIs. These URIs allow the applications to retrieve specific versions of a secret. There is no need to write custom code to protect any of the secret information stored in Key Vault.

#### Securely store secrets and keys

Access to a key vault requires proper authentication and authorization before a caller (user or application) can get access. Authentication establishes the identity of the caller, while authorization determines the operations that they are allowed to perform.

Authentication is done via Azure Active Directory. Authorization may be done via Azure role-based access control (Azure RBAC) or Key Vault access policy. Azure RBAC is used when dealing with the management of the vaults and key vault access policy is used when attempting to access data stored in a vault.

Azure Key Vaults may be either software-protected or, with the Azure Key Vault Premium tier, hardware-protected by hardware security modules (HSMs). Software-protected keys, secrets, and certificates are safeguarded by Azure, using industry-standard algorithms and key lengths. For situations where you require added assurance, you can import or generate keys in HSMs that never leave the HSM boundary. Azure Key Vault uses nCipher HSMs, which are Federal Information Processing Standards (FIPS) 140-2 Level 2 validated. You can use nCipher tools to move a key from your HSM to Azure Key Vault.

Finally, Azure Key Vault is designed so that Microsoft does not see or extract your data.

#### Monitor access and use

Once you have created a couple of Key Vaults, you will want to monitor how and when your keys and secrets are being accessed. You can monitor activity by enabling logging for your vaults. You can configure Azure Key Vault to:

- Archive to a storage account.
- Stream to an event hub.
- Send the logs to Azure Monitor logs.

You have control over your logs and you may secure them by restricting access and you may also delete logs that you no longer need.

#### Simplified administration of application secrets

When storing valuable data, you must take several steps. Security information must be secured, it must follow a life cycle, and it must be highly available. Azure Key Vault simplifies the process of meeting these requirements by:

- Removing the need for in-house knowledge of Hardware Security Modules.
- Scaling up on short notice to meet your organization's usage spikes.
- Replicating the contents of your Key Vault within a region and to a secondary region. Data replication ensures high availability and takes away the need of any action from the administrator to trigger the failover.
- Providing standard Azure administration options via the portal, Azure CLI and PowerShell.
- Automating certain tasks on certificates that you purchase from Public CAs, such as enrollment and renewal.

In addition, Azure Key Vaults allow you to segregate application secrets. Applications may access only the vault that they are allowed to access, and they can be limited to only perform specific operations. You can create an Azure Key Vault per application and restrict the secrets stored in a Key Vault to a specific application and team of developers.

#### Integrate with other Azure services

As a secure store in Azure, Key Vault has been used to simplify scenarios like:

- Azure Disk Encryption
- The always encrypted and Transparent Data Encryption functionality in SQL server and Azure SQL Database
- Azure App Service.

Key Vault itself can integrate with storage accounts, event hubs, and log analytics.

[About keys, secrets, and certificates](https://docs.microsoft.com/en-us/azure/key-vault/about-keys-secrets-and-certificates)

Azure Key Vault enables Microsoft Azure applications and users to store and use several types of secret/key data. Key Vault resource provider supports two resource types: vaults and managed HSMs.

### DNS suffixes for base URL

The table below shows the base URL DNS suffix used by the data-plane endpoint for vaults and managed HSM pools in various cloud environments.

|Cloud environment	|DNS suffix for vaults	|DNS suffix for managed HSMs|
|:--|:--|:--|
|Azure Cloud	|.vault.azure.net	|.managedhsm.azure.net|
|Azure China Cloud	|.vault.azure.cn	|Not supported|
|Azure US Government	|.vault.usgovcloudapi.net	|Not supported|
|Azure German Cloud	|.vault.microsoftazure.de	|Not supported|

### Object types

The table below shows object types and their suffixes in the base URL.

|Object type	|URL Suffix	Vaults	Managed HSM Pools|
|HSM-protected keys	|/keys	|Supported	|Supported|
|Software-protected keys	|/keys	|Supported	|Not supported|
|Secrets	|/secrets|	Supported	|Not supported|
|Certificates	|/certificates	|Supported	|Not supported|
|Storage account keys	|/storageaccount|	Supported	|Not supported|

- **Cryptographic keys**: Supports multiple key types and algorithms, and enables the use of software-protected and HSM-protected keys. For more information, see About keys.
- **Secrets**: Provides secure storage of secrets, such as passwords and database connection strings. For more information, see About secrets.
- **Certificates**: Supports certificates, which are built on top of keys and secrets and add an automated renewal feature. For more information, see About certificates.
- **Azure Storage account keys**: Can manage keys of an Azure Storage account for you. Internally, Key Vault can list (sync) keys with an Azure Storage Account, and regenerate (rotate) the keys periodically. For more information, see Manage storage account keys with Key Vault.

For more general information about Key Vault, see About Azure Key Vault. For more information about Managed HSM pools, see What is Azure Key Vault Managed HSM?

### Objects, identifiers, and versioning

Objects stored in Key Vault are versioned whenever a new instance of an object is created. Each version is assigned a unique identifier and URL. When an object is first created, it's given a unique version identifier and marked as the current version of the object. Creation of a new instance with the same object name gives the new object a unique version identifier, causing it to become the current version.

Objects in Key Vault can be addressed by specifying a version or by omitting version for operations on current version of the object. For example, given a Key with the name `MasterKey`, performing operations without specifying a version causes the system to use the latest available version. Performing operations with the version-specific identifier causes the system to use that specific version of the object.

### Vault-name and Object-name

Objects are uniquely identified within Key Vault using a URL. No two objects in the system have the same URL, regardless of geo-location. The complete URL to an object is called the Object Identifier. The URL consists of a prefix that identifies the Key Vault, object type, user provided Object Name, and an Object Version. The Object Name is case-insensitive and immutable. Identifiers that don't include the Object Version are referred to as Base Identifiers.

For more information, see [Authentication, requests, and responses](https://docs.microsoft.com/en-us/azure/key-vault/general/authentication-requests-and-responses)

An object identifier has the following general format (depending on container type):

For Vaults: `https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`

For Managed HSM pools: `https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`

> Note
>
> See Object type support for types of objects supported by each container type.

Where:

|Element	|Description|
|:--|:--|
|`vault-name` or `hsm-name`	|The name for a vault or an Managed HSM pool in the Microsoft Azure Key Vault service.<br />Vault names and Managed HSM pool names are selected by the user and are globally unique.<br />Vault name and Managed HSM pool name must be a 3-24 character string, containing only 0-9, a-z, A-Z, and -.|
|`object-type`	|The type of the object, "keys", "secrets", or 'certificates'.|
|`object-name`	|An `object-name` is a user provided name for and must be unique within a Key Vault. The name must be a 1-127 character string, starting with a letter and containing only 0-9, a-z, A-Z, and -.|
|`object-version`	|An `object-version` is a system-generated, 32 character string identifier that is optionally used to address a unique version of an object.|

## implement and configure Azure AD Managed Identities

[What are managed identities for Azure resources?](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)

A common challenge for developers is the management of secrets and credentials to secure communication between different services. On Azure, managed identities eliminate the need for developers having to manage credentials by providing an identity for the Azure resource in Azure AD and using it to obtain Azure Active Directory (Azure AD) tokens. This also helps accessing Azure Key Vault where developers can store credentials in a secure manner. Managed identities for Azure resources solves this problem by providing Azure services with an automatically managed identity in Azure AD.

Here are some of the benefits of using Managed identities:

- You don't need to manage credentials. Credentials are not even accessible to you.
- You can use managed identities to authenticate to any Azure service that supports Azure AD authentication including Azure Key Vault.
- Managed identities can be used without any additional cost.

> Note
>
> Managed identities for Azure resources is the new name for the service formerly known as Managed Service Identity (MSI).

### Managed identity types

There are two types of managed identities:

- **System-assigned** Some Azure services allow you to enable a managed identity directly on a service instance. When you enable a system-assigned managed identity an identity is created in Azure AD that is tied to the lifecycle of that service instance. So when the resource is deleted, Azure automatically deletes the identity for you. By design, only that Azure resource can use this identity to request tokens from Azure AD.
- **User-assigned** You may also create a managed identity as a standalone Azure resource. You can create a user-assigned managed identity and assign it to one or more instances of an Azure service. In the case of user-assigned managed identities, the identity is managed separately from the resources that use it.

The table below shows the differences between the two types of managed identities.

|Property	|System-assigned managed identity	|User-assigned managed identity|
|:--|:--|:--|
|Creation	|Created as part of an Azure resource (for example, an Azure virtual machine or Azure App Service)	|Created as a stand-alone Azure resource|
|Life cycle	|Shared life cycle with the Azure resource that the managed identity is created with.<br />|When the parent resource is deleted, the managed identity is deleted as well.	|Independent life cycle.<br />Must be explicitly deleted.|
|Sharing across Azure resources	|Cannot be shared.<br />It can only be associated with a single Azure resource.	|Can be shared<br />The same user-assigned managed identity can be associated with more than one Azure resource.|
|Common use cases	|Workloads that are contained within a single Azure resource<br />Workloads for which you need independent identities.<br />For example, an application that runs on a single virtual machine|Workloads that run on multiple resources and which can share a single identity.<br />Workloads that need pre-authorization to a secure resource as part of a provisioning flow.<br />Workloads where resources are recycled frequently, but permissions should stay consistent.<br />For example, a workload where multiple virtual machines need to access the same resource|

> Important
>
> Regardless of the type of identity chosen a managed identity is a service principal of a special type that may only be used with Azure resources. When the managed identity is deleted, the corresponding service principal is automatically removed.

### How can I use managed identities for Azure resources?

![some examples of how a developer may use managed identities to get access to resources from their code without managing authentication information](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/media/overview/azure-managed-identities-examples.png)

### What Azure services support the feature?

Managed identities for Azure resources can be used to authenticate to services that support Azure AD authentication. For a list of Azure services that support the managed identities for Azure resources feature, see Services that support managed identities for Azure resources.

[Use a Windows VM system-assigned managed identity to access Resource Manager](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm)

Managed identities for Azure resources is a feature of Azure Active Directory. Each of the Azure services that support managed identities for Azure resources are subject to their own timeline. Make sure you review the availability status of managed identities for your resource and known issues before you begin.

This quickstart shows you how to access the Azure Resource Manager API using a Windows virtual machine with system-assigned managed identity enabled. Managed identities for Azure resources are automatically managed by Azure and enable you to authenticate to services that support Azure AD authentication without needing to insert credentials into your code. You learn how to:

- &#x2611; Grant your VM access to a Resource Group in Azure Resource Manager
- &#x2611; Get an access token using the VM identity and use it to call Azure Resource Manager

### Prerequisites

- If you're not familiar with the managed identities for Azure resources feature, see this [overview](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview). If you don't have an Azure account, sign up for a free account before you continue.
- To perform the required resource creation and role management, your account needs "Owner" permissions at the appropriate scope (your subscription or resource group). If you need assistance with role assignment, see [Use Role-Based Access Control to manage access to your Azure subscription resources](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal).

### Grant your VM access to a resource group in Resource Manager

Using managed identities for Azure resources, your code can get access tokens to authenticate to resources that support Azure AD authentication. The Azure Resource Manager supports Azure AD authentication. First, we need to grant this VM’s system-assigned managed identity access to a resource in Resource Manager, in this case the Resource Group in which the VM is contained.

1. Navigate to the tab for Resource Groups.
2. Select the specific Resource Group you created for your Windows VM.
3. Go to Access control (IAM) in the left panel.
4. Then Add role assignment a new role assignment for your Windows VM. Choose Role as Reader.
5. In the next drop-down, Assign access to the resource Virtual Machine.
6. Next, ensure the proper subscription is listed in the Subscription dropdown. And for Resource Group, select All resource groups.
7. Finally, in Select choose your Windows VM in the dropdown and click Save.

![Alt image text](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

### Get an access token using the VM's system-assigned managed identity and use it to call Azure Resource Manager

You will need to use PowerShell in this portion. If you don’t have PowerShell installed, download it here.

1. In the portal, navigate to Virtual Machines and go to your Windows virtual machine and in the Overview, click Connect.
2. Enter in your Username and Password for which you added when you created the Windows VM.
3. Now that you have created a Remote Desktop Connection with the virtual machine, open PowerShell in the remote session.
4. Using the Invoke-WebRequest cmdlet, make a request to the local managed identity for Azure resources endpoint to get an access token for Azure Resource Manager.

#### PowerShell

    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
 
> Note
>
> The value of the "resource" parameter must be an exact match for what is expected by Azure AD. When using the Azure Resource Manager resource ID, you must include the trailing slash on the URI.

Next, extract the full response, which is stored as a JavaScript Object Notation (JSON) formatted string in the $response object.

#### PowerShell

    $content = $response.Content | ConvertFrom-Json

Next, extract the access token from the response.

#### PowerShell

    $ArmToken = $content.access_token

Finally, call Azure Resource Manager using the access token. In this example, we're also using the Invoke-WebRequest cmdlet to make the call to Azure Resource Manager, and include the access token in the Authorization header.

#### PowerShell

    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
 
> Note
>
> The URL is case-sensitive, so ensure if you are using the exact same case as you used earlier when you named the Resource Group, and the uppercase "G" in "resourceGroups."

The following command returns the details of the Resource Group:

#### PowerShell

    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}

### Next steps

In this quickstart, you learned how to use a system-assigned managed identity to access the Azure Resource Manager API. To learn more about Azure Resource Manager see: [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/overview)

## register and manage applications in Azure AD 

[Tutorial: Register an application in Azure Active Directory B2C](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-register-applications?tabs=applications)

Before your applications can interact with Azure Active Directory B2C (Azure AD B2C), they must be registered in a tenant that you manage. This tutorial shows you how to register a web application using the Azure portal.

A "web application" refers to a traditional web application that performs most of the application logic on the server. They may be built using frameworks like ASP.NET Core, Maven (Java), Flask (Python), and Express (Node.js).

> Important
>
> If you're using a single-page application ("SPA") instead (e.g. using Angular, Vue, or React), [learn how to register a single-page application](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-register-spa).

If you're using a native app instead (e.g. iOS, Android, mobile & desktop), [learn how to register a native client application](https://docs.microsoft.com/en-us/azure/active-directory-b2c/add-native-application).

### Register a web application

To register a web application in your Azure AD B2C tenant, you can use our new unified App registrations experience or our legacy Applications (Legacy) experience. Learn more about the new experience.

#### App registrations

1. Sign in to the Azure portal.
2. Select the Directory + Subscription icon in the portal toolbar, and then select the directory that contains your Azure AD B2C tenant.
3. In the Azure portal, search for and select Azure AD B2C.
4. Select App registrations, and then select New registration.
5. Enter a Name for the application. For example, webapp1.
6. Under Supported account types, select Accounts in any identity provider or organizational directory (for authenticating users with user flows).
7. Under Redirect URI, select Web, and then enter https://jwt.ms in the URL text box.
  The redirect URI is the endpoint to which the user is sent by the authorization server (Azure AD B2C, in this case) after completing its interaction with the user, and to which an access token or authorization code is sent upon successful authorization. In a production application, it's typically a publicly accessible endpoint where your app is running, like https://contoso.com/auth-response. For testing purposes like this tutorial, you can set it to https://jwt.ms, a Microsoft-owned web application that displays the decoded contents of a token (the contents of the token never leave your browser). During app development, you might add the endpoint where your application listens locally, like https://localhost:5000. You can add and modify redirect URIs in your registered applications at any time.

  The following restrictions apply to redirect URIs:

  - The reply URL must begin with the scheme https.
  - The reply URL is case-sensitive. Its case must match the case of the URL path of your running application. For example, if your application includes as part of its path .../abc/response-oidc, do not specify .../ABC/response-oidc in the reply URL. Because the web browser treats paths as case-sensitive, cookies associated with .../abc/response-oidc may be excluded if redirected to the case-mismatched .../ABC/response-oidc URL.
8. Under Permissions, select the Grant admin consent to openid and offline_access permissions check box.
9. Select Register.

### Create a client secret

For a web application, you need to create an application secret. This secret will be used by your application to exchange an authorization code for an access token.

#### App registrations

1. In the Azure AD B2C - App registrations page, select the application you created, for example webapp1.
2. In the left menu, under Manage, select Certificates & secrets.
3. Select New client secret.
4. Enter a description for the client secret in the Description box. For example, clientsecret1.
5. Under Expires, select a duration for which the secret is valid, and then select Add.
6. Record the secret's Value. You use this value as the application secret in your application's code.
