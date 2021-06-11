# Implement Azure Active Directory

[Azure Active Directory - Azure Docs](https://docs.microsoft.com/en-us/azure/active-directory/)

## add custom domains

[Add your custom domain name using the Azure Active Directory portal](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/add-custom-domain)

### Create your directory in Azure AD

After you get your domain name, you can create your first Azure AD directory. Sign in to the Azure portal for your directory, using an account with the Owner role for the subscription.

Create your new directory by following the steps in Create a new tenant for your organization.

> Important
>
> The person who creates the tenant is automatically the Global administrator for that tenant. The Global administrator can add additional administrators to the tenant.

For more information about subscription roles, see Azure roles.

> Tip
>
> If you plan to federate your on-premises Windows Server AD with Azure AD, then you need to select I plan to configure this domain for single sign-on with my local Active Directory when you run the Azure AD Connect tool to synchronize your directories.
>
> You also need to register the same domain name you select for federating with your on-premises directory in the Azure AD Domain step in the wizard. To see what that setup looks like, see Verify the Azure AD domain selected for federation. If you don't have the Azure AD Connect tool, you can download it here.

In the Portal, select Azure Active Directory -> Custom domain names -> Add Custom Domain Name. Enter the custom domain name and click "Add Domain".

> Important
>
> You must include .com, .net, or any other top-level extension for this to work properly.

The unverified domain is added. The contoso.com page appears showing your DNS information. Save this information. You need it later to create a TXT record to configure DNS.

### Add your DNS information to the domain registrar

After you add your custom domain name to Azure AD, you must return to your domain registrar and add the Azure AD DNS information from your copied TXT file. Creating this TXT record for your domain verifies ownership of your domain name.

Go back to your domain registrar and create a new TXT record for your domain based on your copied DNS information. Set the time to live (TTL) to 3600 seconds (60 minutes), and then save the record.

> Important
>
>You can register as many domain names as you want. However, each domain gets its own TXT record from Azure AD. Be careful when you enter the TXT file information at the domain registrar. If you enter the wrong or duplicate information by mistake, you'll have to wait until the TTL times out (60 minutes) before you can try again.

### Verify your custom domain name

After you register your custom domain name, make sure it's valid in Azure AD. The propagation from your domain registrar to Azure AD can be instantaneous or it can take a few days, depending on your domain registrar.

To verify your custom domain name, follow these steps:

1. Sign in to the Azure portal using a Global administrator account for the directory.
2. Search for and select Azure Active Directory from any page, then select Custom domain names.
3. In Custom domain names, select the custom domain name. In this example, select contoso.com.
4. On the contoso.com page, select Verify to make sure your custom domain is properly registered and is valid for Azure AD.

After you've verified your custom domain name, you can delete your verification TXT or MX file.

## configure Azure AD Identity Protection

[What is Azure Active Directory Identity Protection?](https://docs.microsoft.com/en-us/azure/active-directory/identity-protection/overview-identity-protection)

Identity Protection is a tool that allows organizations to accomplish three key tasks:

1. Automate the detection and remediation of identity-based risks.
2. Investigate risks using data in the portal.
3. Export risk detection data to third-party utilities for further analysis.

Identity Protection uses the learnings Microsoft has acquired from their position in organizations with Azure AD, the consumer space with Microsoft Accounts, and in gaming with Xbox to protect your users. Microsoft analyses 6.5 trillion signals per day to identify and protect customers from threats.

### Why is automation important?

In his blog post in October of 2018 Alex Weinert, who leads Microsoft's Identity Security and Protection team, explains why automation is so important when dealing with the volume of events:

> Each day, our machine learning and heuristic systems provide risk scores for 18 billion login attempts for over 800 million distinct accounts, 300 million of which are discernibly done by adversaries (entities like: criminal actors, hackers).

### Risk Detection and Remediation

Identity Protection identifies risks in the following classifications:

|Risk detection type	|Description|
|:--|:--|
|Atypical travel	|Sign in from an atypical location based on the user's recent sign-ins.|
|Anonymous IP address	|Sign in from an anonymous IP address (for example: Tor browser, anonymizer VPNs).|
|Unfamiliar sign-in properties	|Sign in with properties we've not seen recently for the given user.|
|Malware linked IP address	|Sign in from a malware linked IP address.|
|Leaked Credentials	|Indicates that the user's valid credentials have been leaked.|
|Password spray	|Indicates that multiple usernames are being attacked using common passwords in a unified, brute-force manner.|
|Azure AD threat intelligence	|Microsoft's internal and external threat intelligence sources have identified a known attack pattern.|

### Risk investigation

Administrators can review detections and take manual action on them if needed. There are three key reports that administrators use for investigations in Identity Protection:

- Risky users
- Risky sign-ins
- Risk detections

More information can be found in the article, How To: Investigate risk.

### Risk levels

Identity Protection categorizes risk into three tiers: low, medium, and high.

While Microsoft does not provide specific details about how risk is calculated, we will say that each level brings higher confidence that the user or sign-in is compromised. For example, something like one instance of unfamiliar sign-in properties for a user might not be as threatening as leaked credentials for another user.

### Permissions

Identity Protection requires users be a Security Reader, Security Operator, Security Administrator, Global Reader, or Global Administrator in order to access.

|Role	|Can do	|Can't do|
|:--|:--|:--|
|Global administrator	|Full access to Identity Protection	||
|Security administrator	|Full access to Identity Protection	|Reset password for a user|
|Security operator	|View all Identity Protection reports and Overview blade<br />Dismiss user risk, confirm safe sign-in, confirm compromise|Configure or change policies</br>Reset password for a user<br />Configure alerts|
|Security reader	|View all Identity Protection reports and Overview blade|Configure or change policies<br />Reset password for a user<br />Configure alerts<br />Give feedback on detections|

Currently, the security operator role cannot access the Risky sign-ins report.

Conditional Access administrators can also create policies that factor in sign-in risk as a condition. Find more information in the article [Conditional Access: Conditions](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/concept-conditional-access-conditions#sign-in-risk).

### License requirements

Using this feature requires an Azure AD Premium P2 license. To find the right license for your requirements, see Comparing generally available features of the Free, Office 365 Apps, and Premium editions.

|Capability	Details	|Azure AD Free / Microsoft 365 Apps	|Azure AD Premium P1	|Azure AD Premium P2|
|:--|:--|:--|:--|
|Risk policies	|User risk policy (via Identity Protection)|	No	|No|	Yes|
|Risk policies	|Sign-in risk policy (via Identity Protection or Conditional Access)|	No	|No|	Yes|
|Security reports|	Overview|	No	|No|	Yes|
|Security reports	|Risky users	|Limited Information. Only users with medium and high risk are shown. No details drawer or risk history.	|Limited Information. Only users with medium and high risk are shown. No details drawer or risk history.	|Full access|
|Security reports	|Risky sign-ins	|Limited Information. No risk detail or risk level is shown.|	Limited Information. No risk detail or risk level is shown.	|Full access|
|Security reports	|Risk detections	|No	|Limited Information. No details drawer.	|Full access|
|Notifications	|Users at risk detected alerts	|No	|No|	Yes|
|Notifications	|Weekly digest	|No	|No|	Yes|
||MFA registration policy	|No	|No|	Yes|

## implement self-service password reset

[Plan an Azure Active Directory self-service password reset](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-sspr-deployment)

> Self-Service Password Reset (SSPR) is an Azure Active Directory (AD) feature that enables users to reset their passwords without contacting IT staff for help. The users can quickly unblock themselves and continue working no matter where they are or time of day. By allowing the employees to unblock themselves, your organization can reduce the non-productive time and high support costs for most common password-related issues.

SSPR has the following key capabilities:

- Self-service allows end users to reset their expired or non-expired passwords without contacting an administrator or helpdesk for support.
- [Password Writeback](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-sspr-writeback) allows management of on-premises passwords and resolution of account lockout though the cloud.
- Password management activity reports give administrators insight into password reset and registration activity occurring in their organization.

### Licensing

Azure Active Directory is licensed per-user meaning each user requires an appropriate license for the features they use. We recommend group-based licensing for SSPR.

### Prerequisites

- A working Azure AD tenant with at least a trial license enabled. If needed, create one for free.
- An account with Global Administrator privileges.

### Solution architecture

The following example describes the password reset solution architecture for common hybrid environments.

![SSPR User Flow](https://docs.microsoft.com/en-us/azure/active-directory/authentication/media/howto-sspr-deployment/solutions-architecture.png)

Description of workflow

To reset the password, users go to the password reset portal. They must verify the previously registered authentication method or methods to prove their identity. If they successfully reset the password, they begin the reset process.

- For cloud-only users, SSPR stores the new password in Azure AD.
- For hybrid users, SSPR writes back the password to the on-prem Active Directory via the Azure AD Connect service.

Note: For users who have Password hash synchronization (PHS) disabled, SSPR stores the passwords in the on-prem Active Directory only.

### Best practices

You can help users register quickly by deploying SSPR alongside another popular application or service in the organization. This action will generate a large volume of sign-ins and will drive registration.

Before deploying SSPR, you may opt to determine the number and the average cost of each password reset call. You can use this data post deployment to show the value SSPR is bringing to the organization.

### Enable combined registration for SSPR and MFA

Microsoft recommends that organizations enable the combined registration experience for SSPR and multi-factor authentication. When you enable this combined registration experience, users need only select their registration information once to enable both features.

The combined registration experience does not require organizations to enable both SSPR and Azure AD Multi-Factor Authentication. Combined registration provides organizations a better user experience. For more information, see [Combined security information registration](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

### Required administrator roles

|Business Role/Persona	|Azure AD Role (if necessary)|
|:--|:--|
|Level 1 helpdesk	|Password administrator|
|Level 2 helpdesk	|User administrator|
|SSPR administrator	|Global administrator|

### Password Writeback

Password Writeback is enabled with Azure AD Connect and writes password resets in the cloud back to an existing on-premises directory in real time. For more information, see What is Password Writeback?

We recommend the following settings:

- Ensure that Write back passwords to on-premises AD is set to Yes.
- Set the Allow users to unlock account without resetting password to Yes.

By default, Azure AD unlocks accounts when it performs a password reset.

### Administrator password setting

Administrator accounts have elevated permissions. The on-premises enterprise or domain administrators can't reset their passwords through SSPR. On-premises admin accounts have the following restrictions:

- can only change their password in their on-prem environment.
- can never use the secret questions and answers as a method to reset their password.

We recommend that you don't sync your on-prem Active Directory admin accounts with Azure AD.

### Plan rollback

To roll back the deployment:

- for a single user, remove the user from the security group
- for a group, remove the group from SSPR configuration
- For everyone, disable SSPR for the Azure AD tenant

### Manage SSPR

Azure AD can provide additional information on your SSPR performance through audits and reports.

### Password management activity reports

You can use pre-built reports on Azure portal to measure the SSPR performance. If you're appropriately licensed, you can also create custom queries. For more information, see Reporting options for Azure AD password management

> Note
>
> You must be a global administrator, and you must opt-in for this data to be gathered for your organization. To opt in, you must visit the Reporting tab or the audit logs on the Azure Portal at least once. Until then, the data doesn't collect for your organization.

Audit logs for registration and password reset are available for 30 days. If security auditing within your corporation requires longer retention, the logs need to be exported and consumed into a SIEM tool such as Azure Sentinel, Splunk, or ArcSight.

[How it works: Azure AD self-service password reset](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-sspr-howitworks)

### Authentication methods

When a user is enabled for SSPR, they must register at least one authentication method. We highly recommend that you choose two or more authentication methods so that your users have more flexibility in case they're unable to access one method when they need it. For more information, see [What are authentication methods?](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-authentication-methods).

The following authentication methods are available for SSPR:

- Mobile app notification
- Mobile app code
- Email
- Mobile phone
- Office phone
- Security questions

Users can only reset their password if they have registered an authentication method that the administrator has enabled.

> Warning
>
> Accounts assigned Azure administrator roles are required to use methods as defined in the section Administrator reset policy differences.

### Number of authentication methods required

You can configure the number of the available authentication methods a user must provide to reset or unlock their password. This value can be set to either one or two.

Users can, and should, register multiple authentication methods. Again, it's highly recommended that users register two or more authentication methods so they have more flexibility in case they're unable to access one method when they need it.

### Write back passwords to your on-premises directory

You can enable password writeback using the Azure portal. You can also temporarily disable password writeback without having to reconfigure Azure AD Connect.

- If the option is set to Yes, then writeback is enabled. Federated, pass-through authentication, or password hash synchronized users are able to reset their passwords.
- If the option is set to No, then writeback is disabled. Federated, pass-through authentication, or password hash synchronized users aren't able to reset their passwords.

[Licensing requirements for Azure AD self-service password reset](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-sspr-licensing)

### Compare editions and features

SSPR is licensed per user. To maintain compliance, organizations are required to assign the appropriate license to their users.

The following table outlines the different SSPR scenarios for password change, reset, or on-premises writeback, and which SKUs provide the feature.

|Feature	|Azure AD Free	|Microsoft 365 Business Standard	|Microsoft 365 Business Premium	|Azure AD Premium P1 or P2|
|:--|:--|:--|:--|:--|
|Cloud-only user password change<br />When a user in Azure AD knows their password and wants to change it to something new.|	●	|●	|●	|●|
|Cloud-only user password reset<br />When a user in Azure AD has forgotten their password and needs to reset it.||		●|	●|	●|
|Hybrid user password change or reset with on-prem writeback<br />When a user in Azure AD that's synchronized from an on-premises directory using Azure AD Connect wants to change or reset their password and also write the new password back to on-prem.|||●|●|

### Enable group or user-based licensing

Azure AD supports group-based licensing. Administrators can assign licenses in bulk to a group of users, rather than assigning them one at a time. For more information, see Assign, verify, and resolve problems with licenses.

## implement Conditional Access including MFA

[Conditional Access: Require MFA for all users](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/howto-conditional-access-policy-all-users-mfa)

As Alex Weinert, the Directory of Identity Security at Microsoft, mentions in his blog post Your Pa$$word doesn't matter:

> Your password doesn't matter, but MFA does! Based on our studies, your account is more than 99.9% less likely to be compromised if you use MFA.

The guidance in this article will help your organization create a balanced MFA policy for your environment.

### User exclusions

Conditional Access policies are powerful tools, we recommend excluding the following accounts from your policy:

- **Emergency access** or **break-glass** accounts to prevent tenant-wide account lockout. In the unlikely scenario all administrators are locked out of your tenant, your emergency-access administrative account can be used to log into the tenant take steps to recover access.
  - More information can be found in the article, Manage emergency access accounts in Azure AD.
- **Service accounts** and **service principals**, such as the Azure AD Connect Sync Account. Service accounts are non-interactive accounts that are not tied to any particular user. They are normally used by back-end services allowing programmatic access to applications, but are also used to sign in to systems for administrative purposes. Service accounts like these should be excluded since MFA can't be completed programmatically. Calls made by service principals are not blocked by Conditional Access.
  - If your organization has these accounts in use in scripts or code, consider replacing them with managed identities. As a temporary workaround, you can exclude these specific accounts from the baseline policy.

### Application exclusions

Organizations may have many cloud applications in use. Not all of those applications may require equal security. For example, the payroll and attendance applications may require MFA but the cafeteria probably doesn't. Administrators can choose to exclude specific applications from their policy.

### Create a Conditional Access policy

The following steps will help create a Conditional Access policy to require All users to perform multi-factor authentication.

1. Sign in to the Azure portal as a global administrator, security administrator, or Conditional Access administrator.
2. Browse to Azure Active Directory > Security > Conditional Access.
3. Select New policy.
4. Give your policy a name. We recommend that organizations create a meaningful standard for the names of their policies.
5. Under Assignments, select Users and groups
  - Under Include, select All users
  - Under Exclude, select Users and groups and choose your organization's emergency access or break-glass accounts.
  - Select Done.
6. Under Cloud apps or actions > Include, select All cloud apps.
  - Under Exclude, select any applications that do not require multi-factor authentication.
7. Under Conditions > Client apps (Preview), under Select the client apps this policy will apply to leave all defaults selected and select Done.
8. Under Access controls > Grant, select Grant access, Require multi-factor authentication, and select Select.
9. Confirm your settings and set Enable policy to On.
10. Select Create to create to enable your policy.

### Named locations

Organizations may choose to incorporate known network locations known as Named locations to their Conditional Access policies. These named locations may include trusted IPv4 networks like those for a main office location. For more information about configuring named locations, see the article What is the location condition in Azure Active Directory Conditional Access?

In the example policy above, an organization may choose to not require multi-factor authentication if accessing a cloud app from their corporate network. In this case they could add the following configuration to the policy:

1. Under Assignments, select Conditions > Locations.
  - Configure Yes.
  - Include Any location.
  - Exclude All trusted locations.
  - Select Done.
2. Select Done.
3. Save your policy changes.

[Conditional Access: Risk-based Conditional Access](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/howto-conditional-access-policy-risk)

Most users have a normal behavior that can be tracked, when they fall outside of this norm it could be risky to allow them to just sign in. You may want to block that user or maybe just ask them to perform multi-factor authentication to prove that they are really who they say they are.

A sign-in risk represents the probability that a given authentication request isn't authorized by the identity owner. Organizations with Azure AD Premium P2 licenses can create Conditional Access policies incorporating Azure AD Identity Protection sign-in risk detections.

There are two locations where this policy may be assigned. Organizations should choose one of the following options to enable a sign-in risk-based Conditional Access policy requiring a secure password change.

### Enable with Conditional Access policy

1. Sign in to the Azure portal as a global administrator, security administrator, or Conditional Access administrator.
2. Browse to Azure Active Directory > Security > Conditional Access.
3. Select New policy.
4. Give your policy a name. We recommend that organizations create a meaningful standard for the names of their policies.
5. Under Assignments, select Users and groups.
  - Under Include, select All users.
  - Under Exclude, select Users and groups and choose your organization's emergency access or break-glass accounts.
  - Select Done.
6. Under Cloud apps or actions > Include, select All cloud apps.
7. Under Conditions > Sign-in risk, set Configure to Yes. Under Select the sign-in risk level this policy will apply to
  - Select High and Medium.
  - Select Done.
8. Under Access controls > Grant, select Grant access, Require multi-factor authentication, and select Select.
9. Confirm your settings and set Enable policy to On.
10. Select Create to create to enable your policy.

### Enable through Identity Protection

1. Sign in to the Azure portal.
2. Select All services, then browse to Azure AD Identity Protection.
3. Select Sign-in risk policy.
4. Under Assignments, select Users.
  - Under Include, select All users.
  - Under Exclude, select Select excluded users, choose your organization's emergency access or break-glass accounts, and select Select.
  - Select Done.
5. Under Conditions, select Sign-in risk, then choose Medium and above.
  - Select Select, then Done.
6. Under Controls > Access, choose Allow access, and then select Require multi-factor authentication.
  - Select Select.
7. Set Enforce Policy to On.
8. Select Save.

## configure user accounts for MFA

[Tutorial: Secure user sign-in events with Azure Multi-Factor Authentication](https://docs.microsoft.com/en-us/azure/active-directory/authentication/tutorial-enable-azure-mfa)

### Create a Conditional Access policy

The recommended way to enable and use Azure AD Multi-Factor Authentication is with Conditional Access policies. Conditional Access lets you create and define policies that react to sign in events and request additional actions before a user is granted access to an application or service.

![Overview diagram of how Conditional Access works to secure the sign-in process](https://docs.microsoft.com/en-us/azure/active-directory/authentication/media/tutorial-enable-azure-mfa/conditional-access-overview.png)

Conditional Access policies can be granular and specific, with the goal to empower users to be productive wherever and whenever, but also protect your organization. In this tutorial, let's create a basic Conditional Access policy to prompt for MFA when a user signs in to the Azure portal. In a later tutorial in this series, you configure Azure AD Multi-Factor Authentication using a risk-based Conditional Access policy.

First, create a Conditional Access policy and assign your test group of users as follows:

1. Sign in to the Azure portal using an account with global administrator permissions.
2. Search for and select Azure Active Directory, then choose Security from the menu on the left-hand side.
3. Select Conditional Access, then choose + New policy.
4. Enter a name for the policy, such as MFA Pilot.
5. Under Assignments, choose Users and groups, then the Select users and groups radio button.
6. Check the box for Users and groups, then Select to browse the available Azure AD users and groups.
7. Browse for and select your Azure AD group, such as MFA-Test-Group, then choose Select.
8. To apply the Conditional Access policy for the group, select Done.

### Configure the conditions for multi-factor authentication

With the Conditional Access policy created and a test group of users assigned, now define the cloud apps or actions that trigger the policy. These cloud apps or actions are the scenarios you decide require additional processing, such as to prompt for MFA. For example, you could decide that access to a financial application or use of management tools requires as an additional verification prompt.

For this tutorial, configure the Conditional Access policy to require MFA when a user signs in to the Azure portal.

1. Select Cloud apps or actions. You can choose to apply the Conditional Access policy to All cloud apps or Select apps. To provide flexibility, you can also exclude certain apps from the policy.<br />For this tutorial, on the Include page, choose the Select apps radio button.
2. Choose Select, then browse the list of available sign-in events that can be used.<br />For this tutorial, choose Microsoft Azure Management so the policy applies to sign-in events to the Azure portal.
3. To apply the select apps, choose Select, then Done.

Access controls let you define the requirements for a user to be granted access, such as needing an approved client app or using a device that's Hybrid Azure AD joined. In this tutorial, configure the access controls to require MFA during a sign-in event to the Azure portal.

1. Under Access controls, choose Grant, then make sure the Grant access radio button is selected.
2. Check the box for Require multi-factor authentication, then choose Select.

Conditional Access policies can be set to Report-only if you want to see how the configuration would impact users, or Off if you don't want to the use policy right now. As a test group of users was targeted for this tutorial, lets enable the policy and then test Azure AD Multi-Factor Authentication.

1. Set the Enable policy toggle to On.
2. To apply the Conditional Access policy, select Create.

## configure fraud alerts

[Reports in Azure Multi-Factor Authentication](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-reporting)

To review and understand Azure AD Multi-Factor Authentication events, you can use the Azure Active Directory (Azure AD) sign-ins report. This report shows authentication details for events when a user is prompted for multi-factor authentication, and if any Conditional Access policies were in use. For detailed information on the sign-ins report, see the overview of sign-in activity reports in Azure AD.

This article shows you how to view the Azure AD sign-ins report in the Azure portal, and then the MSOnline V1 PowerShell module.

### View the Azure AD sign-ins report

The sign-ins report provides you with information about the usage of managed applications and user sign-in activities, which includes information about multi-factor authentication (MFA) usage. The MFA data gives you insights into how MFA is working in your organization. It lets you answer questions like the following:

- Was the sign-in challenged with MFA?
- How did the user complete MFA?
- Why was the user unable to complete MFA?
- How many users are challenged for MFA?
- How many users are unable to complete the MFA challenge?
- What are the common MFA issues end users are running into?

To view the sign-in activity report in the Azure portal, complete the following steps. You can also query data using the reporting API.

1. Sign in to the Azure portal using an account with global administrator permissions.
2. Search for and select Azure Active Directory, then choose Users from the menu on the left-hand side.
3. Under Activity from the menu on the left-hand side, select Sign-ins.
4. A list of sign-in events is shown, including the status. You can select an event to view more details.<br />The Authentication Details or Conditional Access tab of the event details shows you the status code or which policy triggered the MFA prompt.

If available, the authentication is shown, such as text message, Microsoft Authenticator app notification, or phone call.

The following details are shown on the *Authentication Details* window for a sign-in event that show if the MFA request was satisfied or denied:

- If MFA was satisfied, this column provides more information about how MFA was satisfied.
  - completed in the cloud
  - has expired due to the policies configured on tenant
  - registration prompted
  - satisfied by claim in the token
  - satisfied by claim provided by external provider
  - satisfied by strong authentication
  - skipped as flow exercised was Windows broker logon flow
  - skipped due to app password
  - skipped due to location
  - skipped due to registered device
  - skipped due to remembered device
  - successfully completed

- If MFA was denied, this column would provide the reason for denial.
  - authentication in-progress
  - duplicate authentication attempt
  - entered incorrect code too many times
  - invalid authentication
  - invalid mobile app verification code
  - misconfiguration
  - phone call went to voicemail
  - phone number has an invalid format
  - service error
  - unable to reach the user's phone
  - unable to send the mobile app notification to the device
  - unable to send the mobile app notification
  - user declined the authentication
  - user did not respond to mobile app notification
  - user does not have any verification methods registered
  - user entered incorrect code
  - user entered incorrect PIN
  - user hung up the phone call without succeeding the authentication
  - user is blocked
  - user never entered the verification code
  - user not found
  - verification code already used once

[Configure Azure Multi-Factor Authentication settings](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-mfasettings)

To customize the end-user experience for Azure AD Multi-Factor Authentication, you can configure options for settings like the account lockout thresholds or fraud alerts and notifications. Some settings are directly in the Azure portal for Azure Active Directory (Azure AD), and some in a separate Azure AD Multi-Factor Authentication portal.

The following Azure AD Multi-Factor Authentication settings are available in the Azure portal:

|Feature	|Description|
|:--|:--|
|Account lockout	|Temporarily lock accounts from using Azure AD Multi-Factor Authentication if there are too many denied authentication attempts in a row. This feature only applies to users who enter a PIN to authenticate. (MFA Server)|
|Block/unblock users	|Block specific users from being able to receive Azure AD Multi-Factor Authentication requests. Any authentication attempts for blocked users are automatically denied. Users remain blocked for 90 days from the time that they are blocked or they're manually unblocked.|
|Fraud alert	|Configure settings that allow users to report fraudulent verification requests.|
|Notifications	|Enable notifications of events from MFA Server.|
|OATH tokens	|Used in cloud-based Azure AD MFA environments to manage OATH tokens for users.|
|Phone call settings	|Configure settings related to phone calls and greetings for cloud and on-premises environments.|
|Providers	|This will show any existing authentication providers that you may have associated with your account. New authentication providers may not be created as of September 1, 2018|

![Azure portal - Azure AD Multi-Factor Authentication settings](https://docs.microsoft.com/en-us/azure/active-directory/authentication/media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

### Account lockout

To prevent repeated MFA attempts as part of an attack, the account lockout settings let you specify how many failed attempts to allow before the account becomes locked out for a period of time. The account lockout settings are only applied when a pin code is entered for the MFA prompt.

The following settings are available:

- Number of MFA denials to trigger account lockout
- Minutes until account lockout counter is reset
- Minutes until account is automatically unblocked
- To configure account lockout settings, complete the following settings:

1. Sign in to the Azure portal as an administrator.
2. Browse to Azure Active Directory > Security > MFA > Account lockout.
3. Enter the require values for your environment, then select Save.

### Block and unblock users

If a user's device has been lost or stolen, you can block Azure AD Multi-Factor Authentication attempts for the associated account. Any Azure AD Multi-Factor Authentication attempts for blocked users are automatically denied. Users remain blocked for 90 days from the time that they are blocked.

#### Block a user

To block a user, complete the following steps:

1. Browse to Azure Active Directory > Security > MFA > Block/unblock users.
2. Select Add to block a user.
3. Select the Replication Group, then choose Azure Default.<br />Enter the username for the blocked user as username\@domain.com, then provide a comment in the Reason field.
4. When ready, select OK to block the user.

#### Unblock a user

To unblock a user, complete the following steps:

1. Browse to Azure Active Directory > Security > MFA > Block/unblock users.
2. In the Action column next to the desired user, select Unblock.
3. Enter a comment in the Reason for unblocking field.
4. When ready, select OK to unblock the user.

### Fraud alert

The fraud alert feature lets users report fraudulent attempts to access their resources. When an unknown and suspicious MFA prompt is received, users can report the fraud attempt using the Microsoft Authenticator app or through their phone.

The following fraud alert configuration options are available:

- **Automatically block users who report fraud**: If a user reports fraud, the Azure AD MFA authentication attempts for the user account are blocked for 90 days or until an administrator unblocks their account. An administrator can review sign-ins by using the sign-in report, and take appropriate action to prevent future fraud. An administrator can then unblock the user's account.

- **Code to report fraud during initial greeting**: When users receive a phone call to perform multi-factor authentication, they normally press # to confirm their sign-in. To report fraud, the user enters a code before pressing #. This code is 0 by default, but you can customize it.

> Note
>
> The default voice greetings from Microsoft instruct users to press 0# to submit a fraud alert. If you want to use a code other than 0, record and upload your own custom voice greetings with appropriate instructions for your users.

To enable and configure fraud alerts, complete the following steps:

1. Browse to Azure Active Directory > Security > MFA > Fraud alert.
2. Set the Allow users to submit fraud alerts setting to On.
3. Configure the Automatically block users who report fraud or Code to report fraud during initial greeting setting as desired.
4. When ready, select Save.

### View fraud reports

Select Azure Active Directory > Sign-ins > Authentication Details. The fraud report is now part of the standard Azure AD Sign-ins report and it will show in the "Result Detail" as MFA denied, Fraud Code Entered.

### Notifications

Email notifications can be configured when users report fraud alerts. These notifications are typically sent to identity administrators, as the user's account credentials are likely compromised. The following example shows what a fraud alert notification email looks like:

![Example fraud alert notification email](https://docs.microsoft.com/en-us/azure/active-directory/authentication/media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

To configure fraud alert notifications, complete the following settings:

1. Browse to Azure Active Directory > Security > Multi-Factor Authentication > Notifications.
2. Enter the email address to add into the next box.
3. To remove an existing email address, select the ... option next to the desired email address, then select Delete.
4. When ready, select Save.

### Remember Multi-Factor Authentication

The remember Multi-Factor Authentication feature lets users can bypass subsequent verifications for a specified number of days, after they've successfully signed-in to a device by using Multi-Factor Authentication. To enhance usability and minimize the number of times a user has to perform MFA on the same device, select a duration of 90 days or more.

> Important
>
> If an account or device is compromised, remembering Multi-Factor Authentication for trusted devices can affect security. If a corporate account becomes compromised or a trusted device is lost or stolen, you should Revoke MFA Sessions.
>
> The restore action revokes the trusted status from all devices, and the user is required to perform multi-factor authentication again. You can also instruct your users to restore Multi-Factor Authentication on their own devices as noted in Manage your settings for multi-factor authentication.

#### How the feature works

The remember Multi-Factor Authentication feature sets a persistent cookie on the browser when a user selects the Don't ask again for X days option at sign-in. The user isn't prompted again for Multi-Factor Authentication from that same browser until the cookie expires. If the user opens a different browser on the same device or clears their cookies, they're prompted again to verify.

The Don't ask again for X days option isn't shown on non-browser applications, regardless of whether the app supports modern authentication. These apps use refresh tokens that provide new access tokens every hour. When a refresh token is validated, Azure AD checks that the last multi-factor authentication occurred within the specified number of days.

The feature reduces the number of authentications on web apps, which normally prompt every time. The feature can increase the number of authentications for modern authentication clients that normally prompt every 90 days, if a lower duration is configured. May also increase the number of authentications when combined with Conditional Access policies.

> Important
>
> The remember Multi-Factor Authentication feature isn't compatible with the keep me signed in feature of AD FS, when users perform multi-factor authentication for AD FS through Azure Multi-Factor Authentication Server or a third-party multi-factor authentication solution.
>
> If your users select keep me signed in on AD FS and also mark their device as trusted for Multi-Factor Authentication, the user isn't automatically verified after the remember multi-factor authentication number of days expires. Azure AD requests a fresh multi-factor authentication, but AD FS returns a token with the original Multi-Factor Authentication claim and date, rather than performing multi-factor authentication again. This reaction sets off a verification loop between Azure AD and AD FS.
>
> The remember Multi-Factor Authentication feature is not compatible with B2B users and will not be visible for B2B users when signing into the invited tenants.

#### Enable remember Multi-Factor Authentication

To enable and configure the option for users to remember their MFA status and bypass prompts, complete the following steps:

1. In the Azure portal, search for and select Azure Active Directory, then choose Users.
2. Select Multi-Factor Authentication.
3. Under Multi-Factor Authentication, select service settings.
4. On the Service Settings page, under remember multi-factor authentication, select the Allow users to remember multi-factor authentication on devices they trust option.
5. Set the number of days to allow trusted devices to bypass multi-factor authentication. For the optimal user experience, extend the duration to 90 or more days.
6. Select Save.

#### Mark a device as trusted

After you enable the remember Multi-Factor Authentication feature, users can mark a device as trusted when they sign in by selecting the option for Don't ask again.

## configure bypass options

[Configure Azure Multi-Factor Authentication settings](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-mfasettings)

Use Trusted IPs and Devices, and Named Locations

## configure Trusted Ips

[Quickstart: Configure named locations in Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
[What is the location condition in Azure Active Directory Conditional Access?](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/location-condition)

### Trusted IPs

The Trusted IPs feature of Azure AD Multi-Factor Authentication bypasses multi-factor authentication prompts for users who sign in from a defined IP address range. You can set trusted IP ranges for your on-premises environments to when users are in one of those locations, there's no Azure AD Multi-Factor Authentication prompt.

> Note
>
> The trusted IPs can include private IP ranges only when you use MFA Server. For cloud-based Azure AD Multi-Factor Authentication, you can only use public IP address ranges.
>
> IPv6 ranges are only supported in the Named location (preview) interface.

If your organization deploys the NPS extension to provide MFA to on-premises applications note the source IP address will always appear to be the NPS server the authentication attempt flows through.

|Azure AD tenant type	|Trusted IP feature options|
|:--|:--|
|Managed	|**Specific range of IP addresses**: Administrators specify a range of IP addresses that can bypass multi-factor authentication for users who sign in from the company intranet. A maximum of 50 trusted IP ranges can be configured.
|Federated	|**All Federated Users**: All federated users who sign in from inside of the organization can bypass multi-factor authentication. The users bypass verification by using a claim that is issued by Active Directory Federation Services (AD FS).<br />**Specific range of IP addresses**: Administrators specify a range of IP addresses that can bypass multi-factor authentication for users who sign in from the company intranet.|

Trusted IP bypass works only from inside of the company intranet. If you select the All Federated Users option and a user signs in from outside the company intranet, the user has to authenticate by using multi-factor authentication. The process is the same even if the user presents an AD FS claim.

#### End-user experience inside of corpnet

When the trusted IPs feature is disabled, multi-factor authentication is required for browser flows. App passwords are required for older rich client applications.

When trusted IPs are used, multi-factor authentication isn't required for browser flows. App passwords aren't required for older rich client applications, provided that the user hasn't created an app password. After an app password is in use, the password remains required.

#### End-user experience outside corpnet

Regardless of whether trusted IP are defined, multi-factor authentication is required for browser flows. App passwords are required for older rich client applications.

#### Enable named locations by using Conditional Access

You can use Conditional Access rules to define named locations using the following steps:

1. In the Azure portal, search for and select Azure Active Directory, then browse to Security > Conditional Access > Named locations.
2. Select New location.
3. Enter a name for the location.
4. Select Mark as trusted location.
5. Enter the IP Range in CIDR notation for your environment, such as 40.77.182.32/27.
6. Select Create.

### Enable the Trusted IPs feature by using Conditional Access

To enable trusted IPs using Conditional Access policies, complete the following steps:

1. In the Azure portal, search for and select Azure Active Directory, then browse to Security > Conditional Access > Named locations.
2. Select Configure MFA trusted IPs.
3. On the Service Settings page, under Trusted IPs, choose from any of the following two options:
  - **For requests from federated users originating from my intranet**: To choose this option, select the check box. All federated users who sign in from the corporate network bypass multi-factor authentication by using a claim that is issued by AD FS. Ensure that AD FS has a rule to add the intranet claim to the appropriate traffic. If the rule does not exist, create the following rule in AD FS:<br /><br />c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
  - **For requests from a specific range of public IPs**: To choose this option, enter the IP addresses in the text box by using CIDR notation.
    - For IP addresses that are in the range xxx.xxx.xxx.1 through xxx.xxx.xxx.254, use notation like xxx.xxx.xxx.0/24.
    - For a single IP address, use notation like xxx.xxx.xxx.xxx/32.
    - Enter up to 50 IP address ranges. Users who sign in from these IP addresses bypass multi-factor authentication.
4. Select Save.

### Enable the Trusted IPs feature by using service settings

If you don't want to use Conditional Access policies to enable trusted IPs, you can configure the service settings for Azure AD Multi-Factor Authentication using the following steps:

1. In the Azure portal, search for and select Azure Active Directory, then choose Users.
2. Select Multi-Factor Authentication.
3. Under Multi-Factor Authentication, select service settings.
4. On the Service Settings page, under Trusted IPs, choose one (or both) of the following two options:
  - **For requests from federated users on my intranet**: To choose this option, select the check box. All federated users who sign in from the corporate network bypass multi-factor authentication by using a claim that is issued by AD FS. Ensure that AD FS has a rule to add the intranet claim to the appropriate traffic. If the rule does not exist, create the following rule in AD FS:<br /><br />c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
  - **For requests from a specified range of IP address subnets**: To choose this option, enter the IP addresses in the text box by using CIDR notation.
    - For IP addresses that are in the range xxx.xxx.xxx.1 through xxx.xxx.xxx.254, use notation like xxx.xxx.xxx.0/24.
    - For a single IP address, use notation like xxx.xxx.xxx.xxx/32.
    - Enter up to 50 IP address ranges. Users who sign in from these IP addresses bypass multi-factor authentication.
5. Select Save.

## configure verification methods

[Change your two-factor verification method and settings](https://docs.microsoft.com/en-us/azure/active-directory/user-help/multi-factor-authentication-end-user-manage-settings)
[What is the Additional verification page?](https://docs.microsoft.com/en-us/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time)

### Verification methods

You can choose the verification methods that are available for your users in the service settings portal. When your users enroll their accounts for Azure AD Multi-Factor Authentication, they choose their preferred verification method from the options that you have enabled. Guidance for the user enrollment process is provided in Set up my account for multi-factor authentication.

The following verification methods are available:

|Method	|Description|
|:--|:--|
|Call to phone	|Places an automated voice call. The user answers the call and presses # in the phone keypad to authenticate. The phone number is not synchronized to on-premises Active Directory.|
|Text message to phone	|Sends a text message that contains a verification code. The user is prompted to enter the verification code into the sign-in interface. This process is called one-way SMS. Two-way SMS means that the user must text back a particular code. Two-way SMS is deprecated and not supported after November 14, 2018. Administrators should enable another method for users who previously used two-way SMS.|
|Notification through mobile app	|Sends a push notification to your phone or registered device. The user views the notification and selects Verify to complete verification. The Microsoft Authenticator app is available for Windows Phone, Android, and iOS.|
|Verification code from mobile app or hardware token	|The Microsoft Authenticator app generates a new OATH verification code every 30 seconds. The user enters the verification code into the sign-in interface. The Microsoft Authenticator app is available for Windows Phone, Android, and iOS.|

For more information, see What authentication and verification methods are available in Azure AD?

#### Enable and disable verification methods

To enable or disable verification methods, complete the following steps:

1. In the Azure portal, search for and select Azure Active Directory, then choose Users.
2. Select Multi-Factor Authentication.
3. Under Multi-Factor Authentication, select service settings.
4. On the Service Settings page, under verification options, select/unselect the methods to provide to your users.
5. Click Save.

## implement and manage guest accounts

[What is guest user access in Azure Active Directory B2B?](https://docs.microsoft.com/en-us/azure/active-directory/b2b/what-is-b2b)
[Quickstart: Add guest users to your directory in the Azure portal](https://docs.microsoft.com/en-us/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)

Azure Active Directory (Azure AD) business-to-business (B2B) collaboration is a feature within External Identities that lets you invite guest users to collaborate with your organization. With B2B collaboration, you can securely share your company's applications and services with guest users from any other organization, while maintaining control over your own corporate data. Work safely and securely with external partners, large or small, even if they don't have Azure AD or an IT department. A simple invitation and redemption process lets partners use their own credentials to access your company's resources. Developers can use Azure AD business-to-business APIs to customize the invitation process or write applications like self-service sign-up portals. For licensing and pricing information related to guest users, refer to Azure Active Directory pricing.

### Collaborate with any partner using their identities

With Azure AD B2B, the partner uses their own identity management solution, so there is no external administrative overhead for your organization. Guest users sign in to your apps and services with their own work, school, or social identities.

- The partner uses their own identities and credentials; Azure AD is not required.
- You don't need to manage external accounts or passwords.
- You don't need to sync accounts or manage account lifecycles.

### Easily invite guest users from the Azure AD portal

As an administrator, you can easily add guest users to your organization in the Azure portal.

1. Create a new guest user in Azure AD, similar to how you'd add a new user.
2. Assign guest users to apps or groups.
3. Send an invitation email that contains a redemption link, or send a direct link to an app you want to share.

![Screenshot showing the New Guest User invitation entry page](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- Guest users follow a few simple redemption steps to sign in.

![Screenshot showing the Review permissions page](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/media/what-is-b2b/consentscreen.png)

### Use policies to securely share your apps and services

You can use authorization policies to protect your corporate content. Conditional Access policies, such as multi-factor authentication, can be enforced:

- At the tenant level.
- At the application level.
- For specific guest users to protect corporate apps and data.

![Screenshot showing the Conditional Access option](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/media/what-is-b2b/tutorial-mfa-policy-2.png)

### Let application and group owners manage their own guest users

You can delegate guest user management to application owners so that they can add guest users directly to any application they want to share, whether it's a Microsoft application or not.

- Administrators set up self-service app and group management.
- Non-administrators use their Access Panel to add guest users to applications or groups.

![Screenshot showing the Access panel for a guest user](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/media/what-is-b2b/access-panel-manage-app.png)

### Customize the onboarding experience for B2B guest users

Bring your external partners on board in ways customized to your organization's needs.

- Use Azure AD entitlement management to configure policies that manage access for external users.
- Use the B2B collaboration invitation APIs to customize your onboarding experiences.

### Integrate with Identity providers

Azure AD supports external identity providers like Facebook, Microsoft accounts, Google, or enterprise identity providers. You can set up federation with identity providers so your external users can sign in with their existing social or enterprise accounts instead of creating a new account just for your application. Learn more about identity providers for External Identities.

![Screenshot showing the Identity providers page](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/media/what-is-b2b/identity-providers.png)

### Create a self-service sign-up user flow (Preview)

With a self-service sign-up user flow, you can create a sign-up experience for external users who want to access your apps. As part of the sign-up flow, you can provide options for different social or enterprise identity providers, and collect information about the user. Learn about self-service sign-up and how to set it up.

You can also use API connectors to integrate your self-service sign-up user flows with external cloud systems. You can connect with custom approval workflows, perform identity verification, validate user-provided information, and more.

![Screenshot showing the user flows page](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/media/what-is-b2b/self-service-sign-up-user-flow-overview.png)

[Manage guest access with Azure AD access reviews](https://docs.microsoft.com/en-us/azure/active-directory/governance/manage-guest-access-with-access-reviews)

With Azure Active Directory (Azure AD), you can easily enable collaboration across organizational boundaries by using the Azure AD B2B feature. Guest users from other tenants can be invited by administrators or by other users. This capability also applies to social identities such as Microsoft accounts.

You also can easily ensure that guest users have appropriate access. You can ask the guests themselves or a decision maker to participate in an access review and recertify (or attest) to the guests' access. The reviewers can give their input on each user's need for continued access, based on suggestions from Azure AD. When an access review is finished, you can then make changes and remove access for guests who no longer need it.

> Note
>
> This document focuses on reviewing guest users' access. If you want to review all users' access, not just guests, see Manage user access with access reviews. If you want to review users' membership in administrative roles, such as global administrator, see Start an access review in Azure AD Privileged Identity Management.

### Prerequisites

- Azure AD Premium P2

For more information, License requirements.

### Create and perform an access review for guests

First, you must be assigned one of the following roles:

- global administrator
- User administrator
- (Preview) M365 or AAD Security Group owner of the group to be reviewed

Then, go to the Identity Governance page to ensure that access reviews is ready for your organization.

Azure AD enables several scenarios for reviewing guest users.

You can review either:

- A group in Azure AD that has one or more guests as members.
- An application connected to Azure AD that has one or more guest users assigned to it.

You can then decide whether to ask each guest to review their own access or to ask one or more users to review every guest's access.

## manage multiple directories

[Understand how multiple Azure Active Directory tenants interact](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/licensing-directory-independence)

In Azure Active Directory (Azure AD), each Azure AD organization is fully independent: a peer that is logically independent from the other Azure AD organizations that you manage. This independence between organizations includes resource independence, administrative independence, and synchronization independence. There is no parent-child relationship between organizations.

### Resource independence

- If you create or delete an Azure AD resource in one organization, it has no impact on any resource in another organization, with the partial exception of external users.
- If you register one of your domain names with one organization, it can't be used by any other organization.

### Administrative independence

If a non-administrative user of organization 'Contoso' creates a test organization 'Test,' then:

- By default, the user who creates a organization is added as an external user in that new organization, and assigned the global administrator role in that organization.
- The administrators of organization 'Contoso' have no direct administrative privileges to organization 'Test,' unless an administrator of 'Test' specifically grants them these privileges. However, administrators of 'Contoso' can control access to organization 'Test' if they control the user account that created 'Test.'
- If you add or remove an Azure AD role for a user in one organization, the change does not affect the roles that the user is assigned in any other Azure AD organization.

### Synchronization independence

You can configure each Azure AD organization independently to get data synchronized from a single instance of either:

- The Azure AD Connect tool, to synchronize data with a single AD forest.
- The Azure Active Directory Connector for Forefront Identity Manager, to synchronize data with one or more on-premises forests, and/or non-Azure AD data sources.

### Add an Azure AD organization

To add an Azure AD organization in the Azure portal, sign in to the Azure portal with an account that is an Azure AD global administrator, and select New.

> Note
>
> Unlike other Azure resources, your Azure AD organizations are not child resources of an Azure subscription. If your Azure subscription is canceled or expired, you can still access your Azure AD organization's data using Azure PowerShell, the Microsoft Graph API, or the Microsoft 365 admin center. You can also associate another subscription with the organization.
