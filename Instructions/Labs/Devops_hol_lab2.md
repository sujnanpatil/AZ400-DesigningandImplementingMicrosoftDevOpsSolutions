# Lab 02: Configuring Agent Pools and Understanding Pipeline Styles 

## Lab overview

YAML-based pipelines allow you to fully implement CI/CD as code, in which pipeline definitions reside in the same repository as the code that is part of your Azure DevOps project. YAML-based pipelines support a wide range of features that are part of the classic pipelines, such as pull requests, code reviews, history, branching, and templates. 

Regardless of the choice of the pipeline style, to build your code or deploy your solution by using Azure Pipelines, you need an agent. An agent hosts compute resources that runs one job at a time. Jobs can be run directly on the host machine of the agent or in a container. You have an option to run your jobs using Microsoft-hosted agents, which are managed for you, or implementing a self-hosted agent that you set up and manage on your own. 

In this lab, you will learn how to implement and use self-hosted agents with YAML pipelines.

## Objectives

In this lab you will complete the following exercises:

- Exercise 1: Configure the lab prerequisites
- Exercise 2: Author YAML-based Azure DevOps pipelines
- Exercise 3: Manage Azure DevOps agent pools


## Estimated timing: 45 minutes

## Architecture Diagram

   ![Architecture Diagram](images/lab3-architecture-new.png)   


## Set up an Azure DevOps organization

1. On your lab VM open **Edge Browser** on desktop and navigate to https://go.microsoft.com/fwlink/?LinkId=307137. 

    * Email/Username: <inject key="AzureAdUserEmail"></inject>

    * Password: <inject key="AzureAdUserPassword"></inject>

1. On the **Get started with Azure DevOps**, click on **Continue**.

    ![Azure DevOps](images/dev32.png)

1. If pop-up for *Action Required* is prompted, select **Ask later**. 

1. On the next page accept defaults, fill the captcha **(1)** and click on **Continue (2)**.

    ![Azure DevOps](images/dev33.png)
    
1. On the Azure DevOps page click on **Azure DevOps (1)** located at top left corner and then click on **Organization Setting (2)** at the left down corner.

    ![Azure DevOps](images/dev34.png)
    
1. In the **Organization Setting** window on the left menu click on **Billing (1)** and select **Setup Billing (2)** then click on **Save (3)**.

    ![Azure DevOps](images/dev35.png)

1. On the **MS Hosted CI/CD** section under **Paid parallel jobs** enter value **1** and at the end of the page click on **Save**.

    ![Azure DevOps](images/az-400-lab3-3.png)
   
## Exercise 1: Configure the lab prerequisites 

In this exercise, you will set up the prerequisite for the lab, which consists of the pre-configured Parts Unlimited team project based on an Azure DevOps Demo Generator template.

### Task 1: Create and configure the team project

In this task, you will create an **eShopOnWeb** Azure DevOps project to be used by several labs.

1. Click on **Azure DevOps** from the Billing page to create a new project.

    ![Azure DevOps](images/dev36.png)
 
1. Click on **New Project**. Give your project the name  **eShopOnWeb (1)**, select visibility as **Private(2)**  and leave the other fields with defaults. Click on **Create(3)**.

      ![](images/dev241.png)

### Task 2: Import eShopOnWeb Git Repository 

In this task you will import the eShopOnWeb Git repository that will be used by several labs.

1. On the **eShopOnWeb** project. Click on **Repos (1)>Files (2) , Import a Repository**. Select **Import (3)**. On the **Import a Git Repository** window, paste the following URL https://github.com/CloudLabs-MOC/eShopOnWeb.git **(4)** and click **Import (5)**.

      ![](images/dev38.png)
      
1. The repository is organized the following way:
   
   - **.ado** folder contains Azure DevOps YAML pipelines
         
   - **.devcontainer** folder container setup to develop using containers (either locally in VS Code or GitHub Codespaces)
           
   - **.github** folder contains YAML GitHub workflow definitions.
         
   - **src** folder contains the .NET 6 website used in the lab scenarios.
         
     ![](images/dev39.png)

1. Go to **Repos (1)>Branches (2)**, make sure the **main** branch is set as **default branch (3)**.

      ![](images/dev40.png)

1. If not, Hover on the **main** branch then click the **ellipsis (1)** on the right of the column. click on **Set as default branch (2)**.

      ![](images/dev41.png)


## Exercise 2: Author YAML-based Azure DevOps pipelines

In this exercise, you will create an application lifecycle build pipeline, using a YAML-based template.

### Task 1: Create an Azure DevOps YAML pipeline

In this task, you will create a template-based Azure DevOps YAML pipeline.

1. Go to **Pipelines (1)>Pipelines (2)**. Click on **Create Pipeline (3)** or **New Pipeline** button.

    ![](images/dev42.png)  

2. Select **Azure Repos Git (YAML)**

    ![](images/dev43.png)

3. Select the **eShopOnWeb** repository.

    ![](images/dev44.png)

4. Select **Existing Azure Pipelines YAML File**

    ![](images/dev45.png)

5. Select the path **/.ado/eshoponweb-ci-pr.yml(1)** file then click on **Continue(2)**.

    ![](images/dev46.png)
 
6. On the **Review your pipeline YAML** pane, review the sample pipeline. This is a rather straight-forward .NET application Build pipeline, which does the following:

   - A single Stage: Build
   - A single Job: Build
   - 4 tasks within the Build Job:
   - **DotNet Restore:** With NuGet Package Restore you can install all your project's dependency without having to store them in source control.      
   - **DotNet Build:** Builds a project and all of its dependencies.     
   - **DotNet Test:** .Net test driver used to execute unit tests.    
   - **DotNet Publish:** Publishes the application and its dependencies to a folder for deployment to a hosting system. In this case, it's **Build.ArtifactStagingDirectory**.
        
     ![](images/dev47.png)

7. On the **Review your pipeline YAML** pane, click the down-facing caret symbol next to the **Run (1)** button, click **Save (2)**.

    ![](images/dev48.png)

    >**Note**: we are just creating the pipeline definition for now, without running it. You will first set up an Azure DevOps agent pool and run the pipeline in a later exercise. 

# Exercise 3: Manage Azure DevOps agent pools

In this exercise, you will implement self-hosted Azure DevOps agent.

### Task 1: Configure an Azure DevOps self-hosting agent

In this task, you will configure your lab Virtual Machine as an Azure DevOps self-hosting agent and use it to run a build pipeline.

1. In the **Azure DevOps** portal, in the upper right corner of the Azure DevOps page, click the **User settings (1)** icon, in the dropdown menu, click **Personal access tokens (2)**.

    ![Azure DevOps](images/dev49.png)
  
1. On the **Personal Access Tokens (1)** pane, and click **+ New Token (2)**.

    ![Azure DevOps](images/dev50.png)

1. On the **Create a new personal access token** pane, click the **Show all scopes** link.

    ![Azure DevOps](images/dev51.png)

1. Then specify the following settings and click **Create (5)** (leave all others with their default values):

    | Setting | Value |
    | --- | --- |
    | Name | **eShopOnWeb (1)** |
    | Scope **(custom defined) (2)** | **Agent Pools (3)** |
    | Agent Pools | **Read and manage (4)** |
    
     ![Azure DevOps](images/dev54.png)

1. On the **Success** pane, copy the value of the personal access token to Clipboard.

    > **Note**: Make sure you copy the token. You will not be able to retrieve it once you close this pane. 

    ![Azure DevOps](images/dev53.png)

1. On the **Success** pane, click **Close**.

1. On the **Personal Access Token** pane of the Azure DevOps portal, click **Azure DevOps** symbol in the upper left corner.

    ![Azure DevOps](images/dev55.png)

1. Then click **Organization settings** label in the lower left corner.

    ![Azure DevOps](images/dev56.png)

1. To the left side of the **Overview** pane, in the vertical menu, in the **Pipelines (1)** section, click **Agent pools (2)**. On the **Agent pools** pane, in the upper right corner, click **Add pool (3)**. 

    ![Azure DevOps](images/dev57.png)

1. On the **Add agent pool** pane, in the **Pool type** dropdown list,
   
   - Select **Self-hosted (1)**
   - In the **Name** text box, type **devops-pool (2)**
   - Under **Pipeline permissions** select the checkboxes **(3)**
   - Then click **Create (4)**

     ![Azure DevOps](images/dev60.png)    
    
1. Back on the **Agent pools** pane, click the entry representing the newly created **devops-pool**. 

    ![Azure DevOps](images/dev59.png)

1. On the **Jobs** tab of the **devops-pool** pane,  click the **New agent** button.

    ![Azure DevOps](images/dev61.png)

1. On the **Get the agent** pane, ensure that the **Windows (1)** and **x64 (2)** tabs are selected, and click **Download (3)** to download the zip archive containing the agent binaries to download it into the local **Downloads** folder within your user profile.

    ![Azure DevOps](images/dev62.png)

     > **Note**: If you receive an error message at this point indicating that the current system settings prevent you from downloading the file, in the Browser window, in the upper right corner, click the gearwheel symbol designating the **Settings** menu header, in the dropdown menu, select **Internet Options**, in the **Internet Options** dialog box, click **Advanced**, on the **Advanced** tab, click **Reset**, in the **Reset Browser Settings** dialog box, click **Reset** again, click **Close**, and try the download again.

1. On the LabVM, right click on **Start (1)**, then select **Windows PowerShell (Admin) (2)**.

    ![Azure DevOps](images/dev63.png)

1. Run the following lines to create the **C:\\agent** directory and extract the content of the downloaded archive into it.

    ```powershell
    cd \
    mkdir agent ; cd agent
    $TARGET = Get-ChildItem "$Home\Downloads\vsts-agent-win-x64-*.zip"
    Add-Type -AssemblyName System.IO.Compression.FileSystem
    [System.IO.Compression.ZipFile]::ExtractToDirectory($TARGET, "$PWD")
    ```

     >**Note**: If you encounter an error indicating that the item already exists, please ignore it and proceed with the next steps.

      ![Azure DevOps](images/dev64.png)      

1.  In the same **Administrator: Windows PowerShell** console, run the following to configure the agent:

    ```powershell
    .\config.cmd
    ```

     ![Azure DevOps](images/dev65.png)     

1.  When prompted, specify the values of the following settings:

    | Setting | Value |
    | ------- | ----- |
    | Enter server URL | Enter https://dev.azure.com/odluser<inject key="DeploymentID" enableCopy="false"/>/ |
    | Enter authentication type (press enter for PAT) | **Hit Enter** |
    | Enter personal access token | The access token you recorded earlier in this task |
    | Enter agent pool (press enter for default) | enter **devops-pool** |
    | Enter agent name (press enter for labvm-<inject key="DeploymentID" enableCopy="false"/>) | **Hit Enter** |
    | Enter work folder (press enter for _work) | **Hit Enter** |
    | **(Only if shown)** Enter Perform an unzip for tasks for each step. (press enter for N) | **WARNING**: only press **Enter** if the message is shown|
    | Enter run agent as service? (Y/N) (press enter for N) | **Y** |
    | enter enable SERVICE_SID_TYPE_UNRESTRICTED (Y/N) (press enter for N) | **Y** |
    | Enter User account to use for the service (press enter for NT AUTHORITY\NETWORK SERVICE) | **Hit Enter** |
    | Enter whether to prevent service starting immediately after configuration is finished? (Y/N) (press enter for N) | **Hit Enter** |

    ![Azure DevOps](images/dev66.png)    

     > **Note**: You can run self-hosted agent as either a service or an interactive process. You might want to start with the interactive mode, since this simplifies verifying agent functionality. For production use, you should consider either running the agent as a service or as an interactive process with auto-logon enabled, since both persist their running state and ensure that the agent starts automatically if the operating system is restarted.

     > **Note**: Verify that the agent is reporting the **Listening for Jobs** status.

1.  Switch to the browser window displaying the Azure DevOps portal and close the **Get the agent** pane.

1.  Back on the **Agents (1)** tab of the **devops-pool** pane, note that the newly configured agent is listed with the **Online (2)** status.

    ![Azure DevOps](images/dev67.png)

1.  In the web browser window displaying the Azure DevOps portal, in the upper left corner, click the **Azure DevOps** label.

    ![Azure DevOps](images/dev68.png)

1.  In the browser window displaying the list of projects, click the tile representing your **eShopOnWeb** project.

    ![Azure DevOps](images/dev69.png)
 
1.  On the **eShopOnWeb** pane, in the vertical navigational pane on the left side, in the **Pipelines (1)** section, click **Pipelines (2)**. On the **Recent** tab of the **Pipelines** pane, select **eShopOnWeb (3)**.

    ![Azure DevOps](images/dev70.png)

1. On the **eShopOnWeb** pane, select **Edit**.

    ![Azure DevOps](images/dev71.png)

1. On the **eShopOnWeb** edit pane, in the existing YAML-based pipeline, replace line **13** which says  `vmImage: ubuntu-latest` designating the target agent pool the following content, designating the newly created self-hosted agent pool:

    ```yaml
    name: devops-pool
    demands:
    - agent.name -equals Agentname
    ```

    > **Note**: Replace `Agentname` with **labvm-<inject key="DeploymentID" enableCopy="false"/>**

    ![Azure DevOps](images/dev72.png)    
 
    ![Azure DevOps](images/dev73.png)
    
    > **WARNING**: Be careful with copy/paste, make sure you have same indentation shown above. 
 
1.  On the **eShopOnWeb** edit pane, in the upper right corner of the pane, click **Validate + Save**.

    ![Azure DevOps](images/dev74.png)

1. On the **Save** pane, click **Save** again. This will automatically trigger the build based on this pipeline. 

    ![Azure DevOps](images/dev75.png)

1. Click on **Run**.    

    ![Azure DevOps](images/dev253.png)

1. Click on **Run** again to run the pipeline.

    ![Azure DevOps](images/dev78.png)

1. Click on **View** to provide the permission.

    ![Azure DevOps](images/dev250.png)

1. Click on **Permit**.

    ![Azure DevOps](images/dev251.png)

1. Click on **Permit** on **Permit access**.

    ![Azure DevOps](images/dev81.png)

1. Click on **Buid**.  

    ![Azure DevOps](images/dev252.png)

1. Wait until the build  succeeds.

    ![Azure DevOps](images/dev82.png)

     >**Note**: It might take around 5 minutes to build.

1. Your pipeline will take a name based on the project name. Let's **rename** it for identifying the pipeline better.

1. Go to **Pipelines>Pipelines (1)** and click on the recently created pipeline. Click on the **ellipsis (2)** and **Rename/move (3)** option.
   
    ![Azure DevOps](images/dev83.png)

1. Name it **eshoponweb-ci-pr (1)** and click on **Save (2)**.

    ![Azure DevOps](images/dev84.png) 


   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - If you receive a success message, you can proceed further.
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.


   <validation step="38dc84d9-2b4f-44c8-bf6f-1da2f5a9cde7" />

## Review

In this lab, you learned how to convert classic pipelines into YAML-based ones and how to implement and use self-hosted agents.



