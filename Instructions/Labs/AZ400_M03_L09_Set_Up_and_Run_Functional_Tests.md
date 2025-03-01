# Lab 03: Setting Up and Running Functional Tests

## Lab Overview

Software of any complexity can fail in unexpected ways in response to changes. Thus, testing after making changes is required for all but the most trivial (or least critical) applications. Manual testing is the slowest, least reliable, most expensive way to test software.

There are many kinds of automated tests for software applications. The simplest, lowest level test is the unit test. At a slightly higher level, there are integration tests and functional tests. Other kinds of tests, such as UI tests, load tests, stress tests, and smoke tests, are beyond the scope of this lab.

*If you want to know more about the different types of testing, we recommend you reading this article: [Test ASP.NET Core MVC apps](https://learn.microsoft.com/dotnet/architecture/modern-web-apps-azure/test-asp-net-core-mvc-apps).*

## Objectives

In this lab, you will perform the following exercise:

 - Exercise 1: Setup Tests in CI pipeline

## Estimated timing: 30 minutes

## Architecture Diagram

  ![Architecture Diagram](images/lab09-architecture-new.png) 

### Exercise 1: Setup Tests in CI pipeline

In this exercise, you will setup tests in CI pipeline.

#### Task 1: Add tests to the CI pipeline

In this task, you will add the integration and functional tests to the CI Pipeline.

You can notice that the Unit Tests task is already part of the pipeline.

- **Unit Tests** test a single part of your application's logic. One can further describe it by listing some of the things that it isn't. A unit test doesn't test how your code works with dependencies or infrastructure – that's what integration tests are for.

1. From the **eShopOnWeb_MultiStageYAML** project in the Azure DevOps portal, in the vertical navigational pane, select **Pipelines** and then, within the **Pipelines** section, click **Releases(1)**.

1. On the pipeline run pane, click the ellipsis symbol in the upper right corner and, in the dropdown menu, click **Edit pipeline**.

1. At the end of the scipt add new line and add below code with and use the **Tab** key to fix the YAML indentation. 

1. Now you need to add the Integration Tests task after the Unit Tests task:

    ```YAML
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

#### Task 2: Check the tests summary

1. Click on the **Run**, then from the **Run pipeline** tab, click on **Run** again.

1. Wait for the pipeline to start and until it completes the Build Stage successfully.

1. Once completed, the **Test** tab will show as part of the pipeline run. Click on it to check the summary. It looks like shown below:

    ![Tests Summary](images/AZ400_M05_TEST.png)

1. For more details, at the bottom of the page, the table shows a list of the different run tests.

    >**Note**: If the table is empty, you need to reset the filters to have all the details about the tests run.

    ![Tests Table](images/AZ400_M05_TEST1.png)

#### Task 3: Remove the Azure Devops billing

In this task, you will remove pipeline billing to eliminate unnecessary charges.

1. On the lab computer, switch to the browser window displaying Azure DevOps organization homepage by clicking on **Azure Devops** from the top left corner.

   ![Branch Policies](images/407.png)

1. Select **Organization Settings** at bottom left corner.

   ![Branch Policies](images/408.png)

1. Under **Organization Settings** select **Billing** from the left pane and click on **Change billing** button to open Change billing pane.

1. In the **Change billing** pane, select **Remove billing** setting and click on **Save**.   

## Review

In this lab, you learned how to setup and run different tests types using Azure Pipelines and .Net.
