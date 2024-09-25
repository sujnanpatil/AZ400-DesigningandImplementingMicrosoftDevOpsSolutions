# Lab 09: Setting Up and Running Functional Tests

## Lab overview

Software of any complexity can fail in unexpected ways in response to changes. Thus, testing after making changes is required for all but the most trivial (or least critical) applications. Manual testing is the slowest, least reliable, most expensive way to test software.

There are many kinds of automated tests for software applications. The simplest, lowest level test is the unit test. At a slightly higher level, there are integration tests and functional tests. Other kinds of tests, such as UI tests, load tests, stress tests, and smoke tests, are beyond the scope of this lab.

If you want to know more about the different types of testing, we recommend you reading this article: [Test ASP.NET Core MVC apps](https://learn.microsoft.com/dotnet/architecture/modern-web-apps-azure/test-asp-net-core-mvc-apps).

## Objectives

After you complete this lab, you will be able to configure a CI pipeline for a .Net application that includes:

- Unit Tests
- Integration Tests
- Functional Tests

## Estimated timing: 60 minutes

## Architecture Diagram

![Architecture Diagram](images/lab9-architecture-new.png)

### Before you start

## Review applications required for this lab

Identify the applications that you'll use in this lab:
    
-   Microsoft Edge

## Set up an Azure DevOps organization (Skip if already done) 

1. On your lab VM open **Edge Browser** on desktop and navigate to https://go.microsoft.com/fwlink/?LinkId=307137. 

2. In the pop-up for *Help us protect your account*, select **Skip for now (14 days until this is required)**.

3. On the next page accept defaults and click on continue.

    ![Azure DevOps](images/az-400-5-1.png)

4. On the **Almost Done...** page fill the captcha and click on continue. 

    ![Azure DevOps](images/az-400-5-2.png)

# Exercise 0: Configure the lab prerequisites

In this exercise, you will set up the prerequisites for the lab, which consist of a new Azure DevOps project with a repository based on the [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb).

## Task 1: Configure the team project (Skip if already done) 

In this task, you will create an eShopOnWeb Azure DevOps project to be used by several labs.
    1. On your lab computer, in a browser window open your Azure DevOps organization. Click on New Project. Give your project the name eShopOnWeb and leave the other fields with defaults. Click on Create.

## Task 2: Import the eShopOnWeb Git Repository (Skip if already done) 

In this task you will import the eShopOnWeb Git repository that will be used by several labs.

1.  On your lab computer, in a browser window open your Azure DevOps organization and the previously created eShopOnWeb project. Click on Repos>Files , Import a Repository. Select Import. On the Import a Git Repository window, paste the following URL [https://github.com/MicrosoftLearning/eShopOnWeb.git](https://github.com/MicrosoftLearning/eShopOnWeb.git) and click Import:

1. The repository is organized the following way:

    - **.ado** folder contains Azure DevOps YAML pipelines.
    - **.devcontainer** folder container setup to develop using containers (either locally in VS Code or GitHub Codespaces).
    - **infra** folder contains Bicep & ARM infrastructure as code templates used in some lab scenarios.
    - **.github** folder contains YAML GitHub workflow definitions.
    - **src** folder contains the .NET website used in the lab scenarios.

## Task 3: Set main branch as default branch (Skip if already done) 

1. Go to **Repos>Branches**.
2. Hover on the **main** branch then click the ellipsis on the right of the column.
3. Click on **Set as default branch**.

# Exercise 1: Setup Tests in CI pipeline

In this exercise, you will setup tests in CI pipeline.

## Task 1: Import the YAML build definition for CI (Skip if already done) 

In this task, you will add the YAML build definition that will be used to implement the Continuous Integration.

Let's start by importing the CI pipeline named [eshoponweb-ci.yml](https://github.com/MicrosoftLearning/eShopOnWeb/blob/main/.ado/eshoponweb-ci.yml).

1. Go to **Pipelines>Pipelines**.
2. Click on **New Pipeline** button.
3. Select **Azure Repos Git (YAML)**.
4. Select the **eShopOnWeb** repository.
5. Select **Existing Azure Pipelines YAML File**.
6. Select the **main** branch and the **/.ado/eshoponweb-ci.yml** file, then click on **Continue**.

   The CI definition consists of the following tasks:

    - **DotNet Restore:** With NuGet Package Restore you can install all your project's dependency without having to store them in source control.
    - **DotNet Build:** Builds a project and all of its dependencies.
    - **DotNet Test:** .Net test driver used to execute unit tests.
    - **DotNet Publish: **Publishes the application and its dependencies to a folder for deployment to a hosting system. In this case, it's **Build.ArtifactStagingDirector**y.
    - **Publish Artifact - Website:** Publish the app artifact (created in the previous step) and make it available as a pipeline artifact.
    - **Publish Artifact - Bicep:** Publish the infrastructure artifact (Bicep file) and make it available as a pipeline artifact.

7. Click the **Save** button (not **Save and run**) to save the pipeline definition.

## Task 2: Add tests to the CI pipeline

In this task, you will add the integration and functional tests to the CI Pipeline.

You can notice that the Unit Tests task is already part of the pipeline.

- Unit Tests test a single part of your application's logic. One can further describe it by listing some of the things that it isn't. A unit test doesn't test how your code works with dependencies or infrastructure – that's what integration tests are for.

1. Now you need to add the Integration Tests task after the Unit Tests task:

    ```
    - task: DotNetCoreCLI@2
      displayName: Integration Tests
      inputs:
        command: 'test'
        projects: 'tests/IntegrationTests/*.csproj'
      ```

      > **Integration Tests** test how your code works with dependencies or infrastructure. Although it's a good idea to encapsulate your code that interacts with infrastructure like databases and file systems, you will still have some of that code, and you will probably want to test it. Additionally, you should verify that your code's layers interact as you expect when your application's dependencies are fully resolved. This functionality is the responsibility of integration tests.

1. Then you need to add the Functional tests task after the Integration Tests task:

    ```YAML
    - task: DotNetCoreCLI@2
      displayName: Functional Tests
      inputs:
        command: 'test'
        projects: 'tests/FunctionalTests/*.csproj'
    ```

    > **Functional Tests** are written from the perspective of the user, and verify the correctness of the system based on its requirements. Unlike integration tests that are written from the perspective of the developer, to verify that some components of the system work correctly together.

1. Click **Save**, on the **Save** pane, click **Save** again to commit the changes directly into the main branch.

#### Task 4: Check the tests summary

1. Click on the **Run**, then from the **Run pipeline** tab, click on **Run** again.

1. Wait for the pipeline to start and until it completes the Build Stage successfully.

1. Once completed, the **Test** tab will show as part of the pipeline run. Click on it to check the summary. It looks like shown below:

    ![Tests Summary](images/AZ400_M05_L09_Tests_Summary.png)

1. For more details, at the bottom of the page, the table shows a list of the different run tests.

    >**Note**: If the table is empty, you need to reset the filters to have all the details about the tests run.

    ![Tests Table](images/AZ400_M05_L09_Tests_Table.png)

  **Congratulations** on completing the lab! Now, it's time to validate it. Here are the steps:

  > - Navigate to the Lab Validation tab, from the upper right corner in the lab guide section.
  > - Hit the Validate button for the corresponding task. If you receive a success message, you have successfully validated the lab. 
  > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
  > - If you need any assistance, please contact us at labs-support@spektrasystems.com.

## Review

In this lab, you learned how to setup and run different tests types using Azure Pipelines and .Net.
