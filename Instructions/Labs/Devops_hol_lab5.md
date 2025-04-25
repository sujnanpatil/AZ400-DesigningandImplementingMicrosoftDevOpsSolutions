# Lab 5: Implementing Security and Compliance in an Azure Pipeline 

## Exercise 1: Implement Security and Compliance in an Azure DevOps pipeline by using Mend Bolt 

### Task 1: Activate Mend Bolt extension 

1. On the Azure DevOps page click on **Azure DevOps** located at top left corner.

    ![Azure DevOps](images/dev199.png)

1. Then click on **Organization Setting** at the left down corner. 

    ![Azure DevOps](images/dev200.png)

1. Navigate to **Extensions (1)** under **General** and click on **Browse marketplace**.

    ![Azure DevOps](images/dev201.png)

1. Search for **Mend Bolt (1)**, click on **Search (2)** icon and then select from the results **(3)**.

    ![Azure DevOps](images/dev202.png)

1. Select **Get it free**.

    ![Azure DevOps](images/dev203.png)

1. Click on **Install**.

    ![Azure DevOps](images/dev204.png)

1. Click on **Proceed to organization**.

    ![Azure DevOps](images/dev205.png)

1. On the **Organization Settings**, select **Mend (1)** under Extensions. Provide your First name, Last name, Work Email, Company Name and other details **(2)** and then click **Create Account (3)** button to start using the Free version.    

    ![Azure DevOps](images/dev206.png)


### Task 2: Create and Trigger a build 

1. On the **Organization Setting** page, click on **Azure DevOps** located at top left corner.

    ![Azure DevOps](images/dev207.png)

1. Select the **eShopOnWeb_MultiStageYAML** project.

    ![Azure DevOps](images/dev208.png)

1. Select **Pipelines (1)** under **Pipelines** section, then select the recent pipeline **(2)**.

    ![Azure DevOps](images/dev209.png)

1. Click on **Edit**.

    ![Azure DevOps](images/dev210.png)

1. On the **Show assistant**, Search for **Mend (1)** and select **Mend Bolt (2)** from the results.

    ![Azure DevOps](images/dev211.png)

1. Under the **Project name**, enter **eShopOnWeb_MultiStageYAML (1)** and then click on **Add (2)**.

    ![Azure DevOps](images/dev212.png)

1. Give 2 Tab spaces, make sure the alignment is there as in the screenshot **(1)** and then click on **Validate and save (2)**.

    ![Azure DevOps](images/dev213.png)

1. Click on **Save**.

    ![Azure DevOps](images/dev-214.png)

1. Click on **Run** to run the pipeline.

    ![Azure DevOps](images/dev215.png)

1. Click on **Run** again.

    ![Azure DevOps](images/dev216.png)

1. Click on **build**.

    ![Azure DevOps](images/dev217.png)

1. Once the build is completed **(1)**, click back navigation **(2)** to see the summary which shows Test results, Build artifacts etc. as shown below.    

    ![Azure DevOps](images/dev218.png)

1. Navigate to **Mend Bolt** tab. This shows the list of all vulnerable open source components with Vulnerability Risk, Vulnerable Libraries, Severity Distribution.

    ![Azure DevOps](images/dev219.png)

### Task 3: Remove the Azure DevOps billing

In this task, you will remove pipeline billing to eliminate unnecessary charges.

1. On the lab computer, switch to the browser window displaying Azure DevOps organization homepage by clicking on **Azure Devops** from the top left corner.

   ![Branch Policies](images/407.png)

1. Select **Organization Settings** at bottom left corner.

   ![Branch Policies](images/dev200.png)

1. Under **Organization Settings** select **Billing (1)** from the left pane and click on **Change billing (2)** button to open Change billing pane.

   ![Branch Policies](images/dev220.png)

1. In the **Change billing** pane, select **Remove billing (1)** setting and click on **Save (2)**.      

   ![Branch Policies](images/dev221.png)