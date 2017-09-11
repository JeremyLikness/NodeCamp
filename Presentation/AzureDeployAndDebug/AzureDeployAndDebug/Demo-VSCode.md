﻿<a name="title" />
# Provisioning and Deploying on Azure #

---
<a name="Overview" />
## Overview ##

This demo introduces how to deploy a Node.js application to Azure from GitHub. Additionally, you will see how to use Azure CLI to manage your resources in Azure.

<a id="goals" />
### Goals ###
In this demo, you will see how to:

1. Deploy a Node.js app to Azure

1. Manage your Azure resources using the Azure CLI

<a name="technologies" />
### Key Technologies ###

- [Node.js][1]
- [Microsoft Azure][2]
- [Azure DocumentDB][3]
- [Visual Studio Code][4]
- [Azure CLI][5]

[1]: https://nodejs.org/
[2]: http://azure.microsoft.com/
[3]: http://azure.microsoft.com/en-us/services/documentdb/
[4]: https://code.visualstudio.com/
[5]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/

<a name="Setup" />
### Setup and Configuration ###
Follow these steps to set up your environment for the demo.

1. Get an [Azure subscription](https://azure.microsoft.com/en-us/pricing/free-trial/)

1. Get a [GitHub Account](https://help.github.com/articles/signing-up-for-a-new-github-account/)

1. Download [Visual Studio Code](https://code.visualstudio.com/Download) for your platform and follow the [installation and setting up instructions](https://code.visualstudio.com/Docs/editor/setup).

1. Install [Node.js](https://nodejs.org/en/download/).

1. Create a new repository in your GitHub account and import the code from the **source\Begin** folder. Details [here](https://help.github.com/articles/create-a-repo/).

1. Open a **command prompt/terminal** according to your platform in the **source/Begin/Chatroom** folder.

1. Run **npm install** to install all the missing dependencies.

	![Installing Missing npm Packages](images/VSCode/installing-missing-npm-packages.png?raw=true "Installing Missing npm Packages")

	_Installing Missing npm Packages_

1. Install [Azure CLI](https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/#install-and-use-nodejs-and-npm)
	
<a name="Demo" />
## Demo ##
This demo is composed of the following segments:

1. [Provisioning Azure resources using Azure CLI](#segment1)
1. [Deploying to Azure with GitHub](#segment2)

<a name="segment1" />
### Provisioning Azure resources using Azure CLI ###
> **Note:** In the following section you will see how to use the Azure CLI tool to create the Azure resources for the web app. If you want you can use the [Azure Portal](https://portal.azure.com/) to perform the same operations. Some of the screenshots may reference `Azure` as the command line, but the correct reference is `az`.

1. In the command prompt/terminal run **az login** to authenticate interactively.

	![Azure Interactive Login](images/VSCode/cli-azure-interactive-login.png?raw=true "Azure Interactive Login")

1. Copy the code offered to you, above, and open a browser to http://aka.ms/devicelogin. Enter the code, and then you are prompted to enter the username and password for the identity you want to use. When that process completes, the command shell completes the log in process. It might look something like:

	![Interactive Login completed](images/VSCode/cli-azure-interactive-login-complete.png?raw=true "Interactive Login completed")

	> **Note:** Interactive authentication is used in this case because it works with either school/work or Microsoft accounts. If you have a school/work account you can also use the non-interactive authentication method, as it's explained [here](https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-connect/#use-non-interactive-log-in-with-a-work-or-school-account)

1. Run **az group create -n "chatroomRG" -l "East US"** to create a new Resource Group named _chatroomRG_. 

	![Creating a new Azure Resource Group](images/VSCode/creating-azure-resource-group.png?raw=true "Creating a new Azure Resource Group")
	
	_Creating a new Azure Resource Group_

1. If you haven't already, set up your deployment user: **az webapp deployment user set --user-name {user} --password {password}**

1. Create an app service plan **az appservice plan create --name chatroomServicePlan --resource-group chatroomRG --sku FREE**

    ![Creating an app service plan](images/VSCode/cli-serviceplan-create.png)

    _Creating the Service Plan_

1. Create the web app **az webapp create --name nodeChatApp123 --resource-group chatroomRG --plan chatroomServicePlan --deployment-local-git**

   ![Creating the web app](images/VSCode/cli-webapp-create.png)

    _Creating the Web App_

1. Get the remote deployment endpoint **az webapp deployment source config-local-git --name nodeChatApp123 --resource-group chatroomRG --query url --output tsv**

    ![Get the Deployment Endpoint](images/VSCode/cli-get-endpoint.png)

    _Get the Deployment Endpoint_

1. Setup the deployment and push

   ```bash
   git init
   git add -A
   git commit -am "Initial Commit"
   git remote add azure https://{deploymentuser}@{appname}.scm.azurewebsites.net/{appname}.git
   git push azure master
   ```

1. Navigate to the website!

1. Go to the [Azure Portal](https://portal.azure.com/) and check that the Resource Group was successfully provisioned.

	![Checking Azure Resources](images/VSCommunity/checking-azure-resources.png?raw=true "Checking Azure Resources")

	_Checking Azure Resources_

<a name="segment2" />
### Deploying to Azure with GitHub ###

1. Go back to the [Azure Portal](https://portal.azure.com/). In the Settings blade of your Web App, locate the **Publishing** section and click **Continuous deployment**.

	![Setting up continuous deployment](images/VSCode/setting-up-continuos-deployment.png?raw=true "Setting up continuous deployment")

	_Setting up continuous deployment_

1. In the **Continuous Deployment** blade, select **Choose Source** and then select **GitHub**.

	> **Note:** The first time you will need to authorize Azure to access your GitHub information.

	![Selecting GitHub as source](images/VSCode/selecting-github-as-source.png?raw=true "Selecting GitHub as source")

	_Selecting GitHub as source_

1. Select the repository you created in the setup section from the list, leave **master** as the branch to deploy and click **OK**.

	![Selecting the repository](images/VSCode/selecting-the-repository.png?raw=true "Selecting the repository")

	_Selecting the repository_

1. Now, the **Deployments** blade should open, else navigate to the **Continuous deployment** option again in the **Settings** blade. Show that the initial commit is the current active deployment.

	![Showing the Deployments blade](images/VSCode/showing-the-deployments-blade.png?raw=true "Showing the Deployments blade")

	_Showing the Deployments blade_

	> **Speaking point:** In order to make the continuous integration work, a _deploy.cmd_ file must be provided. The Begin solution already includes a deployment script file, but in case you need to create a new one it can be achieved with the Azure CLI tool by running **azure config mode asm** to switch to Azure Service Manager and then **azure site deploymentscript --node** to generate a new deploy.cmd file
	
1. In the command prompt/terminal run **code .** to open the current directory (your local Git repository) with **Visual Studio Code**.

1. In the **Explore** view, open the **index.jade** file located in the **views** folder. 

1. Update the main title.

	![Updating the index view](images/VSCode/updating-the-index-view.png?raw=true "Updating the index view")

	_Updating the index view_

1. Push the changes to GitHub using Visual Studio Code. To do this bring up the **Git** view by clicking on the Git icon in the View Bar on the side of Visual Studio Code. Enter a commit message (e.g. "Updated index view") and click on the **Commit all** button.

	![Committing all changes](images/VSCode/committing-all-changes.png?raw=true "Committing all changes")
	
	_Committing all changes_

1. Now push the changes by clicking on the ellipsis button and then in the **Push** command.

	![Pushing changes to GitHub](images/VSCode/pushing-changes-to-github.png?raw=true "Pushing changes to GitHub")
	
	_Pushing changes to GitHub_

1. Go back to the **Deployments** blade for your Web App and show the new deployment entry.

	![Showing the new deployment entry](images/VSCode/showing-the-new-deployment-entry.png?raw=true "Showing the new deployment entry")

	_Showing the new deployment entry_

1. Navigate to the site to show that its was updated.

	![Showing the updated site](images/VSCode/showing-the-updated-site.png?raw=true "Showing the updated site")

	_Showing the updated site_

---

<a name="summary" />
## Summary ##

By completing this demo, you have learned how to deploy a Node.js application to Azure from GitHub. Additionally, you have learned how to provision Azure resources using Azure CLI Resource Manager.

---
