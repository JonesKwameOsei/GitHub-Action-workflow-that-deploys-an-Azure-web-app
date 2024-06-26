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

















