---
services: billing
platforms: dotnet
author: bryanla
---

# Microsoft Azure Billing API Code Samples: RateCard API
A simple console application that calls the Azure Billing RateCard REST API to retrieve the list of resources per Azure offer in a given subscription, along with the pricing details for each resource. The Microsoft Azure Billing APIs enable integration of Azure Billing information into your applications, providing new insights into your consumption of Azure resources, allowing you to accurately predict and manage your Azure resource consumption costs. 

To learn more about the Billing Usage and RateCard APIs, visit the overview article [Gain insights into your Microsoft Azure resource consumption](https://azure.microsoft.com/documentation/articles/billing-usage-rate-card-overview/).  Please note, the Billing APIs were in Preview at the time of this writing. Please refer to MSDN article [Azure Billing REST API Reference](https://msdn.microsoft.com/library/azure/mt218998.aspx) for current details on the Billing APIs.

## Complete billing samples index
Below is the list of all of the available Azure Billing API code samples:

-	[billing-dotnet-usage-api](https://github.com/Azure-Samples/billing-dotnet-usage-api) - This sample will help you get started with the Usage API.
-	[billing-dotnet-ratecard-api](https://github.com/Azure-Samples/billing-dotnet-ratecard-api/) - This sample help you get started with the RateCard API.
-	[billing-dotnet-webapp-multitenant](https://github.com/Azure-Samples/billing-dotnet-webapp-multitenant/) - This Multi-Tenant sample creates a WebApp that allows the signed-in user to give it consent, to call the Azure Graph API and the Usage API on the user's behalf. It also shows the OAuth flows required to get consent for the ‘Reader’ role, to access the list of Microsoft Azure subscriptions that the user wants to allow access to. 

## How To Run This Sample
To run this sample you will need:

- Visual Studio 2013 or higher
- An Internet connection
- An Azure subscription (a free trial is sufficient)

You will also need to be comfortable with the following tasks:

- Using the [Azure portal](https://portal.azure.com) (or working with your administrator) to do configuration work 
- Using Git and Github to bring the sample code down to your local machine
- Using Visual Studio to edit configuration files, build, and run the sample

Every Azure subscription has an associated Azure Active Directory (AAD) tenant.  If you don't already have an Azure subscription, you can get a free subscription by signing up at [http://wwww.windowsazure.com](http://www.azure.com).  

### Step 1: Configure a Native Client application in your AAD tenant
Before you can run the sample application, you will need to allow it to access your AAD tenant for authentication and authorization to access the Billing APIs.  If you already have a Native Client Application configured that you would like to use (and it is configured according to the steps below), you can jump to Step 2.

To configure a new AAD application:

1. Sign in to the [Azure portal](https://portal.azure.com), using credentials that have been granted service administrator/co-administrator access on the subscription which is trusting your AAD tenant, and granted Global Administrator access in the AAD tenant. See [Role Based Access Control](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) for details on managing the service administrator and co-administrators.
2. Select the AAD tenant you wish to use, and go to the "Applications" page.
3. From there, you can use the "Add" feature to "Add a new application my organization is developing".
4. Provide a name (ie: ConsoleApp-Billing-RateCard or similar) for the new application.
5. Be sure to select the "Native Client Application" type, then specify a valid URL for "Redirect URI" (which can be http://localhost/ for the purposes of this sample), and click the check mark to save.
6. After you've added the new application, select it again within the list of applications and click "Configure" so you can make sure the sample app will have permissions to access the Windows Azure Service Management APIs, which is the permission used to secure the Billing APIs.  
7. Scroll down to the to the "Permissions to other applications" section of your newly created application's configuration page.  Then click the "Add Application" button, select the "Windows Azure Service Management" row, and click the check mark to save.  After saving, hover the "Delegated Permissions" area on the right side of the "Windows Azure Service Management" row, click the "Delegated Permissions" drop down list, select the "Access Azure Service Management (preview)" option, and click "Save" again.

    **NOTE**: the "Windows Azure Active Directory" permission "Enable sign-on and read users' profiles" is enabled by default.  It allows users to sign in to the application with their organizational accounts, enabling the application to read the profiles of signed-in users, such as their email address and contact information.  This is a delegation permission, and gives the user the ability to consent before proceeding.  Please refer to [Integrate Apps with Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) for more depth on configuring an Azure AD tenant to enable an application to access your tenant.
  
8. While you are on this page, also note/copy the "Client ID" GUID and "Redirect URI", as you will use these in Step #3 below.  You will also need your Azure Subscription ID and AAD tenant domain name, both of which you can copy from the "Settings" page in the management portal.

### Step 2:  If you haven't already, Clone or download the billing-dotnet-ratecard-api repository

From your shell (ie: Git Bash, etc.) or command line, run the following command :

    git clone https://github.com/Azure-Samples/billing-dotnet-ratecard-api.git

### Step 3:  Edit and Build the sample in Visual Studio
After you've configured your tenant and downloaded the sample app, you will need to go into the local sub directory in which the Visual Studio solution is stored (typically in `<your-git-root-directory>\billing-dotnet-ratecard-api\ConsoleApp-Billing-RateCard)`, and open the `ConsoleApp-Billing-RateCard.sln` Visual Studio solution.  Upon opening, navigate to the app.config file and update the following key/value pairs, using your subscription and AAD specific configuration information from earlier.  NOTE: It's very important that all values match your configuration!

	<add key="ADALRedirectURL" value="https://localhost/"/>
	<add key="TenantDomain" value="ENTER.AAD.TENANT.DNS.NAME"/>                           
	<add key="SubscriptionID" value="00000000-0000-0000-0000-000000000000"/>
	<add key="ClientId" value="00000000-0000-0000-0000-000000000000"/>

When you build the solution, it will also restore the missing Nuget packages which contain libraries upon which the sample depends.  Program.cs contains the RateCard query string, so if you would like to query for different data, you can also change the OfferDurableId, Currency, Locale, and/or RegionInfo parameter values.

### Step 4:  Run the application against your AAD tenant

When finished with Step 3, you should be able to successfully run the application, which will prompt you for your Azure AD credentials.  Upon successful authentication, the API will be called and the results will be displayed in the console window. 

The application first fetches an access token using credentials of the signed-in user, then gets the RateCard response and deserializes it and prints it to the console. 

### Troubleshooting
Below is a list of common errors you may receive, if your application or user account is not configured correctly.

**401 Unauthorized** HTTP error  
This is typically caused by either an invalid SubscriptionID value in the App.config file, or a valid Subscription ID that is not being trusted for authentication by the specified Azure AD tenant (TenantDomain value). 

Make sure the value you enter for SubscriptionID is both a valid subscription and it is associated with the Azure AD tenant you specified for the TenantDomain value. You can verify this in the [Azure classic portal](https://manage.windowsazure.com) by logging in with a service admin/co-admin account for the given subscription, and navigate to the "Settings/Subscriptions" page: 

`https://manage.windowsazure.com/<MyTenantName>.onmicrosoft.com#Workspaces/AdminTasks/SubscriptionMapping`

You should see the GUID for the subscription under the "Subscription ID" column, and the DNS name for your Azure AD tenant under the "Directory" column (shown in a `Friendly-Name (DNS-Name)` format).

For more details on the trust relationship between the Azure subscription and Azure AD, please see [How Azure subscriptions are associated with Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory). 

**403 Forbidden** HTTP error  
The Azure Billing REST APIs are implemented as a Resource Provider as part of the Azure Resource Manager, and therefore share its dependencies.  Access control for Azure Resource Manager uses the built-in "Owner", "Contributor", and "Reader" roles, via the Role Based Access Control (RBAC) feature in the [Azure Portal](https://portal.azure.com/) to secure resources. For details on configuring, see [Role-based access control in the Microsoft Azure portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/). 

There are typically 2 things to look for if you are receiving this error :

- Signed-in user does not belong to specified Azure AD tenant: In order for the application to access your subscription (and billing information), it must obtain an access token from Azure AD. In order for it to obtain an access token, it will prompt you to authenticate using credentials from the Azure AD tenant being trusted by your subscription. 

    As such, make sure you authenticate using credentials from the Azure AD tenant being trusted by your subscription. If you're not sure which Azure AD tenant is being trusted for authentications by your subscriptions, see the "401 Unauthorized" error above for instructions.

- Signed-in user does not belong to correct role: in addition to the above requirement, make the signed-in user is a member of either the ‘Reader’, ‘Owner’ or ‘Contributor’ roles for the specified subscription.  By default, all service administrators are members of the Owner role. If you attempt sign-in with a valid user from the Azure AD tenant being trusted by the subscription, but don't configure role membership correctly, you may experience a "401 Unauthorized" error.

**AADSTS65005** Azure AD exception  
This error is typically caused by incorrect or missing permission requests in the application's configuration. See steps #1.6 and #1.7 above for more information.

## Need Help?

Be sure to check out the Azure forums on [StackOverflow](http://stackoverflow.com/search?q=azure+billing) and [MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=windowsazurepurchasing) if you are having trouble. The Azure Billing product team actively monitors the forums and will be more than happy to assist you.

If you would like to provide feedback on the Billing APIs or ideas on how we can improve them, please visit the [Azure Feedback Forums](http://feedback.azure.com/forums/170030-billing).

## Contribute Code or Provide Feedback

If you would like to become an active contributor to this project please follow the instructions provided in [Microsoft Azure Projects Contribution Guidelines](http://azure.github.com/guidelines.html).

If you encounter any bugs with the library please file an issue in the [Issues](https://github.com/Azure-Samples/billing-dotnet-ratecard-api/issues) section of the repository.

## Learn More
* [Azure Billing API Overview](https://docs.microsoft.com/en-us/azure/billing/billing-usage-rate-card-overview)
