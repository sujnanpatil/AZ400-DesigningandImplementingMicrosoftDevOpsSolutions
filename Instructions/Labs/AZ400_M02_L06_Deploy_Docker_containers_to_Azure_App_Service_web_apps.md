# Lab 04: Deploying Docker containers to Azure App Service web apps

## Lab overview

In this lab, you will learn how to use an Azure DevOps CI/CD pipeline to build a custom Docker image, push it to Azure Container Registry, and deploy it as a container to Azure App Service. 

## Objectives

After you complete this lab, you will be able to:

- Build a custom Docker image by using a Microsoft hosted Linux agent
- Push an image to Azure Container Registry
- Deploy a Docker image as a container to Azure App Service by using Azure DevOps

## Estimated timing: 30 minutes

## Architecture Diagram

   ![Architecture Diagram](images/lab6-architecture-new.png)

# Exercise 1: Manage the service connection

In this exercise, you will configure the service connection with your Azure Subscription then import and run the CI pipeline.

## Task 1: Manage the service connection

You can create a connection from Azure Pipelines to external and remote services for executing tasks in a job.

In this task, you will create a service principal by using the Azure CLI, which will allow Azure DevOps to:
- Deploy resources on your azure subscription
- Push the docker image to Azure Container Registry
- Add a role assignment to allow Azure App Service pull the docker image from Azure Container Registry

> **Note**: If you do already have a service principal, you can proceed directly to the next task.

You will need a service principal to deploy  Azure resources from Azure Pipelines.

A service principal is automatically created by Azure Pipeline when you connect to an Azure subscription from inside a pipeline definition or when you create a new service connection from the project settings page (automatic option). You can also manually create the service principal from the portal or using Azure CLI and re-use it across projects. 

1.  From the lab computer, start a web browser, navigate to the [**Azure Portal**](https://portal.azure.com), and sign in with the user account that has the Owner role in the Azure subscription you will be using in this lab and has the role of the Global Administrator in the Azure AD tenant associated with this subscription.

1.  In the Azure portal, click on the **Cloud Shell** icon, located directly to the right of the search textbox at the top of the page. 

1.  If prompted to select either **Bash** or **PowerShell**, select **Bash**. 

    >**Note**: If this is the first time you are starting **Cloud Shell** and you are presented with the **You have no storage mounted** message, select the subscription you are using in this lab, and select **Create storage**. 

1.  From the **Bash** prompt, in the **Cloud Shell** pane, run the following commands to retrieve the values of the Azure subscription ID attribute: 

    ```
    subscriptionName=$(az account show --query name --output tsv)
    subscriptionId=$(az account show --query id --output tsv)
    echo $subscriptionName
    echo $subscriptionId
    ```

    > **Note**: Copy both values to a text file. You will need them later in this lab.

1.  From the **Bash** prompt, in the **Cloud Shell** pane, run the following command to create a service principal:

    > **Note**: Replace `$subscriptionId` with the subscription ID you copied in the previous step. Once the command runs, it will generate a JSON output. Save this output to a text file, as you will need it later in this lab.

    ```
    az ad sp create-for-rbac --name sp-az400-azdo --role contributor --scopes /subscriptions/$subscriptionId
    ```

1. Next, from the lab vm, start a web browser, navigate to the [**Azure Devops**](https://go.microsoft.com/fwlink/?LinkId=307137) **eShopOnWeb** project. Click on **Project Settings>Service Connections (under Pipelines)** and **Create Service Connection**.

1. On the **New service connection** blade, select **Azure Resource Manager** and **Next** (may need to scroll down).

1. The choose **Service principal (manual)** and click on **Next**.

1. Fill in the empty fields using the information gathered during previous steps:
    - Subscription Id and Name
    - Service Principal Id **(appId**), Service principal key ( **Password**) and TenantId (**tenant**).
    - In **Service connection name** type **azure-connection**. This name will be referenced in YAML pipelines when needing an Azure DevOps Service Connection to communicate with your Azure subscription.

1. Click on **Verify and Save**.

# Exercise 2: Import and run the CI pipeline

In this exercise, you will import and run the CI pipeline.

## Task 1 : Remove Branch Policies
 
When going through the different course labs in the order they are presented, the branch policy configured during this lab will block exercises in future labs. Therefore, we want you to remove the configured branch policies.
 
1. From the Azure DevOps **eShopOnWeb** Project view, navigate to **Repos** and select **Branches (1)**. Select the **Mine (2)** tab of the **Branches** pane.
1. On the **Mine** tab of the **Branches** pane, hover the mouse pointer over the **main** branch entry to reveal the ellipsis symbol (the ...) **(3)** on the right side.
1. Click the ellipsis and, in the pop-up menu, select **Branch Policies (4)**.
 
    ![Policy Settings](images/az-400-37.png)
 
1. On the **main** tab of the repository settings, disable the option for **Require minimum number of reviewers (1)**.
1. On the **main** tab of the repository settings, disable the option for **Check for linked work items (2)**.
 
    ![Branch Policies](images/az-400-38.png)
 
    >**Note:** Kindly check if the Build validation option is toggled off. 
    
1. You have now disabled/removed the branch policies for the main branch.

## Task 2: Import and run the CI pipeline

1. Go to **Pipelines>Pipelines**.

1. Click on **New pipeline** button.

1. Select **Azure Repos Git (Yaml)**.

1. Select the **eShopOnWeb** repository.

1. Select **Existing Azure Pipelines YAML File**.

1. Select the **/.ado/eshoponweb-ci-docker.yml** file then click on **Continue**.

1. In the YAML pipeline definition, customize:
    - **Azure-service-connection-name** with **azure-connection**.
    - **YOUR-SUBSCRIPTION-ID** with your Azure subscription id.
    - **rg-az400-container-NAME** with the resource group name defined before in the lab.

1. Click on **Save and Run** and wait for the pipeline to execute succesfully.
2. Select "Create new brach" and enter the branch name as **master** and submit to run the pipeline.

    > **Note**: The deployment may take a few minutes to complete if it is ask for permission click on permit.

    The CI definition consists of the following tasks:
    - **Resources**: It downloads the repository filest will be used in the followinf tasks.
    - **AzureResourceManagerTemplateDeployment**: Deploys the Azure Container Registry using bicep template.
    - **PowerShell**: Retrieve the **ACR Login Server** value from the previous task's output and create a new parameter **acrLoginServer**
    - [**Docker**](https://learn.microsoft.com/azure/devops/pipelines/tasks/reference/docker-v0?view=azure-pipelines) **- Build**: Build the docker image and create two tags (Latest and current BuildID)
    - **Docker - Push**: Push the images to Azure Container Registry

1. Your pipeline will take a name based on the project name. Let's **rename** it for identifying the pipeline better. Go to **Pipelines>Pipelines** and click on the recently created pipeline. Click on the ellipsis and **Rename/Remove** option. Name it **eshoponweb-ci-docker** and click on **Save**.

# Exercise 3: Import and run the CD pipeline

In this exercise, you will configure the service connection with your Azure Subscription then import and run the CD pipeline.

## Task 1: Add a new role assignment

In this task, you will add a new role assignment to allow Azure App Service pull the docker image from Azure Container Registry.

1. Navigate to the [**Azure Portal**](https://portal.azure.com).

1. In the Azure portal, click on the **Cloud Shell** icon, located directly to the right of the search textbox at the top of the page. 

1. If prompted to select either **Bash** or **PowerShell**, select **Bash**. 

1. From the **Bash** prompt, in the **Cloud Shell** pane, run the following commands to retrieve the values of the Azure subscription ID attribute: 

    ```sh
    subscriptionId=$(az account show --query id --output tsv)
    echo $subscriptionId
    spId=$(az ad sp list --display-name sp-az400-azdo --query "[].id" --output tsv)
    echo $spId
    roleName=$(az role definition list --name "User Access Administrator" --query "[0].name" --output tsv)
    echo $roleName
    ```

1. After getting the service principal ID and the role name, let's create the role assignment by running this command **(replace <rg-az400-container-NAME> with your resource group name)**

    ```sh
    az role assignment create --assignee $spId --role $roleName --scope /subscriptions/$subscriptionId/resourceGroups/<rg-az400-container-NAME>
    ```

You should now see the JSON output which confirms the success of the command run.

## Task 2: Import and run the CD pipeline

In this task, you will import and run the CI pipeline.

1. In the DevOps portal, navigate to **Repos**, then select **Files** and navigate to **infra/webapp-docker.bicep**

1. Change the SKU to **F1** from **B1** on line 19 and Commit the changes.

1. Go to **Pipelines>Pipelines**.

1. Click on **New pipeline** button.

1. Select **Azure Repos Git (Yaml)**.

1. Select the **eShopOnWeb** repository.

1. Select **Existing Azure Pipelines YAML File**.

1. Select the **/.ado/eshoponweb-cd-webapp-docker.yml** file then click on **Continue**.

1. In the YAML pipeline definition, customize:
    - **azureServiceConnection:** 'azure-connection'
location: 'southcentralus'
    - **YOUR-SUBSCRIPTION-ID** with your Azure subscription id.
    - **rg-az400-container-NAME** with the resource group name defined before in the lab.
    - **location:** 'southcentralus'

1. Click on **Save and Run** and wait for the pipeline to execute succesfully.

    > **Note**: The deployment may take a few minutes to complete and if its asks for permission click on Permit.

    > **Important**: If you receive the error message "TF402455: Pushes to this branch are not permitted; you must use a pull request to update this branch.", you need to uncheck the "Require a minimum number of reviewers" Branch protection rule enabled in the previous labs.

    The CD definition consists of the following tasks:
    - **Resources**: It downloads the repository filest will be used in the following tasks.
    - **AzureResourceManagerTemplateDeployment**: Deploys the Azure App Service using bicep template.
    - **AzureResourceManagerTemplateDeployment**: Add role assignment using Bicep

1. Your pipeline will take a name based on the project name. Let's **rename** it for identifying the pipeline better. Go to **Pipelines>Pipelines** and click on the recently created pipeline. Click on the ellipsis and **Rename/Remove** option. Name it **eshoponweb-cd-webapp-docker** and click on **Save**.

    > **Note 1**: The use of the **/.azure/bicep/webapp-docker.bicep** template creates an app service plan, a web app with system assigned managed identity enabled, and references the docker image pushed previously: **${acr.properties.loginServer}/eshoponweb/web:latest**.

    > **Note 2**: The use of the **/.azure/bicep/webapp-to-acr-roleassignment.bicep** template creates a new role assignment for the web app with AcrPull role to be able to retreive the docker image. This could be done in the first template, but since the role assignment can take some time to propagate, it's a good idea to do both tasks separately.

## Task 3: Test the solution

1. In the Azure Portal, navigate to the recently created Resource Group, you should now see three resources (Ap Service, App Service Plan and Container Registry).

1. Navigate to the App Service, then click **Browse**, this will take you to the website.

Congratulations! In this exercise, you deployed a website using custom docker image.

# Exercise 4: Remove the Azure DevOps billing

In this exercise, you will remove the Azure DevOps billing enabled in this lab to eliminate unexpected charges.

## Task 1: Remove the Azure DevOps billing

In this task, you will remove pipeline billing to eliminate unnecessary charges.

1. On the lab computer, switch to the browser window displaying Azure DevOps organization homepage and select **Organization Settings** at bottom left corner.

1. Under **Organization Settings** select **Billing** and click on **Change billing** button to open Change billing pane.

1. In the **Change billing** pane, select **Remove billing** setting and click on Save.

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   - If you receive a success message, you can proceed to the next task.
   - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.
 
   <validation step="87a02bd6-fc07-4d97-9993-757399fc846f" />

## Review

In this lab, you used an Azure DevOps CI/CD pipeline to build a custom Docker image, pushed it to Azure Container Registry, and deployed it as a container to Azure App Service by using Azure DevOps.

### You have successfully completed the lab.
