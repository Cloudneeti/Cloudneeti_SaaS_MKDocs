# Office 365 Onboarding Guide

## Onboarding Steps

The following steps are required to onboard Office 365 to Cloudneeti
application.
	![onBoarding Steps](.././images/onboardingOffice365Subscription/Onboarding_Steps.png#thumbnail_1)

**1. Registering Cloudneeti application** includes registering the Cloudneeti
application with the Azure Active Directory (AD) tenant, providing access to
Microsoft Graph and granting admin consent to the Cloudneeti application.

**2. Adding the Office 365 subscription** includes adding Office 365
subscription information to the respective Cloud Account and waiting until the
first data collection is complete.

**3. Advanced security configuration** includes adding a script to the
customer’s Azure account and granting the required access rights.

Advanced security configurations (step 3) requires a Cloudneeti PowerShell agent
to be installed in an Azure subscription under the same tenant where the Office
365 subscription is located. The Cloudneeti PowerShell agent retrieves (A)
additional configuration information from the Office 365 subscription (there are
no Office 365 APIs to retrieve this information) and pushes (B) this information
as a JSON file to the Cloudneeti application.
	![Advanced Security Configuration](.././images/onboardingOffice365Subscription/Advanced_Security_Configuration.png#thumbnail)

| S. No.	 | Step                                                         | Product                      | Role                                                 |
|--------|--------------------------------------------------------------|------------------------------|------------------------------------------------------|
| 1      | Register Cloudneeti application                              | Microsoft Azure AD           | Global AD Administrator                              |
| 2      | Add Office 365 subscription                                  | Cloudneeti                   | License Admin                                        |
| 3      | Advanced security configurations (optional)                   |                              |                                                      |
|        | a. Create application password                               | Office 365 My Account portal | Global AD Administrator                              |
|        | b. Generate Cloudneeti API key                               | Cloudneeti API portal        | License Admin                                        |
|        | c. Provision M365 Data Collector to your Azure Subscription  | Microsoft Azure              | Subscription Owner or Azure Subscription Contributor |
|        | d .Apply delete lock                                         | Microsoft Azure              | Subscription Owner or Azure Subscription Contributor |

### Required Roles

People with the following roles are required to complete the Office 365
onboarding process.

| Role                    | Product                |
|-------------------------|------------------------|
| License Admin           | Cloudneeti application |
| Global AD Administrator | Microsoft Azure AD     |
| Global Administrator    | Microsoft Office 365   |
| Subscription Owner      | Microsoft Azure        |

The Cloudneeti application **License Admin** is assigned to an individual in the
customer’s organization who will be responsible for the configuration of the
respective Cloudneeti application License.

The Microsoft Azure **Global AD Administrator** role is required for App
Registration of the Cloudneeti application and granting access rights to the
Cloudneeti application.

Microsoft Office 365 **Global Administrator** role is required for App
Registration of the Cloudneeti application and granting access rights to the
Cloudneeti application.

For Advanced security configurations (optional) the following roles are required.

| **Role**                                                   | **Product**            |
|------------------------------------------------------------|------------------------|
| License Admin                                              | Cloudneeti application |
| Azure Subscription Owner or Azure Subscription Contributor | Microsoft Azure        |

The Microsoft Azure **Subscription Owner or Contributor** role is required to
provision a PowerShell agent to pull advanced security configuration information
and ingest to the Cloudneeti application.

### Required Permissions

The Cloudneeti application will be granted two read permissions to the Azure
AD and Cloudneeti Agent provisioned in Step 3 will require Office 365 Global Admin permission. Each
optional permission is linked to a number of security policies where this
permission is needed for data collection. If an optional permission is not
provided, Cloudneeti application will not collect the data for the related
policies. Security policies that require such permissions are listed later in
this document.

Login to [Azure Portal](https://portal.azure.com/){target=_blank} with **Global AD
Administrator** associated to your Office 365 Subscription role.

| **Object**                      | **Permission**                                                     |  **Required Role**  | **Step** | **Type**  | **Policies** |
|----------------------------------|--------------------------------------------------------------------|--------------------|----------|-----------|--------------|
| Azure Active Directory | Organisation Read All Microsoft Graph permissions                  |  Global AD Admin    | STEP 1   | mandatory | NA           |
| Azure Active Directory | Security Events Read All Microsoft Graph permissions               |  Global AD Admin    | STEP 1   | mandatory | NA           |
| Azure Active Directory | DeviceManagementConfiguration.Read.All Microsoft Graph permissions |  Global AD Admin    | STEP 1   | Optional  | [23](../../onboardingGuide/office365Subscription/#micorosft-graph-permission-devicemanagementconfigurationreadall)           |
| Azure Active Directory | Application Read All Microsoft Graph permissions                   |  Global AD Admin    | STEP 1   | Optional  | [2](../../onboardingGuide/office365Subscription/#micorosft-graph-permission-applicationreadall)            |
| Azure Active Directory | User Read All Microsoft Graph permissions                          |  Global AD Admin    | STEP 1   | Optional  | [2](../../onboardingGuide/office365Subscription/#micorosft-graph-permission-userreadall)            |
| Azure Active Directory | DeviceManagementApps.Read.All Microsoft Graph permissions          |  Global AD Admin    | STEP 1   | Optional  | [3](../../onboardingGuide/office365Subscription/#micorosft-graph-permission-devicemanagementappsreadall)            |
| Cloudneeti Agent       | Office 365 Global Administrator Access Permission                  |  Subscription Owner | STEP 3   | Optional  | [42](../../onboardingGuide/office365Subscription/#advanced-security-configurations)           |


## STEP 1: Register Cloudneeti Application

The following steps are executed by the Microsoft Azure **Global ADAdministrator** role.

Cloudneeti application can be registered either manually or using automation
script.

### 1.1 Manual
#### Register Cloudneeti application

Login to [Azure Portal](https://portal.azure.com/){target=_blank} with **Global AD
Administrator** associated to your Office 365 Subscription role.

1. Select **Azure Active Directory** associated to your Office 365 Subscription
    in the primary menu
2. Select **App Registrations** in the secondary menu
3. Click on **New Registration**
	![New Registration](.././images/onboardingOffice365Subscription/New_Registration.png#thumbnail)
4. Enter the name, for example "Cloudneeti"
5. Click **Register**
	![Register](.././images/onboardingOffice365Subscription/Register.png#thumbnail)
6. **Copy to clipboard** and paste the Application id to your notepad
	![Copy AppID](.././images/onboardingOffice365Subscription/Copy_AppID.png#thumbnail)

#### Add Client Secret

1. Click on **new client secret** in **Certificates & secrets** section (1)
2. Add **Description** and select expiry time
3. Click on **Add** (2)
4. **Copy to clipboard** (3) and paste the Client Secret to your notepad.
    **Note:** You will not be able to copy this value after you move away from
    this screen.
	![Certificates & secrets](.././images/onboardingOffice365Subscription/Copy_to_Clipboard.png#thumbnail)

#### Grant admin consent for API permissions

Add Microsoft Graph permissions and grant admin consent

The Microsoft graph permission **Organisation.Read.All** allows the app to read data in the organization's directory, such as user attributes, groups, and applications, without a signed-in user.

Microsoft graph permission **SecurityEvents.Read.All** allows the app to read Office 365 Secure Score data. 

Microsoft graph **DeviceManagementConfiguration.Read.All** allows the app to read properties of Microsoft Intune-managed device configuration and device compliance policies and their assignment to groups.

Microsoft graph **Application.Read.All** allows the app to read properties of application.

Microsoft graph **User.Read.All** allows the app to read properties of users.

Microsoft graph **DeviceManagementApps.Read.All** allows the app to read properties of Microsoft Intune-managed application configuration and application compliance policies and their assignment to groups.

You can find more details about the Microsoft Graph API permission [here](https://docs.microsoft.com/en-us/graph/permissions-reference){target=_blank}.

1. Click **API Permissions**
2. Select **Microsoft Graph**
	![API Permissions](.././images/onboardingOffice365Subscription/API_Permissions.png#thumbnail)
3. Select **Application**
	![Application](.././images/onboardingOffice365Subscription/Application.png#thumbnail)
4. Click **Add permission** and add the following information (1)

| API             | Permission Name                                                                                                     | Type        |
|-----------------|---------------------------------------------------------------------------------------------------------------------|-------------|
| Microsoft.Graph | Directory.Read.All [Refer here](https://docs.microsoft.com/en-us/graph/permissions-reference#directory-permissions){target=_blank} | Application |
| Microsoft.Graph | SecurityEvents.Read.All [Refer here](https://docs.microsoft.com/en-us/graph/permissions-reference#security-permissions){target=_blank}                                                                                           | Application |
| Microsoft.Graph | DeviceManagementConfiguration.Read.All [Refer here](https://docs.microsoft.com/en-us/graph/permissions-reference#intune-device-management-permissions){target=_blank}                                                                                           | Application |

5. Click **Grant admin consent** in the Grant consent section (2)
	![API Permission](.././images/onboardingOffice365Subscription/Grant_Admin_Consent.png#thumbnail)

### 1.2 Automated
#### Prerequisites 

The below steps are required for registering the Cloudneeti application in Azure
Tenant using PowerShell script.

| Activity                                                                                                              | Description                                                                                                                                                                                                               |
|-----------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1. Download and review **PowerShell script** for creation of the service principal                                    | The PowerShell script is used to create a service principal in Azure Tenant AD: [Download Link.](https://raw.githubusercontent.com/Cloudneeti/docs_cloudneeti/master/scripts/Create-ServicePrincipal-Office365Onboarding.ps1){target=_blank} |
| 2. **Workstation:** Ensure you have the latest PowerShell version (v5 and above)                                      | Verify PowerShell version by running the following command<br>`$PSVersionTable.PSVersion`<br>on the workstation where you will run the ServicePrincipal creation script. If the PowerShell version is lower than 5, then follow this link for installation of a later version: [Download Link.](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-windows-powershell?view=powershell-6){target=_blank}|
| 3. **Workstation:** Before executing the script, make sure there are no restrictions in running the PowerShell script | Use this PowerShell command:<br>``Set-ExecutionPolicy ` ``<br>``-Scope Process ` ``<br>`-ExecutionPolicy Bypass`<br>PowerShell contains built-in execution policies that limit its use as an attack vector. By default, the execution policy is set to Restricted, which is the primary policy for script execution. The bypass allows for running scripts and keeps the lowered permissions isolated to just the current running process. |
| 4. **Workstation:** Install Azure Modules to execute PowerShell commands within service principal automation script   | ``Install-Module ` ``<br>``-Name AzureAD ` ``<br>`-MinimumVersion 2.0.0.131`<br>It is a roll-up module for the Azure Resource Manager cmdlets. |

#### Register Cloudneeti Application 

Use the Create-ServicePrincipal-Office365Onboarding.ps1 script to create and
register a Cloudneeti Application.

1. Open PowerShell in administrator mode.

2. Go to the directory where Create-ServicePrincipal-Office365Onboarding.ps1
    was downloaded earlier.

3. Run the below command to create a service principal
	<pre>
		<code>```
		.\Create-ServicePrincipal-Office365Onboarding.ps1 `
			-azureActiveDirectoryId <Active_Directory_Id> `
			-servicePrincipalName <data_collector_name> `
			-expirationPeriod 1year
		```</code>
	</pre>
4. The script will prompt the login screen
5. Log in with Global AD Administrator or Application Administrator user
    credentials.
6. Store service principal information from the output in a notepad. This
    information will be needed while onboarding the Azure account in the
    Cloudneeti portal.
	![Register Cloudneeti Application](.././images/onboardingOffice365Subscription/Register_Cloudneeti_Application.png#thumbnail)

#### Grant admin consent for API permissions

The Azure **Global AD Administrator** needs to grant permissions to the
Cloudneeti application to be able to collect the Azure AD data.

1. Click **Azure Active Directory**

2. Click **App registrations**

3. Find and select Cloudneeti application under Display Name

4. Go to **API permissions**

5. Click **Grant admin consent** in the Grant consent section

6. Review permissions as below listed (1)

	| API             | Permission Name                                                                                                     | Type        |
|-----------------|---------------------------------------------------------------------------------------------------------------------|-------------|
| Microsoft.Graph | 
Organization.Read.All [Refer here](https://docs.microsoft.com/en-us/graph/permissions-reference#organization-permissions){target=_blank} | Application |
| Microsoft.Graph | SecurityEvents.Read.All [Refer here](https://docs.microsoft.com/en-us/graph/permissions-reference#security-permissions){target=_blank}  | Application |
| Microsoft.Graph | DeviceManagementConfiguration.Read.All [Refer here](https://docs.microsoft.com/en-us/graph/permissions-reference#intune-device-management-permissions){target=_blank} | Application |
| Microsoft.Graph | DeviceManagementApps.Read.All [Refer here](https://docs.microsoft.com/en-us/graph/permissions-reference#intune-device-management-permissions){target=_blank}     | Application |
| Microsoft.Graph | Application.Read.All [Refer here](https://docs.microsoft.com/en-us/graph/permissions-reference#application-resource-permissions){target=_blank}          | Application |
| Microsoft.Graph | User.Read.All [Refer here](https://docs.microsoft.com/en-us/graph/permissions-reference#user-permissions){target=_blank}  | Application |

7. Click **Grant admin consent** in the Grant consent section (2)
	![Grant_Admin_Consent](.././images/onboardingOffice365Subscription/Grant_Admin_Consent.png#thumbnail)

### 1.3 Collect Information


The Cloudneeti application **License Admin** requires this information to add an
Azure subscription as a cloud account.

| Information                              | Portal to use     | User               |
|------------------------------------------|-------------------|--------------------|
| Azure Directory ID                       | Microsoft Azure   | Subscription Owner |
| Azure Directory Domain name              | Microsoft Azure   | Subscription Owner |
| Registered Cloudneeti Application ID     | Microsoft Azure   | Subscription Owner |
| Registered Cloudneeti Application Secret | Microsoft Azure   | Subscription Owner |

#### Azure Directory ID

1. Click on **Azure Active Directory** on the primary menu
2. Click on **Properties** on the secondary menu
3. Copy **Directory ID** to a notepad
	![Directory ID](.././images/onboardingOffice365Subscription/Directory_ID.png#thumbnail)

#### Azure AD Domain Name

1. Click on **Azure Active Directory** on the primary menu
2. Click on **Overview** on the secondary menu
3. Copy **Domain name** to a notepad
	![Azure Domain Name](.././images/onboardingOffice365Subscription/Azure_Domain_Name.png#thumbnail)

#### Registered Cloudneeti Application ID 
1. Select **Azure Active Directory** in the primary menu
2. Select **App Registrations** in the secondary menu
3. Select Cloudneeti Application registered in [Step 1](.././onboardingOffice365Subscription/#step-1-register-cloudneeti-application)
	![Registered Cloudneeti Application ID](.././images/onboardingOffice365Subscription/Cloudneeti_Application_ID.png#thumbnail)
4. Copy the Cloudneeti Application id
	![Cloudneeti Application id](.././images/onboardingOffice365Subscription/Copy_AppID.png#thumbnail)

#### Registered Cloudneeti Application Secret 

1. Select **Azure Active Directory** in the primary menu
2. Select **App Registrations** in the secondary menu
3. Select Cloudneeti Application registered in [Step 1](.././onboardingOffice365Subscription/#step-1-register-cloudneeti-application)
4. Click on **new client secret** in **Certificates & secrets** section (1)
5. Add **Description** and select expiry time
6. Click on **Add** (2)
7. **Copy to clipboard** and paste the Client Secret to your notepad. **Note:**
    You will not be able to copy this value after you move away from this
    screen. (3)
	![Registered Cloudneeti Application Secret](.././images/onboardingOffice365Subscription/Copy_to_Clipboard.png#thumbnail)

## STEP 2: Add Office 365 subscription

The following steps are done by the Cloudneeti application **License Admin**
role.

### 2.1 Activate the License
1. Log in to the Cloudneeti application with **License Admin** role.
2. Click on **Activate License**
	![Activate License](.././images/onboardingOffice365Subscription/Activate_License.png#thumbnail)

### 2.2 Add Cloud Account

1. Select cloud connector for **Office 365**
	![Add Cloud Account](.././images/onboardingOffice365Subscription/Add_Cloud_Account.png#thumbnail)
2. Fill in the Account Name, Domain name, Azure Tenant Id (Domain ID) Azure
    Application ID and Azure Application Password.
3. Click on **Add Account**
	![Add Account](.././images/onboardingOffice365Subscription/Add_Account.png#thumbnail)
4. You receive a confirmation that Office 365 has been added and data
    collection started.
	![confirmation](.././images/onboardingOffice365Subscription/Confirmation.png#thumbnail)

### 2.3 Data Collection

Once Azure subscription is added to the cloud account under Cloudneeti License,
it requires about 5 minutes for the data to be collected and processed, before
they can be displayed in Cloudneeti dashboards.

1. Select **Dashboard** on the menu
2. Review the data on dashboard
	![Dashboard](.././images/onboardingOffice365Subscription/Dashboard.png#thumbnail)

## STEP 3: Advanced security configurations
**This step is optional.**

The following steps are done by Microsoft Azure **Subscription Owner (or
Subscription Contributor)** role.

An Azure Automation Account resource is deployed to collect data for additional
security policies. The Office 365 control plane exposes the data only through
PowerShell that needs to run under a Global AD administrator credential (with
MFA access).

To ensure that Cloudneeti does not ever store/have access to a global AD
administrator, it is recommended to deploy a small PowerShell script under
customer’s control in their own Azure subscription. The metadata collected after
running a script is then pushed to a Cloudneeti API that you registered during
the Cloudneeti API key generation.

### 3.1 Collect Information

| **Information**                                                                                 | **Source / Portal to use** | **User**                |
|-------------------------------------------------------------------------------------------------|----------------------------|-------------------------|
| [Cloudneeti License Id](.././office365Subscription/#license-id)                        | Cloudneeti                 | License Admin           |
| [Cloudneeti Account Id ](.././office365Subscription/#account-id)                         | Cloudneeti                 | License Admin           |
| [Office Directory Id  ](.././office365Subscription/#azure-directory-id)                     | Cloudneeti                 | License Admin           |
| [Cloudneeti Application Id ](.././office365Subscription/#registered-cloudneeti-application-id)    | Cloudneeti                 | License Admin           |
| [Cloudneeti Environment ](.././office365Subscription/#cloudneeti-artifacts-and-data-collector-details)  | Cloudneeti Team            | License Admin           |
| [Cloudneeti API key](.././office365Subscription/#generate-cloudneeti-api-key)   | Cloudneeti Team            | License Admin           |
| [Artifacts Name ](.././office365Subscription/#cloudneeti-artifacts-and-data-collector-details) | Cloudneeti Team            | License Admin           |
| [Data Collector Version ](.././office365Subscription/#cloudneeti-artifacts-and-data-collector-details) | Cloudneeti Team            | License Admin           |
| [Cloudneeti M365 data collector artifacts storage access Key](.././office365Subscription/#cloudneeti-artifacts-and-data-collector-details) | Cloudneeti Team            | License Admin           |
| [Cloudneeti data collector Service Principal secret](.././office365Subscription/#registered-cloudneeti-application-secret)  | Microsoft Azure            | Subscription Owner      |
| [Azure Subscription Id ](.././office365Subscription/#azure-subscription-id)    | Microsoft Azure            | Subscription Owner      |
| [Office Domain ](.././office365Subscription/#office-365-details)       | Office 365 portal          | Office 365 user with Exchnage Admin and SharePoint Admin role |
| [Office Admin Email Id  ](.././office365Subscription/#enable-mfa-and-create-application-password)     | Office 365 portal          | Office 365 user with Exchnage Admin and SharePoint Admin role |
| [Office 365 App Password](.././office365Subscription/#enable-mfa-and-create-application-password)   | Office 365 portal          | Office 365 user with Exchnage Admin and SharePoint Admin role|

#### Cloudneeti license and account details

Login to Cloudneeti portal as a License Admin.

##### License id

1. Navigate to **Features and Quota​s** under **Configurations**
2. Copy license ID and paste to notepad.
	![License id](.././images/onboardingOffice365Subscription/License_Id.png#thumbnail)

##### Account id
1. Navigate to **Cloud Accounts** in **Configurations**
2. Copy account ID and paste to notepad.
	![Manage Accounts](.././images/onboardingOffice365Subscription/Manage_Accounts.png#thumbnail)

#### Office 365 details

Office Admin id and office domain can be retrieved from the user id used for
signing-in like userid\@domain.com.

Cloudneeti platform queries and processes Office 365 meta-data using a
non-interactive login credential. As the background job processing is
non-interactive it’ll require a service account credential. The following is the
process outlined to create a secure service account credential.

Sign into your Office 365 portal (e.g. <https://outlook.office.com/mail/inbox>)
as global administrator.

##### Create Office 365 user

###### 1. Create Office 365 user with roles **SharePoint Administrator** and **Exchange Administrator**

1. Login to [office portal](https://www.office.com){target=_blank} with Office Administrator role
2. Click **Admin** to go to **M365 Admin center**
	
	![Admin Center](.././images/onboardingOffice365Subscription/M365AdminCenter.png#thumbnail)

3. Select **Users** 
4. Click **Add a user**
5. Fill in Basics and Product License details

	![Basic info](.././images/onboardingOffice365Subscription/addUser.png#thumbnail)

6. Select **SharePoint Administrator** and **Exchange Administrator** from Common specialist roles
7. Click **Finish adding**

	![Basic info](.././images/onboardingOffice365Subscription/AdminRoles_Save.png#thumbnail)

###### 2. Assign User with Compliance Manager role

1. Login to [Azure portal](https://portal.azure.com/){target=_blank} with AD admin role

2. Navigate to the User created

    ![Basic info](.././images/onboardingOffice365Subscription/CM1.png#thumbnail)

3. Assign **Compliance Manager** role to the User

    ![Basic info](.././images/onboardingOffice365Subscription/CM2.png#thumbnail)


    ![Basic info](.././images/onboardingOffice365Subscription/CM3.png#thumbnail)


##### Enable MFA and create application password

Follow [link](https://docs.microsoft.com/en-us/office365/admin/security-and-compliance/set-up-multi-factor-authentication?view=o365-worldwide#set-up-multi-factor-authentication-in-the-new-microsoft-365-admin-center){target=_blank} to enable MFA


Sign into your Office 365 portal (e.g. <https://outlook.office.com/mail/inbox>)
as user created above [step](.././office365Subscription/#create-office-365-user)

1. Choose **My Account Settings**
	![My Account Settings](.././images/onboardingOffice365Subscription/My_Account_Settings.png#thumbnail)
2. Select **Security & privacy**
3. Select **Additional security verification**
    Note : If you do not see additional security configuration them make sure you have enabled MFA for your user. 
4. Click **Create and manage app passwords**
	![Create and manage app passwords](.././images/onboardingOffice365Subscription/Manage_Password.png#thumbnail)
5. Select **app passwords**
6. Click **create**
7. Get an app password
	![App Password](.././images/onboardingOffice365Subscription/App_Password.png#thumbnail)

#### Generate Cloudneeti API key

##### Sign-up on Cloudneeti API portal.

1. Go to [API portal](https://portal.cloudneeti.com/){target=_blank} and Sign up.

2. Fill the required fields in the sign-up form

3. You will receive a confirmation mail for sign-up, Click on the confirmation
    link.

4. The confirmation link will ask you for change password (info: You can use
    the password your used when signing up)

5. You are signed up successfully

##### Retrieve and activate API key

Retrieve and activate your API key using the Cloudneeti API portal

1. Click on **PRODUCTS**
2. Select **Unlimited**
	![Cloudneeti API](.././images/onboardingOffice365Subscription/Cloudneeti_API.png#thumbnail)
3. Click on **Subscribe**
	![Subscribe](.././images/onboardingOffice365Subscription/API_Subscribe.png#thumbnail)

This will notify Cloudneeti to activate your API subscription access. Please
wait for activation to be done. When Cloudneeti activates your subscription, you
will get an email notification.

Once you receive the confirmation, proceed with the following steps.

1. Click on **Username**
2. Select **Profile**
3. Click on **Show**
4. Copy the **Primary key** to your notepad.
	![Primary key](.././images/onboardingOffice365Subscription/Primary_key.png#thumbnail)



#### Cloudneeti artifacts and data collector details
Contact Cloudneeti Team for:

-   Cloudneeti environment

-   ArtifactsName

-   DataCollectorVersion

-   ArtifactsAccessKey


##### Azure details

Login to Azure portal [https://portal.azure.com](https://portal.azure.com){target=_blank} as subscription owner.

##### Azure Subscription ID

1. Choose your Azure AD tenant by selecting your **Azure subscription** in the
    top right corner of the page
2. Select **Default Directory**
	![Default Directory](.././images/onboardingOffice365Subscription/Default_Directory.png#thumbnail)
3. Click on **Subscriptions** (1) on the primary menu
4. Select the desired **Azure subscription** (2)
	![Azure subscription](.././images/onboardingOffice365Subscription/Azure_Subscription.png#thumbnail)
5. Copy **Subscription ID** to a notepad
	![Subscription ID](.././images/onboardingOffice365Subscription/Subscription_Id.png#thumbnail)



### 3.2 Provision Office 365 data collector 


Login to Azure portal [https://portal.azure.com](https://portal.azure.com){target=_blank} as Subscription Contributor or
Subscription Owner access.

Switch to Azure AD with the Azure Subscription with pre-requisite access.

1. Open **CloudShell**
2. Click on **Cloudshell** icon on the navigation bar to open Cloudshell
3. Choose PowerShell from shell drop down
4. Select **storage**
	![CloudShell](.././images/onboardingOffice365Subscription/CloudShell.png#thumbnail)
5. Execute below command in Cloudshell to download the Cloudneeti data
    collector provisioning script.
	<pre>
	<code>```
		wget https://raw.githubusercontent.com/Cloudneeti/docs_cloudneeti/master/scripts/Provision-M365DataCollector.ps1 -O Provision-M365DataCollector.ps1
	```</code>
	</pre>
6. Switch to the User directory
	<pre>
	<code>```
		cd $User
	```</code>
	</pre>
7. Run provisioning script with inline parameters
	<pre>
	<code>```
		./Provision-M365DataCollector.ps1 `
			-CloudneetiLicenseId <Cloudneeti License Id> `
			-CloudneetiAccountId <Cloudneeti Account Id> `
			-CloudneetiEnvironment <Cloudneeti Environment> `
			-CloudneetiApplicationId <Cloudneeti Data Collector Registered Application Id> `
			-ArtifactsName <Cloudneeti office 365 Data Collector Artifact Name> `
			-DataCollectorVersion <Cloudneeti Office 365 Data Collector Version> `
			-OfficeDomain <Office 365 Domain Name> `
			-OfficeDirectoryId <Office 365 Directory Id> `
			-OfficeAdminEmailId <Office 365 Administator Email Id> `
			-AzureSubscriptionId <Azure Subscription Id where office 365 datacollector resouces will be created> `
			-DataCollectorName <Office 365 Data Collector Name> `
			-Location <Default EastUs2>
	```</code>
	</pre>
Note: Contact Cloudneeti Team for ArtifactsName, DataCollectorVersion and
ArtifactsAccessKey
8. The script will execute and prompt you for below details:
   - Cloudneeti API key </br>
   - Cloudneeti Data collector application secret </br>
   - Cloudneeti Office 365 data collector artifacts storage access Key </br>
   - User Password or Office 365 App Password  
    - User Password </br>
      Advanced security configuration done using **User Password** will enable all 44 policies listed [here.](.././office365Subscription/#advanced-security-configuration))
    - Office 365 App Password </br>
      Advanced security configuration done using **Office 365 App Password** will **not enable 18 policies** of category M365 - Identity listed [here.](.././office365Subscription/#advanced-security-configuration))
   
9. This will create a runbook inside automation account

### 3.3 Apply delete lock

Apply delete lock to prevent accidental deletion of the data collection resource
group in your Azure Subscription.

1. Navigate to M365 data collector resource group
    (**cloudneeti-m365-datacollector-rg**).

2. Click on **Locks** (1)

3. Click **Add** (2)

4. Enter **Lock name** DoNotDelete (3)

5. Select **Lock type** as Delete (4)

6. Add **Notes** (Do not delete M365 data collector resource group) (5)

7. Click **OK** (6)
	![Apply Delete Lock](.././images/onboardingOffice365Subscription/Locks.png#thumbnail)

### 3.4 Modify the data collection schedule

Set the automation account schedule before the daily Cloudneeti data collection
time.

1. Go to **M365 data collector** resource group
2. Select **Automation account**
3. Click on **Schedules**
4. Select **Schedule**
	![Schedule](.././images/onboardingOffice365Subscription/Schedule.png#thumbnail)
5. Modify the schedule **Time** (set time about 1 hour before the daily
    Cloudneeti data collection time)
6. Click **Save**
	![Modify and Save](.././images/onboardingOffice365Subscription/Modify_Save.png#thumbnail)

Cloudneeti portal will show details for policies from next scan.

<!-- ## NEXT STEPS

[Configure Notifications](../../administratorGuide/configureNotifications/) -->

## Security Policies that Require Permissions

The following Security Policies will be excluded if advanced security
configurations are not done.

### Micorosft graph permission - DeviceManagementConfiguration.Read.All
Microsoft graph permission DeviceManagementConfiguration.Read.All is required to collect data for device related security policies listed below.

| **Category**           | **Policy Title**                                                                                                     |
|---------------|-----------------------------------------------------------------|
| M365 - Device          | Ensure that mobile devices require complex passwords with atleast two character sets to prevent brute force attacks  |
| M365 - Device          | Ensure that mobile device encryption is enabled to prevent unauthorized access to mobile data                        |
| M365 - Device          | Require mobile devices to manage email profile                                                                       |
| M365 - Device          | Ensure that mobile devices require a complex password with a minimum password length to prevent brute force attacks  |
| M365 - Device          | Ensure that mobile devices are set to never expire passwords                                                         |
| M365 - Device          | Require mobile devices to use a password                                                                             |
| M365 - Device          | Ensure that users cannot connect from devices that are jail broken or rooted                                         |
| M365 - Device          | Ensure that mobile devices require complex passwords to prevent brute force attacks                                  |
| M365 - Device          | Enable mobile devices to wipe on multiple sign-in failures to prevent brute force compromise                         |
| M365 - Device          | Ensure that settings are enable to lock multiple devices after a period of inactivity to prevent unauthorized access |
| M365 - Device          | Ensure that mobile device password reuse is prohibited                                                               |
| M365 - Device          | Create a Microsoft Intune Compliance Policy for iOS                                                                  |
| M365 - Device          | Create a Microsoft Intune Compliance Policy for Android                                                              |
| M365 - Device          | Create a Microsoft Intune Compliance Policy for Android for Work                                                     |
| M365 - Device          | Create a Microsoft Intune Compliance Policy for Windows                                                              |
| M365 - Device          | Create a Microsoft Intune Compliance Policy for macOS                                                                |
| M365 - Device          | Create a Microsoft Intune Configuration Profile for iOS                                                              |
| M365 - Device          | Create a Microsoft Intune Configuration Profile for Android                                                          |
| M365 - Device          | Create a Microsoft Intune Configuration Profile for Android for Work                                                 |
| M365 - Device          | Create a Microsoft Intune Configuration Profile for Windows                                                          |
| M365 - Device          | Create a Microsoft Intune Configuration Profile for macOS                                                            |
| M365 - Device          | Enable Enhanced Jailbreak Detection in Microsoft Intune                                                              |
| M365 - Device          | Ensure that devices connecting have local firewall enabled                                                           |

### Micorosft graph permission - Application.Read.All
Microsoft graph permission Application.Read.All is required to collect data for application related security policies listed below.

| Category               | Policy Title                                                                                                         |
|---------------|-----------------------------------------------------------------|
| M365 - Identity        | Ensure that Service Principal Certificates are renewed before it expires                                             |
| M365 - Apps            | Ensure that AD Application keys are rotated before they expires                                                      |

### Micorosft graph permission - User.Read.All
Microsoft graph permission User.Read.All is required to collect data for user related security policies listed below.

| Category               | Policy Title                                                                                                         |
|---------------|-----------------------------------------------------------------|
| M365 - Identity        | Enforce the policy to set Password to ‘always' expire in Azure Active Directory for all Organization Users           |
| M365 - Identity        | Ensure that there are no guest users                                                                                 |

### Micorosft graph permission - DeviceManagementApps.Read.All
Microsoft graph permission DeviceManagementApps.Read.All is required to collect data for device related security policies listed below.

| Category      | Policy Title                                                    |
|---------------|-----------------------------------------------------------------|
| M365 - Device | Create a Microsoft Intune App Protection Policy for iOS         |
| M365 - Device | Create a Microsoft Intune App Protection Policy for Android     |
| M365 - Device | Create a Microsoft Intune Windows Information Protection Policy |

### Advanced security configurations

The advanced security policy data collector enables the following 43 policies as
available in the Center for Internet Security (CIS) Microsoft 365 benchmark
(Reference
[here](https://www.cloudneeti.com/2019/01/assure-microsoft-365-security-and-compliance-with-cloudneeti/){target=_blank}).

Advanced security configuration done using Office 365 App Password will not enable 18 policies of category M365 - Identity listed in below table.

| **Category**                            |                                                                                                                      |
|-----------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Account / Authentication                | Ensure modern authentication for SharePoint applications is required                                                 |
| Application Permissions                 | Ensure calendar details sharing with external users is disabled                                                      |
| Application Permissions                 | Ensure O365 ATP SafeLinks for Office Applications is Enabled                                                         |
| Application Permissions                 | Ensure Office 365 ATP for SharePoint, OneDrive, and Microsoft Teams is Enabled                                       |
| Data Management                         | Ensure that external users cannot share files, folders, and sites they do not own                                    |
| Email Security / Exchange Online        | Ensure that DKIM is enabled for all Exchange Online Domains                                                          |
| Email Security / Exchange Online        | Ensure the Common Attachment Types Filter is enabled                                                                 |
| Email Security / Exchange Online        | Ensure that SPF records are published for all Exchange Domains                                                       |
| Email Security / Exchange Online        | Ensure DMARC Records for all Exchange Online domains are published                                                   |
| Email Security / Exchange Online        | Ensure notifications for internal users sending malware is Enabled                                                   |
| Email Security / Exchange Online        | Ensure Exchange Online Spam Policies are set correctly                                                               |
| Email Security / Exchange Online        | Ensure mail transport rules do not whitelist specific domains                                                        |
| Email Security / Exchange Online        | Ensure Client Rules Forwarding Block is enabled                                                                      |
| Email Security / Exchange Online        | Ensure that an anti-phishing policy has been created                                                                 |
| Auditing                                | Enable Microsoft 365 audit log search                                                                                |
| Auditing                                | Ensure account provisioning activity report is reviewed weekly                                                       |
| Auditing                                | Ensure the spoofed domains report is reviewed weekly                                                                 |
| Auditing                                | Ensure user role group changes is reviewed at least every week                                                       |
| Storage                                 | Ensure document sharing is being controlled by domains with whitelist or blacklist                                   |
| M365 - Account / Authentication         | Ensure modern authentication for Exchange Online is enabled                                                          |
| M365 - Data Management                  | Use custom sensitive infromation type classification for information protection                                      |
| M365 - Email Security / Exchange Online | Ensure MailTips are enabled for end users                                                                            |
| M365 - Email Security / Exchange Online | Ensure basic authentication for Exchange Online is disabled                                                          |
| M365 - Storage                          | Block OneDrive for Business sync from unmanaged devices                                                              |
| M365 - Identity                         | Ensure that 'Number of methods required to reset' is set to '2'                                                      |
| M365 - Identity                         | Ensure that 'Number of days before users are asked to re-confirm their authentication information' is not set to '0' |
| M365 - Identity                         | Ensure that 'Notify all admins when other admins reset their password?' is set to 'Yes'                              |
| M365 - Identity                         | Ensure that 'Notify users on password resets?' is set to 'Yes'                                                       |
| M365 - Identity                         | Ensure that 'Users can consent to apps accessing company data on their behalf' is set to 'No'                        |
| M365 - Identity                         | Ensure that 'Users can add gallery apps to their Access Panel' is set to 'No'                                        |
| M365 - Identity                         | Ensure that 'Users can register applications' is set to 'No'                                                         |
| M365 - Identity                         | Ensure that 'Guest user permissions are limited' is set to 'Yes'                                                     |
| M365 - Identity                         | Ensure that 'Members can invite' is set to 'No'                                                                      |
| M365 - Identity                         | Ensure that 'Guests can invite' is set to 'No'                                                                       |
| M365 - Identity                         | Ensure that 'Restrict access to Azure AD administration portal' is set to 'Yes'                                      |
| M365 - Identity                         | Ensure that 'Self-service group management enabled' is set to 'No'                                                   |
| M365 - Identity                         | Ensure that 'Users can create security groups' is set to 'No'                                                        |
| M365 - Identity                         | Ensure that 'Users who can manage security groups' is set to 'None'                                                  |
| M365 - Identity                         | Ensure that 'Users can create Office 365 groups' is set to 'No'                                                      |
| M365 - Identity                         | Ensure that 'Users who can manage Office 365 groups' is set to 'None'                                                |
| M365 - Identity                         | Ensure that 'Enable All Users group' is set to 'Yes'                                                                 |
| M365 - Identity                         | Ensure that 'Require Multi-Factor Auth to join devices' is set to 'Yes'                                              |
| M365 - Data                             | Enable audit data recording                                                                                          |
| M365 - Data                             | Ensure Advanced Threat Protection safe attach policy is Enabled                                                      |

<!-- | M365 - Data                             | Ensure Advanced Threat Protection safe links policy is Enabled                                                       | -->


<div class="policy-json-code">
<pre>
<code>
	[
    {
        "ttl": xxxxxxxxx,
        "id": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        "_rid": "SvAwAJo4UAdIVAQAAAAADA==",
        "_self": "dbs/SvAwAA==/colls/SvAwAJo4UAc=/docs/SvAwAJo4UAdIVAQAAAAADA==/",
        "_etag": "\"1900267d-0000-0100-0000-5d1ee0810000\"",
        "AccountId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXxx",
        "Type": "M365ScanedData",
        "PartitionKey": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXx",
        "ScanedDataFromPS": [
            {
                "Status": "Green",
                "TotalResourceCount": 1,
                "PolicyId": "CalExtSharing",
                "PassedResourceCount": 1
            },
            {
                "Status": "Red",
                "TotalResourceCount": 1,
                "PolicyId": "ATPSafeLinks",
                "PassedResourceCount": 0
            },
            {
                "Status": "Green",
                "TotalResourceCount": 1,
                "PolicyId": "ATPEnabled",
                "PassedResourceCount": 1
            },
            {
                "Status": "Red",
                "TotalResourceCount": 1,
                "PolicyId": "AttachmentFilter",
                "PassedResourceCount": 0
            },
            {
                "Status": "Green",
                "TotalResourceCount": 1,
                "PolicyId": "OutbondSpam",
                "PassedResourceCount": 1
            },
            {
                "Status": "Green",
                "TotalResourceCount": 1,
                "PolicyId": "TransportRule",
                "PassedResourceCount": 1
            },
            {
                "Status": "Green",
                "TotalResourceCount": 1,
                "PolicyId": "ClientRuleFrwd",
                "PassedResourceCount": 1
            },
            {
                "Status": "Green",
                "TotalResourceCount": 1,
                "PolicyId": "AntiPhishing",
                "PassedResourceCount": 1
            },
            {
                "Status": "Green",
                "TotalResourceCount": 1,
                "PolicyId": "DKIMExchange",
                "PassedResourceCount": 1
            },
            {
                "Status": "Green",
                "TotalResourceCount": 1,
                "PolicyId": "InternalMalware",
                "PassedResourceCount": 1
            },
            {
                "Status": "Green",
                "TotalResourceCount": 1,
                "PolicyId": "AuditLogSearch",
                "PassedResourceCount": 1
            },
            {
                "Status": "Red",
                "TotalResourceCount": 1,
                "PolicyId": "UserRoleGroup",
                "PassedResourceCount": 0
            },
            {
                "Status": "Green",
                "TotalResourceCount": 1,
                "PolicyId": "AddedAccount",
                "PassedResourceCount": 1
            },
            {
                "Status": "Green",
                "TotalResourceCount": 1,
                "PolicyId": "DomainSpoof",
                "PassedResourceCount": 1
            },
            {
                "Status": "Red",
                "TotalResourceCount": 1,
                "PolicyId": "ModernAuthSPO",
                "PassedResourceCount": 0
            },
            {
                "Status": "Red",
                "TotalResourceCount": 1,
                "PolicyId": "ExtUserSharing",
                "PassedResourceCount": 0
            },
            {
                "Status": "Red",
                "TotalResourceCount": 1,
                "PolicyId": "DomainControl",
                "PassedResourceCount": 0
            },
            {
                "Status": "Green",
                "TotalResourceCount": 1,
                "PolicyId": "SPFExchange",
                "PassedResourceCount": 1
            },
            {
                "Status": "Red",
                "TotalResourceCount": 1,
                "PolicyId": "DMARCExchange",
                "PassedResourceCount": 0
            }
        ],
        "ContractId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        "LastModified": "0001-01-01T00:00:00",
        "LastModifiedBy": null,
        "AppVersion": "xxx",
        "_attachments": "attachments/",
        "_ts": xxxxxxx
    }
]
</code>
</pre>
</div>


##	OFFBOARDING

### Delete registered Cloudneeti Application

Cloudneeti Application within Customer’s Active Directory is removed which eventually will remove permissions, roles assigned on subscription/subscriptions. This will stop data collection for related cloud
accounts – Azure subscriptions related to Office 365 accounts in Cloudneeti.

1.  Go to **Azure Active Directory**

2.  Click on **App Registration**

3.  Select **Cloudneeti Application**

4.  Click on **Delete** to remove Cloudneeti Application registration

    ![Apply Delete Lock](.././images/onboardingOffice365Subscription/DeleteAppReg.png#thumbnail)

### Delete Office365 Advanced Security Policy Data Collector

Deletion of the advanced security policy data collector within Customer’s Active
Directory is removed which eventually will remove permissions, roles assigned on
subscription/subscriptions. This will stop data collection for related cloud
accounts in Cloudneeti

1.  Navigate to M365 data collector resource group
    (**cloudneeti-m365-datacollector-rg**).

2.  Click **Locks** (1)

3.  **Delete** lock “DoNotDelete” (2)

4.  **Select** and **Delete Automation account** within resource group
    **cloudneeti-m365-datacollector-rg**

    ![Apply Delete Lock](.././images/onboardingOffice365Subscription/DeleteRGLock.png#thumbnail)

### Delete cloud account in Cloudneeti application

Please send a request to <support@cloudneeti.com> to delete this cloud account
under your license.
