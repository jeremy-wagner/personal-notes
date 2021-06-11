# Implement and manage hybrid identities

[What is Azure AD Connect?](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/whatis-azure-ad-connect)

Azure AD Connect is the Microsoft tool designed to meet and accomplish your hybrid identity goals. It provides the following features:

- Password hash synchronization - A sign-in method that synchronizes a hash of a users on-premises AD password with Azure AD.
- Pass-through authentication - A sign-in method that allows users to use the same password on-premises and in the cloud, but doesn't require the additional infrastructure of a federated environment.
- Federation integration - Federation is an optional part of Azure AD Connect and can be used to configure a hybrid environment using an on-premises AD FS infrastructure. It also provides AD FS management capabilities such as certificate renewal and additional AD FS server deployments.
- Synchronization - Responsible for creating users, groups, and other objects. As well as, making sure identity information for your on-premises users and groups is matching the cloud. This synchronization also includes password hashes.
- Health Monitoring - Azure AD Connect Health can provide robust monitoring and provide a central location in the Azure portal to view this activity.

![What is Azure AD Connect](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/media/whatis-hybrid-identity/arch.png)

### What is Azure AD Connect Health?

Azure Active Directory (Azure AD) Connect Health provides robust monitoring of your on-premises identity infrastructure. It enables you to maintain a reliable connection to Microsoft 365 and Microsoft Online Services. This reliability is achieved by providing monitoring capabilities for your key identity components. Also, it makes the key data points about these components easily accessible.

The information is presented in the Azure AD Connect Health portal. Use the Azure AD Connect Health portal to view alerts, performance monitoring, usage analytics, and other information. Azure AD Connect Health enables the single lens of health for your key identity components in one place.

![What is Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/media/whatis-hybrid-identity-health/aadconnecthealth2.png)

### Why use Azure AD Connect?

Integrating your on-premises directories with Azure AD makes your users more productive by providing a common identity for accessing both cloud and on-premises resources. Users and organizations can take advantage of:

- Users can use a single identity to access on-premises applications and cloud services such as Microsoft 365.
- Single tool to provide an easy deployment experience for synchronization and sign-in.
- Provides the newest capabilities for your scenarios. Azure AD Connect replaces older versions of identity integration tools such as DirSync and Azure AD Sync. For more information, see [Hybrid Identity directory integration tools comparison](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison).

## install and configure Azure AD Connect

**Important Link, contains a lot of information**: [Custom installation of Azure AD Connect](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-install-custom)

[Select which installation type to use for Azure AD Connect](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-install-select-installation)

Azure AD Connect has two installation types for new installation: Express and customized. This topic helps you to decide which option to use during installation.

### Express

Express is the most common option and is used by about 90% of all new installations. It was designed to provide a configuration that works for the most common customer scenarios.

It assumes:

- You have a single Active Directory forest on-premises.
- You have an enterprise administrator account you can use for the installation.
- You have less than 100,000 objects in your on-premises Active Directory.

You get:

- Password hash synchronization from on-premises to Azure AD for single sign-on.
- A configuration that synchronizes users, groups, contacts, and Windows 10 computers.
- Synchronization of all eligible objects in all domains and all OUs.
- Automatic upgrade is enabled to make sure you always use the latest available version.

Options where you can still use Express:

- If you do not want to synchronize all OUs, you can still use Express and on the last page, unselect **Start the synchronization process...\***. Then run the installation wizard again and change the OUs in configuration options and enable scheduled sync.
- You want to enable one of the features in Azure AD Premium, such as Password writeback. First go through express to get the initial installation completed. Then run the installation wizard again and change the configuration options.

### Custom

The customized path allows many more options than express. It should be used in all cases where the configuration described in previous section for express is not representative for your organization.

Use when:

- You do not have access to an enterprise admin account in Active Directory.
- You have more than one forest or you plan to synchronize more than one forest in the future.
- You have domains in your forest not reachable from the Connect server.
- You plan to use federation or pass-through authentication for user sign-in.
- You have more than 100,000 objects and need to use a full SQL Server.
- You plan to use group-based filtering and not only domain or OU-based filtering.

## identity synchronization options

You need to use the links for this as they contain too much information:

[Azure AD Connect sync: Understand and customize synchronization](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-sync-whatis)
[Azure Active Directory Hybrid Identity Design Considerations](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-overview)

## configure and manage password sync and password writeback

[Implement password hash synchronization with Azure AD Connect sync](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)

This article provides information that you need to synchronize your user passwords from an on-premises Active Directory instance to a cloud-based Azure Active Directory (Azure AD) instance.

### How password hash synchronization works

The Active Directory domain service stores passwords in the form of a hash value representation, of the actual user password. A hash value is a result of a one-way mathematical function (the hashing algorithm). There is no method to revert the result of a one-way function to the plain text version of a password.

To synchronize your password, Azure AD Connect sync extracts your password hash from the on-premises Active Directory instance. Extra security processing is applied to the password hash before it is synchronized to the Azure Active Directory authentication service. Passwords are synchronized on a per-user basis and in chronological order.

The actual data flow of the password hash synchronization process is similar to the synchronization of user data. However, passwords are synchronized more frequently than the standard directory synchronization window for other attributes. The password hash synchronization process runs every 2 minutes. You cannot modify the frequency of this process. When you synchronize a password, it overwrites the existing cloud password.

The first time you enable the password hash synchronization feature, it performs an initial synchronization of the passwords of all in-scope users. You cannot explicitly define a subset of user passwords that you want to synchronize. However, if there are multiple connectors, it is possible to disable password hash sync for some connectors but not others using the [Set-ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/tutorial-configure-password-hash-sync) cmdlet.

When you change an on-premises password, the updated password is synchronized, most often in a matter of minutes. The password hash synchronization feature automatically retries failed synchronization attempts. If an error occurs during an attempt to synchronize a password, an error is logged in your event viewer.

The synchronization of a password has no impact on the user who is currently signed in. Your current cloud service session is not immediately affected by a synchronized password change that occurs, while you are signed in, to a cloud service. However, when the cloud service requires you to authenticate again, you need to provide your new password.

A user must enter their corporate credentials a second time to authenticate to Azure AD, regardless of whether they're signed in to their corporate network. This pattern can be minimized, however, if the user selects the Keep me signed in (KMSI) check box at sign-in. This selection sets a session cookie that bypasses authentication for 180 days. KMSI behavior can be enabled or disabled by the Azure AD administrator. In addition, you can reduce password prompts by turning on Seamless SSO, which automatically signs users in when they are on their corporate devices connected to your corporate network.

### Detailed description of how password hash synchronization works

The following section describes, in-depth, how password hash synchronization works between Active Directory and Azure AD.

![Detailed password flow](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/media/how-to-connect-password-hash-synchronization/arch3b.png)

### Password policy considerations

There are two types of password policies that are affected by enabling password hash synchronization:

- Password complexity policy
- Password expiration policy

#### Password complexity policy

When password hash synchronization is enabled, the password complexity policies in your on-premises Active Directory instance override complexity policies in the cloud for synchronized users. You can use all of the valid passwords from your on-premises Active Directory instance to access Azure AD services.

> Note
>
> Passwords for users that are created directly in the cloud are still subject to password policies as defined in the cloud.

#### Password expiration policy

If a user is in the scope of password hash synchronization, by default the cloud account password is set to Never Expire.

You can continue to sign in to your cloud services by using a synchronized password that is expired in your on-premises environment. Your cloud password is updated the next time you change the password in the on-premises environment.

### Account expiration

If your organization uses the accountExpires attribute as part of user account management, this attribute is not synchronized to Azure AD. As a result, an expired Active Directory account in an environment configured for password hash synchronization will still be active in Azure AD. We recommend that if the account is expired, a workflow action should trigger a PowerShell script that disables the user's Azure AD account (use the Set-AzureADUser cmdlet). Conversely, when the account is turned on, the Azure AD instance should be turned on.

### Enable password hash synchronization

> Important
>
> If you are migrating from AD FS (or other federation technologies) to Password Hash Synchronization, we highly recommend that you follow our detailed deployment guide published here.

When you install Azure AD Connect by using the Express Settings option, password hash synchronization is automatically enabled. For more information, see Getting started with Azure AD Connect using express settings.

If you use custom settings when you install Azure AD Connect, password hash synchronization is available on the user sign-in page. For more information, see Custom installation of Azure AD Connect.

![Enabling password hash synchronization](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/media/how-to-connect-password-hash-synchronization/usersignin2.png)

[Azure AD Connect: Enabling device writeback](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-device-writeback)

> A subscription to Azure AD Premium is required for device writeback.

The following documentation provides information on how to enable the device writeback feature in Azure AD Connect. Device Writeback is used in the following scenarios:

- Enable Windows Hello for Business using hybrid certificate trust deployment
- Enable Conditional Access based on devices to ADFS (2012 R2 or higher) protected applications (relying party trusts).

This provides additional security and assurance that access to applications is granted only to trusted devices. For more information on Conditional Access, see Managing Risk with Conditional Access and Setting up On-premises Conditional Access using Azure Active Directory Device Registration.

> Important
>
> - Devices must be located in the same forest as the users. Since devices must be written back to a single forest, this feature does not currently support a deployment with multiple user forests.
> - Only one device registration configuration object can be added to the on-premises Active Directory forest. This feature is not compatible with a topology where the on-premises Active Directory is synchronized to multiple Azure AD directories.

[Tutorial: Enable Azure Active Directory self-service password reset writeback to an on-premises environment](https://docs.microsoft.com/en-us/azure/active-directory/authentication/tutorial-enable-sspr-writeback)

### Prerequisites

To complete this tutorial, you need the following resources and privileges:

- A working Azure AD tenant with at least an Azure AD Premium P1 or trial license enabled.
  - If needed, create one for free.
  - For more information, see Licensing requirements for Azure AD SSPR.
- An account with global administrator privileges.
- Azure AD configured for self-service password reset.
  - If needed, complete the previous tutorial to enable Azure AD SSPR.
- An existing on-premises AD DS environment configured with a current version of Azure AD Connect.
  - If needed, configure Azure AD Connect using the Express or Custom settings.
  - To use password writeback, your Domain Controllers must be Windows Server 2012 or later.

### Enable password writeback in Azure AD Connect

One of the configuration options in Azure AD Connect is for password writeback. When this option is enabled, password change events cause Azure AD Connect to synchronize the updated credentials back to the on-premises AD DS environment.

To enable SSPR writeback, first enable the writeback option in Azure AD Connect. From your Azure AD Connect server, complete the following steps:

1. Sign in to your Azure AD Connect server and start the Azure AD Connect configuration wizard.
2. On the Welcome page, select Configure.
3. On the Additional tasks page, select Customize synchronization options, and then select Next.
4. On the Connect to Azure AD page, enter a global administrator credential for your Azure tenant, and then select Next.
5. On the Connect directories and Domain/OU filtering pages, select Next.
6. On the Optional features page, select the box next to Password writeback and select Next.

![Configure Azure AD Connect for password writeback](https://docs.microsoft.com/en-us/azure/active-directory/authentication/media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

7. On the Ready to configure page, select Configure and wait for the process to finish.
8. When you see the configuration finish, select Exit.

### Enable password writeback for SSPR

With password writeback enabled in Azure AD Connect, now configure Azure AD SSPR for writeback. When you enable SSPR to use password writeback, users who change or reset their password have that updated password synchronized back to the on-premises AD DS environment as well.

To enable password writeback in SSPR, complete the following steps:

1. Sign in to the Azure portal using a global administrator account.
2. Search for and select Azure Active Directory, select Password reset, then choose On-premises integration.
3. Set the option for Write back passwords to your on-premises directory? to Yes.
4. Set the option for Allow users to unlock accounts without resetting their password? to Yes.

![Enable Azure AD self-service password reset for password writeback](https://docs.microsoft.com/en-us/azure/active-directory/authentication/media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

[What is password writeback?](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-sspr-writeback)

## configure single sign-on

[Azure Active Directory Seamless Single Sign-On: Quick start](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-sso-quick-start)

### Deploy Seamless Single Sign-On

Azure Active Directory (Azure AD) Seamless Single Sign-On (Seamless SSO) automatically signs in users when they are on their corporate desktops that are connected to your corporate network. Seamless SSO provides your users with easy access to your cloud-based applications without needing any additional on-premises components.

### Step 1: Check the prerequisites

Ensure that the following prerequisites are in place:

- Set up your Azure AD Connect server: If you use Pass-through Authentication as your sign-in method, no additional prerequisite check is required. If you use password hash synchronization as your sign-in method, and if there is a firewall between Azure AD Connect and Azure AD, ensure that:
  - You use version 1.1.644.0 or later of Azure AD Connect.
  - If your firewall or proxy allows, add the connections to the allowed list for *.msappproxy.net URLs over port 443. If not, allow access to the Azure datacenter IP ranges, which are updated weekly. This prerequisite is applicable only when you enable the feature. It is not required for actual user sign-ins.
  > Note
  >
  > Azure AD Connect versions 1.1.557.0, 1.1.558.0, 1.1.561.0, and 1.1.614.0 have a problem related to password hash synchronization. If you don't intend to use password hash synchronization in conjunction with Pass-through Authentication, read the Azure AD Connect release notes to learn more.
- Use a supported Azure AD Connect topology: Ensure that you are using one of Azure AD Connect's supported topologies described here.
  > Note
  >
  > Seamless SSO supports multiple AD forests, whether there are AD trusts between them or not.
- Set up domain administrator credentials: You need to have domain administrator credentials for each Active Directory forest that:
  - You synchronize to Azure AD through Azure AD Connect.
  - Contains users you want to enable for Seamless SSO.
- Enable modern authentication: You need to enable modern authentication on your tenant for this feature to work.
- Use the latest versions of Microsoft 365 clients: To get a silent sign-on experience with Microsoft 365 clients (Outlook, Word, Excel, and others), your users need to use versions 16.0.8730.xxxx or above.

### Step 2: Enable the feature

Enable Seamless SSO through Azure AD Connect.

> Note
>
> You can also enable Seamless SSO using PowerShell if Azure AD Connect doesn't meet your requirements. Use this option if you have more than one domain per Active Directory forest, and you want to be more targeted about the domain you want to enable Seamless SSO for.

If you're doing a fresh installation of Azure AD Connect, choose the custom installation path. At the User sign-in page, select the Enable single sign on option.

> Note
>
> The option will be available for selection only if the Sign On method is Password Hash Synchronization or Pass-through Authentication.

![Azure AD Connect: User sign-in](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/media/how-to-connect-sso-quick-start/sso8.png)

If you already have an installation of Azure AD Connect, select the Change user sign-in page in Azure AD Connect, and then select Next. If you are using Azure AD Connect versions 1.1.880.0 or above, the Enable single sign on option will be selected by default. If you are using older versions of Azure AD Connect, select the Enable single sign on option.

![Azure AD Connect: Change the user sign-in](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/media/how-to-connect-sso-quick-start/changeusersignin.png)

Continue through the wizard until you get to the **Enable single sign on page**. Provide domain administrator credentials for each Active Directory forest that:

- You synchronize to Azure AD through Azure AD Connect.
- Contains users you want to enable for Seamless SSO.

After completion of the wizard, Seamless SSO is enabled on your tenant.

> Note
>
>The domain administrator credentials are not stored in Azure AD Connect or in Azure AD. They're used only to enable the feature.

[Azure Active Directory Seamless Single Sign-On: Frequently asked questions](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-sso-faq)

In this article, we address frequently asked questions about Azure Active Directory Seamless Single Sign-On (Seamless SSO). Keep checking back for new content.

**Q: What sign-in methods do Seamless SSO work with**

Seamless SSO can be combined with either the Password Hash Synchronization or Pass-through Authentication sign-in methods. However this feature cannot be used with Active Directory Federation Services (ADFS).

**Q: Is Seamless SSO a free feature?**

Seamless SSO is a free feature and you don't need any paid editions of Azure AD to use it.

**Q: Is Seamless SSO available in the Microsoft Azure Germany cloud and the Microsoft Azure Government cloud?**

Seamless SSO is available for the Azure Government cloud. For details, view Hybrid Identity Considerations for Azure Government.

## use Azure AD Connect Health

[Azure Active Directory Connect Health operations](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-health-operations)

This topic describes the various operations you can perform by using Azure Active Directory (Azure AD) Connect Health.

### Enable email notifications

You can configure the Azure AD Connect Health service to send email notifications when alerts indicate that your identity infrastructure is not healthy. This occurs when an alert is generated, and when it is resolved.

### Delete a server or service instance

> Note
>
> Azure AD premium license is required for the deletion steps.

In some instances, you might want to remove a server from being monitored. Here's what you need to know to remove a server from the Azure AD Connect Health service.

### Delete a service instance from Azure AD Connect Health service

In some instances, you might want to remove a service instance. Here's what you need to know to remove a service instance from the Azure AD Connect Health service.

### Manage access with Azure RBAC

Azure role-based access control (Azure RBAC) for Azure AD Connect Health provides access to users and groups other than global administrators. Azure RBAC assigns roles to the intended users and groups, and provides a mechanism to limit the global administrators within your directory.

### Roles

Azure AD Connect Health supports the following built-in roles:

|Role|	Permissions|
|:--|:--|
|Owner	|Owners can manage access (for example, assign a role to a user or group), view all information (for example, view alerts) from the portal, and change settings (for example, email notifications) within Azure AD Connect Health.<br />By default, Azure AD global administrators are assigned this role, and this cannot be changed.|
|Contributor	|Contributors can view all information (for example, view alerts) from the portal, and change settings (for example, email notifications) within Azure AD Connect Health.|
|Reader	|Readers can view all information (for example, view alerts) from the portal within Azure AD Connect Health.<br />All other roles (such as User Access Administrators or DevTest Labs Users) have no impact to access within Azure AD Connect Health, even if the roles are available in the portal experience.|

### Access scope

Azure AD Connect Health supports managing access at two levels:

- All service instances: This is the recommended path in most cases. It controls access for all service instances (for example, an AD FS farm) across all role types that are being monitored by Azure AD Connect Health.
- Service instance: In some cases, you might need to segregate access based on role types or by a service instance. In this case, you can manage access at the service instance level.

Permission is granted if an end user has access either at the directory or service instance level.
