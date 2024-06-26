# GitHub-Action-workflow-that-deploys-an-Azure-web-app
## Implementing GitHub Actions for CI/CD to deploys an Azure web app.
## Overview
In this project, we will be creating a GitHub Action workflow to deploy an Azure web app.

### Importing eShopOnWeb to My GitHub Repository

In this exercise, I will import the existing [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb) repository code to my own GitHub private repository. The repository is organized as follows:

- `.ado` folder contains Azure DevOps YAML pipelines.
- `.devcontainer` folder contains setup to develop using containers (either locally in VS Code or GitHub Codespaces).
- `infra` folder contains Bicep & ARM infrastructure as code templates used in some lab scenarios.
- `.github` folder contains YAML GitHub workflow definitions.
- `src` folder contains the .NET 8 website used in the lab scenarios.

#### Task 1: Create a public repository in GitHub and import eShopOnWeb
The task is to establish a new public GitHub repository and then proceed to import the current [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb) repository.
1. Open a web browser and go to the GitHub website. Log in with your account and then select New to make a new repository.
2. On the `Create a new repository` page, click on the "Import a repository" link (below the page title).

>**Note:** You can also open the import website directly at https://github.com/new/import <p>
![createrepo](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/935a85b6-7cae-4db8-ab7b-fccc55ac94a2)<p>

3. On the `Import your project to GitHub` page, fill in the following details:

| Field | Value |
| --- | --- |
| Your old repository's clone URL | https://github.com/MicrosoftLearning/eShopOnWeb |
| Owner | Your account alias |
| Repository Name | eShopOnWeb |
| Privacy | Public | <p>

![createrepo2](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/c5262c24-9533-4e68-90aa-c8681ac7cf10)<p>

Click on "Begin Import" and wait for your repository to be ready.<p>
![createrepo3](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/87b11713-cfe9-41de-babd-5e3301471317)<p>
![prep-repo](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/b280dfc4-fa40-4915-99d7-3743e94f090f)<p>
![prep-repo2](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/73ce339b-19d7-45fc-8dbd-3ba7215cc10d)<p>

On the repository page, go to "Settings", click on "Actions > General" and choose the option "Allow all actions and reusable workflows". Click on "Save".<p>
![action-perm](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/a36b8c0c-4433-4321-8f75-5755482d7f16)<p>

#### Setup your GitHub Repository and Azure access

In this section, I will create an `Azure Service Principal` to authorize **GitHub** accessing my **Azure subscription** from GitHub Actions. I will also setup the `GitHub workflow` that will **build, test and deploy** my website to Azure.

##### Task 1: Create an Azure Service Principal and save it as GitHub secret

In this task, I will create the Azure Service Principal used by GitHub to deploy the desired resources. As an alternative, we could also use `OpenID connect` in Azure, as a **secretless authentication mechanism**. Following these steps will help us achieve our goal:<p>

1. In a browser window, open the Azure Portal (https://portal.azure.com/).
2. In the portal, look for Resource Groups and click on it.<p>
![azure-portal](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/d76f1a9e-1403-4859-8760-63cc0994f535)<p>

3. Click on + Create to create a new Resource Group for the exercise.<p>
![azure-portal2](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/e8950e09-9f79-416d-a057-665bec718632)<p>

4. On the Create a resource group tab, give the following name to your Resource Group: `rg-eshoponweb-NAME` (replace `NAME` for some unique alias). Click on "Review+Create > Create".<p>
![azure-portal3](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/74db9689-5d72-4aeb-bc26-fd9f1bc2002a)<p>
![azure-portal4](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/d777004d-88a9-4736-ab8e-3527377c4ea2)<p>
![rsg](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/234ed316-e0d9-477f-af60-33ffe13ce24e)<p>

5. In the Azure Portal, open the Cloud Shell (next to the search bar).<p>
![cloudshell](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/a0d290fb-0e08-474c-a319-82a6a7d9d27f)<p>

   >**Note:** if this is the first time you open the Cloud Shell, you need to configure the persistent storage

6. Make sure the terminal is running in Bash mode and execute the following command, replacing `SUBSCRIPTION-ID` and `RESOURCE-GROUP` with your own identifiers (both can be found on the Overview page of the Resource Group):<p>
![cloudshell2](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/865259c8-4bde-4d5b-93e5-f986425cdfac)<p>

   ```
   az ad sp create-for-rbac --name GH-Action-eshoponweb --role contributor --scopes /subscriptions/SUBSCRIPTION-ID/resourceGroups/RESOURCE-GROUP --sdk-auth
   ```

   >**Note:** Make sure this is typed or pasted as a single line! **Note:** this command will create a Service Principal with Contributor access to the Resource Group created before. This way we make sure GitHub Actions will only have the permissions needed to interact only with this Resource Group (not the rest of the subscription)

7. The command will output a JSON object, you will later use it as a GitHub secret for the workflow. Copy the JSON. The JSON contains the identifiers used to authenticate against Azure in the name of a Microsoft Entra identity (service principal).

   ```json
   {
     "clientId": "<GUID>",
     "clientSecret": "<GUID>",
     "subscriptionId": "<GUID>",
     "tenantId": "<GUID>",
     (...)
   }
   ```

8. You also need to run the following command to register the resource provider for the Azure App Service you will deploy later:

   ```
   az provider register --namespace Microsoft.Web
   ```

9. In a browser window, go back to your eShopOnWeb GitHub repository.
10. On the repository page, go to "Settings", click on "Secrets and variables > Actions". Click on "New repository secret"<p>
![actions](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/87f5e9fc-9966-4ebc-bb4b-484c907fb54d)<p>
![actions2](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/bf418d62-57e5-4be5-82bf-5a3346b1a2d7)

    - Name: `AZURE_CREDENTIALS`
    - Secret: paste the previously copied JSON object (GitHub is able to keep multiple secrets under same name, used by `azure/login` action)<p>
![actions3](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/5f581e16-bf61-4b10-9bc5-218e1fcf9c37)<p>

11. Click on "Add secret". Now GitHub Actions will be able to reference the service principal, using the repository secret.<p>
![actions-secrets](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/a9e96ba5-72e9-4828-aeca-57b1068c2106)<p>

##### Task 2: Modify and execute the GitHub workflow

I will modify the given GitHub workflow and execute it to deploy the solution in my own subscription.

1. In a browser window, return to the eShopOnWeb GitHub repository.
2. On the repository page, go to "Code" and open the following file: `eShopOnWeb/.github/workflows/eshoponweb-cicd.yml`. This workflow defines the CI/CD process for the given .NET 8 website code.
3. Uncomment the `on` section (delete `#`). The workflow triggers with every push to the main branch and also offers manual triggering (`workflow_dispatch`).
4. In the `env` section, make the following changes:
   - Replace `NAME` in `RESOURCE-GROUP` variable. It should be the same resource group created in previous steps.
   - (Optional) You can choose your closest azure region for `LOCATION`. For example, "eastus", "eastasia", "westus", etc.
   - Replace `YOUR-SUBS-ID` in `SUBSCRIPTION-ID`.
   - Replace `NAME` in `WEBAPP-NAME` with some unique alias. It will be used to create a globally unique website using Azure App Service.
5. Read the workflow carefully, comments are provided to help understand.
6. Click on "Start Commit" and "Commit Changes" leaving defaults (changing the main branch). The workflow will get automatically executed.

## Task 3: Review GitHub Workflow execution

In this task, you will review the GitHub workflow execution:

1. In a browser window, go back to your eShopOnWeb GitHub repository.
2. On the repository page, go to "Actions", you will see the workflow setup before executing. Click on it.

# Task 3: Review GitHub Workflow Execution

In this task, our aim is to review the GitHub workflow execution:

1. In a browser window, go back to the eShopOnWeb GitHub repository.
2. On the repository page, go to "Actions", you will see the workflow setup before executing. Click on it.<p>
![workflows](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/8b479ce3-0c4d-462f-8788-30ec026d7d0b)<p>
This will take us to the Actions tab of your GitHub repository, where we can view the status and details of the workflow run that was triggered when I committed the changes in the previous task.

Here,  we can see the individual jobs and steps that were executed as part of the workflow, as well as the overall status of the run (whether it was successful, failed, or is still in progress).<p>
![workflows2](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/c76d02b2-8a5e-4430-ba87-47d7f752071c)<p>

You can click on the specific workflow run to view more details, such as the logs for each step, any artifacts that were generated, and information about the environment in which the workflow was executed.
![workflows3](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/58bce5e1-a025-4ae8-b5f7-051c5504a3ee)<p>
![workflows4](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/e6b60a8b-0825-4cb0-ac16-d394c62bc3fd)<p>
3. Let's wait for the workflow to complete. In the Summary, we can view the two workflow jobs, their status, and the artifacts saved from the execution.  Each job can be examined through the logs.<p>
![workflows-done](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/8c227498-f9ba-4f8e-8579-64396cf456e2)<p>
![workflows-done2](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/3e29ee1d-94eb-4e32-b175-2c22b99c2fba)<p>
![image](https://github.com/JonesKwameOsei/GitHub-Action-workflow-that-deploys-an-Azure-web-app/assets/81886509/69660402-12aa-4a05-8c1e-b1bccc1d6a62)<p>

Reviewing the workflow execution is an important step to ensure that the deployment to Azure was successful and to troubleshoot any issues that may have occurred during the process.















