# Lab 1: Manage infrastructure as code using Azure and DSC

# Deployments using Azure Bicep templates

## Lab requirements

- This lab requires **Microsoft Edge** or an [Azure DevOps supported browser.](https://docs.microsoft.com/azure/devops/server/compatibility)

- **Set up an Azure DevOps organization:** If you don't already have an Azure DevOps organization that you can use for this lab, create one by following the instructions available at [Create an organization or project collection](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization).

- Identify an existing Azure subscription or create a new one.

- Verify that you have a Microsoft account or a Microsoft Entra account with the Owner role in the Azure subscription and the Global Administrator role in the Microsoft Entra tenant associated with the Azure subscription. For details, refer to [List Azure role assignments using the Azure portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) and [View and assign administrator roles in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/roles/manage-roles-portal).

## Lab overview

In this lab, you'll create an Azure Bicep template and modularize it using the Azure Bicep Modules concept. You'll then modify the main deployment template to use the module and finally deploy all the resources to Azure.

## Objectives

In this lab you will perform the following exercises:

- Exercise 0: Configure the lab prerequisites
- Exercise 1: Understand an Azure Bicep template and simplify it using a reusable module
- Exercise 2: Deploying the templates to Azure using YAML pipelines

## Estimated timing: 45 minutes

# Exercise 0: Configure the lab prerequisites

In this exercise, you will set up the prerequisites for the lab, which consist of a new Azure DevOps project with a repository based on the [eShopOnWeb](https://dev.azure.com/unhueteb/_git/eshopweb-az400).

## Task 1: Set up an Azure DevOps organization. 

1. On your lab VM open **Edge Browser** on desktop and navigate to https://go.microsoft.com/fwlink/?LinkId=307137. 

2. In the pop-up for *Help us protect your account*, select **Skip for now (14 days until this is required)**.

3. On the next page accept defaults and click on continue.

    ![Azure DevOps](images/400-3.png)

4. On the **Almost Done...** page fill the captcha and click on continue. 

    ![Azure DevOps](images/m1-2.png)

5. On the Azure DevOps page click on **Azure DevOps** located at top left corner and then click on **Organization Setting** at the left down corner

    ![Azure DevOps](images/az-400-lab3-(1).png)
    
6. In the **Organization Setting** window on the left menu click on **Billing (1)** and select **Setup Billing (2)** then click on **save (3)**.

    ![Azure DevOps](images/az-400-lab3-1.png)

   ![Azure DevOps](images/az-400-lab3-2.png)    

8. On the **MS Hosted CI/CD** section under **Paid parallel jobs** enter value **1** and at the end of the page click on **Save**.

    ![Azure DevOps](images/az-400-lab3-3.png)   

9. In the **Organization Setting** window on the left menu click on **Policies** and enable **Third-party application access via OAuth**.

    ![Azure DevOps](images/AZ41.png)    

## Task 2: Create and configure the team project

In this task, you will create an **eShopOnWeb** Azure DevOps project to be used by several labs.

1. On your lab computer, in a browser window open your Azure DevOps organization.

    ![Create Project](images/az4.2.png)

1. Give your project the name **eShopOnWeb** and leave the other fields with defaults. Click on **+ Create Project**.

    ![Create Project](images/6-10.png)

## Task 3: Import eShopOnWeb Git Repository

In this task you will import the eShopOnWeb Git repository that will be used by several labs.

1. On your lab computer, in a browser window open your Azure DevOps organization and the previously created **eShopOnWeb** project.
  
1. Click on **Repos (1) >Files (2)** from the left pane , under **Import a Repository** select **Import (3)**. On the **Import a Git Repository** window, paste the following URL <https://github.com/MicrosoftLearning/eShopOnWeb.git> **(4)** and click **Import (5)**:

     ![Import Repository](images/az-400-4.png)

## Task 4: Set main branch as default branch
  
1. Go to **Repos>Branches (1)**.

1. Hover on the **main** branch then click the ellipsis on the right of the column **(2)**.

1. Click on **Set as default branch (3)**.
   
   ![Import Repository](images/az-400-5.png)

   >**Note:** If there's only one branch, it will automatically be set as the default branch, and the option will be greyed out. In this case, you can proceed with the next steps.

# Exercise 1: Understand an Azure Bicep template and simplify it using a reusable module

In this lab, you will review an Azure Bicep template and simplify it using a reusable module.

## Task 1: Create Azure Bicep template

In this task, you will use Visual Studio Code to create an Azure Bicep template

1. In the browser tab you have your Azure DevOps project open, navigate to **Repos** and **Files** from the left pane. Open the `infra` folder and find the `simple-windows-vm.bicep` file.

   ![Simple-windows-vm.bicep file](./images/6-8.png)

1. Review the template to get a better understanding of its structure. There are some parameters with types, default values and validation, some variables, and quite a few resources with these types:

   - Microsoft.Storage/storageAccounts
   - Microsoft.Network/publicIPAddresses
   - Microsoft.Network/virtualNetworks
   - Microsoft.Network/networkInterfaces
   - Microsoft.Compute/virtualMachines

1. Pay attention to how simple the resource definitions are and the ability to implicitly reference symbolic names instead of explicit `dependsOn` throughout the template.

## Task 2: Create a bicep module for storage resources

In this task, you will create a storage template module **storage.bicep** which will create a storage account only and will be imported by the main template. The storage template module needs to pass a value back to the main template, **main.bicep**, and this value will be defined in the outputs element of the storage template module.

1. First we need to remove the storage resource from our main template. From the top right corner of your browser window click the **Edit** button:

   ![Edit button](./images/6-9.png)

1. Now delete the storage resource located in line number 93:

   ```bicep
   resource storageAccount 'Microsoft.Storage/storageAccounts@2022-05-01' = {
     name: storageAccountName
     location: location
     sku: {
       name: 'Standard_LRS'
     }
     kind: 'Storage'
   }
   ```
   
   ![Simple-windows-vm.bicep file](./images/az-400-5a28.png)
   
1. Click on Commit twice to commit the changes, however, we're not done with it yet.

1. Next, hover your mouse over the **infra** folder and click the ellipsis icon **(1)**, then select **New>File (2)**. 

   
   ![Simple-windows-vm.bicep file](./images/az-400-5a26.png)
   
1. Enter **storage.bicep (1)** for the name and click **Create (2)**.
   
   ![Simple-windows-vm.bicep file](./images/az-400-5a29.png)
   
1. Now copy the following code snippet into the file and select **commit (1)** twice to commit your changes.:

   ```bicep
   @description('Location for all resources.')
   param location string = resourceGroup().location

   @description('Name for the storage account.')
   param storageAccountName string

   resource storageAccount 'Microsoft.Storage/storageAccounts@2022-05-01' = {
     name: storageAccountName
     location: location
     sku: {
       name: 'Standard_LRS'
     }
     kind: 'Storage'
   }

   output storageURI string = storageAccount.properties.primaryEndpoints.blob
   ```
   
   ![Simple-windows-vm.bicep file](./images/az-400-5a30.png)
   
## Task 3: Modify the main template to use the template module

In this task, you will modify the main template to reference the template module you created in the previous task.

1. Navigate back to the `simple-windows-vm.bicep` file and click on the **Edit** button once again.

1. Next, add the following code after the variables in line number 93:

   ```bicep
   module storageModule './storage.bicep' = {
     name: 'linkedTemplate'
     params: {
       location: location
       storageAccountName: storageAccountName
     }
   }
   ```
   
   ![Simple-windows-vm.bicep file](./images/az-400-5a23.png)
   
1. We also need to modify the reference to the storage account blob URI in our virtual machine resource to use the output of the module instead. Find the virtual machine resource and replace the diagnosticsProfile section with the following:

   ```bicep
   diagnosticsProfile: {
     bootDiagnostics: {
       enabled: true
       storageUri: storageModule.outputs.storageURI
     }
   }
   ```
   
   ![Simple-windows-vm.bicep file](./images/az-400-5a31.png)

1. Click on Commit twice.

# Exercise 2: Deploying the templates to Azure using YAML pipelines

In this lab, you will create a service connection and use it in an Azure DevOps YAML pipeline to deploy your template to your Azure environment.

## Task 1: Create a Service Connection for deployment

In this task, you will create a Service Principal by using the Azure CLI. You will need a Service Principal to deploy  Azure resources from Azure Pipelines. Since we are going to retrieve secrets in a pipeline, we will need to grant permission to the service when we create the Azure Key Vault.

A Service Principal is automatically created by Azure Pipelines, when you connect to an Azure subscription from inside a pipeline definition or when you create a new Service Connection from the project settings page (automatic option). You can also manually create the Service Principal from the portal or using Azure CLI and re-use it across projects.

1. From the lab computer, start a web browser, navigate to the [**Azure Portal**](https://portal.azure.com), if prompted to sign in sign in using the following credentials:

    - Username:  <inject key="AzureAdUserEmail"></inject>
    - Password:  <inject key="AzureAdUserPassword"></inject>
      
1. In the Azure portal, click on the **Cloud Shell** icon, located directly to the right of the search textbox at the top of the page.

     ![](images/az-400-9a2.png)
   
1. If prompted to select either **Bash** or **PowerShell**, select **Bash**.
   
1. On the **Getting started**, select **Mount storage account** and select your subscription under storage account subscription. Click on **Apply**.
   
     ![](images/lab15-storage-acc-1.png)
   
1. On the **Mount storage account** tab, select **I want to create a storage account**. Click on **Next**.
   
     ![](images/lab15-storage-acc-2.png)

1. Use the existing **Subscription (1)** and **Resource group** as **AZ400-JumpVM (2)** and **Region** as **westus(3)** and specify new names for **Storage account** as **az400storage<inject key="DeploymentID" enableCopy="false" /> (4)** and **File share** as **az400fileshare<inject key="DeploymentID" enableCopy="false" /> (5)**, as shown in the dialog below. Then select **Create**.
   
     ![](images/az-400-storage.png)

1. From the **Bash** prompt, in the **Cloud Shell** pane, run the following commands to retrieve the values of the Azure subscription ID and subscription name attributes:

    ```bash
    az account show --query id --output tsv
    az account show --query name --output tsv
    ```

    > **Note**: Copy both values to a text file. You will need them later in this lab.

1. From the **Bash** prompt, in the **Cloud Shell** pane, run the following command to create a Service Principal

   - Replace the **myServicePrincipalName** with **azureconnection<inject key="DeploymentID" enableCopy="false" />** and **mySubscriptionID** with your Azure subscription Id which you copied in the previous step.

    ```bash
    az ad sp create-for-rbac --name myServicePrincipalName \
                         --role contributor \
                         --scopes /subscriptions/mySubscriptionID
    ```

    ![New Service Connection](images/6-5.png)

1. Run the following command to create a resource group.

    Replace **[Location]** with **<inject key="Region" enableCopy="false" />** in the below command.

     ```bash
       az group create --name az400m06l15-RG --location [Location]
     ```

1. Next, from the lab computer, start a web browser, navigate to the Azure DevOps **eShopOnWeb** project. Click on **Project Settings**. 

   ![New Service Connection](images/az-4.3.png)

1. From the left pane click **Service Connections** under Pipelines.

   ![New Service Connection](images/AZ44.png)

1. In the window that appears click on **Create Service Connection**.

   ![New Service Connection](images/6-6.png)

1. On the **New service connection** blade, select **Azure Resource Manager** and **Next** (may need to scroll down).

   ![New Service Connection](images/402.png)

1. Fill in the below fields and leave the others as default:
    - Resource Group: **az400m06l15-RG** (1)
    - In **Service connection name** type **azure subs**(2). This name will be referenced in YAML pipelines when needing an Azure DevOps Service Connection to communicate with your Azure subscription.
    - Click on **Save**(3).

      ![Policy Settings](images/AZ42.png)
   
## Task 2: Deploy resources to Azure by YAML pipelines

In this task, you will create a template-based Azure DevOps YAML pipeline.

1. Go to **Pipelines (1)>Pipelines (2)**. Click on **Create Pipeline (3)** button.

    ![](images/AZ-400-create.png)  

2. Select **Azure Repos Git (YAML)**

    ![](images/AZ-400-code.png)

3. Select the **eShopOnWeb** repository.

    ![](images/az400-m3-L4-09.png)

4. Select **Existing Azure Pipelines YAML File**

    ![](images/az400-m3-L4-10.png)

1. In the **Selecting an existing YAML File** blade, specify the following parameters and  click **Continue (3)** to save these settings.
   - Branch: **main (1)**
   - Path: **/.ado/eshoponweb-cd-windows-cm.yml (2)**

   ![Save and running the YAML pipeline after making changes](./images/6-3.png)

1. In the variables section, replace the  following:

   - Name of the resource group with **az400m06l15-RG** (1)
   - Set the desired **location** to **<inject key="Region" enableCopy="false"/>** (2)
   - Value of the service connection with **azure subs** (3)

    ![](images/AZ43.png)

1. Click the **Save and run (1)** button from the top right corner and in the commit dialog, click **Save and run** again.

1. Wait for the pipeline to kick off.

1. **Ignore** any Warnings showing up during the Build Stage. Wait until it completes the Build Stage successfully. (You can select the actual Build stage to see more details from the logs.)

1. Once the Deploy Stage wants to start, you are prompted with **Permissions Needed**. Click on **View**.

   ![Successful resource deployment to Azure using YAML pipelines](./images/az-400-5a25.png)

1. From the **Waiting for Review** pane, click **Permit**.

1. Validate the message in the **Permit popup** window, and confirm by clicking **Permit**.

1. This sets off the Deploy Stage. Wait for this to complete successfully. The pipeline can take around 10 minutes to complete.

1. Wait for the deployment to finish and review the results.

   ![Successful resource deployment to Azure using YAML pipelines](./images/6-1.png)

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   - If you receive a success message, you can proceed to the next task.
   - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.
 
   <validation step="9cd20f12-9e2f-4fb2-b764-6790b73ade56" />

## Task 3: Remove the Azure Devops billing

In this task, you will remove pipeline billing to eliminate unnecessary charges.

1. On the lab computer, switch to the browser window displaying Azure DevOps organization homepage by clicking on **Azure Devops** from the top left corner.

   ![Branch Policies](images/407.png)

1. Select **Organization Settings** at bottom left corner.

   ![Branch Policies](images/408.png)

1. Under **Organization Settings** select **Billing** from the left pane and click on **Change billing** button to open Change billing pane.

1. In the **Change billing** pane, select **Remove billing** setting and click on **Save**.


## Review

In this lab, you learned how to create an Azure Bicep template, modularize it by using a template module, modify the main deployment template to use the module and updated dependencies, and finally deploy the templates to Azure using YAML pipelines.

## You have successfully completed the lab.
