# Azure DevOps 

### Overall Estimated Duration: 4 Hours

## Overview

In this hands-on lab, you will learn how to implement end-to-end DevOps practices using Azure DevOps and GitHub. You will start by deploying an Azure web app using GitHub Actions and DevOps Starter, followed by configuring self-hosted agents and YAML pipelines to enable CI/CD automation. You will then define continuous integration workflows with branch policies, control release flows with deployment gates based on app health and monitoring, and ensure pipeline security by integrating the Mend Bolt extension to detect open-source vulnerabilities. By the end of the lab, you’ll have a comprehensive understanding of modern DevOps automation, security, and compliance practices.

## Objectives

By the end of this lab, you will be able to:

- **Implementing GitHub Actions for CI/CD**: In this hands-on lab, participants will learn how to implement a GitHub Action workflow to deploy an Azure web app using DevOps Starter. They will configure GitHub Actions for continuous integration and deployment, connect to Azure services, and automate the deployment process for a streamlined DevOps pipeline.

- **Configuring Agent Pools and Understanding Pipeline Styles**: In this hands-on lab, participants will learn how to configure self-hosted agents and implement CI/CD using YAML pipelines in Azure DevOps, enabling full pipeline automation and control within their code repositories.

- **Enabling Continuous Integration with Azure Pipelines**: In this hands-on lab, participants will define and manage YAML-based build pipelines in Azure DevOps, configure branch policies, and implement continuous integration to automate and validate code changes through pull requests.

- **Controlling Deployments using Release Gates**: In this hands-on lab, participants will configure deployment gates in Azure Pipelines to control and automate application releases across environments, ensuring deployments proceed only when predefined health and compliance checks are met.

- **Implementing Security and Compliance in an Azure Pipeline**: In this hands-on lab, participants will implement security and compliance in an Azure DevOps pipeline by integrating the Mend Bolt extension to scan for vulnerabilities, analyze security risks during builds, and manage costs by disabling unnecessary billing.
  
## Prerequisites

- Basic knowledge of Azure DevOps and GitHub repositories.

- Familiarity with CI/CD concepts and pipeline workflows.

- Access to an active Azure subscription and Azure DevOps organization.

- Understanding of YAML syntax and Git-based version control.

- Basic experience working with Azure Web Apps and Application Insights.

## Architecture

The architecture flow begins with setting up Azure DevOps pipelines using GitHub Actions or YAML-based configurations for CI/CD. Self-hosted or Microsoft-hosted agents execute build and deployment tasks, while Application Insights monitors performance and health. Release gates are configured to control staged deployments, and Mend Bolt integrates into the pipeline to scan for security vulnerabilities, ensuring secure, compliant, and automated application delivery across environments.

## Architecture Diagram

  ![](../media/afg15.png)

## Explanation of Components

1. **GitHub Actions**: A powerful automation tool for implementing CI/CD workflows, GitHub Actions enables seamless integration and deployment to Azure. It will help you automate the build and deployment processes for your applications directly from GitHub repositories.

1. **Azure DevOps**: It is a suite of cloud-hosted services offered by Microsoft that supports the entire Software Development Life Cycle (SDLC), from planning and development to testing and deployment. It helps teams collaborate more effectively and streamline the process of building, testing, and deploying software. 

1. **Azure DevOps Pipelines**: Azure DevOps provides a complete suite for managing your DevOps lifecycle, from version control to build, release, and monitoring. In this lab, you will work with YAML-based pipelines and manage your build and release configurations using Azure DevOps.

1. **Self-Hosted Agents**: These agents give you complete control over the build and deployment environment, offering flexibility to run CI/CD tasks on your own infrastructure or virtual machines, ensuring compatibility with your system’s requirements.

1. **Release Gates**: Release Gates are conditions placed on the deployment process in Azure Pipelines to ensure that certain checks are met before releasing to production. These gates could include health checks, approval processes, or even security compliance checks.

1. **Mend Bolt**: A security scanning tool that integrates into your Azure DevOps pipeline. Mend Bolt scans your project for vulnerabilities in open-source components, ensuring that security and compliance are maintained throughout the CI/CD pipeline.

## Getting Started with the Lab
 
## Accessing Your Lab Environment
 
Once you are ready to dive in, your virtual machine and **Lab Guide** will be right at your fingertips within your web browser.

   ![](../media/afg1.png)

## Lab Guide Zoom In/Zoom Out

To adjust the zoom level for the environment page, click the **A↕ : 100%** icon located next to the timer in the lab environment.

   ![Manage Your Virtual Machine](images/dpg2.png)

## Virtual Machine & Lab Guide
 
Your virtual machine is your workhorse throughout the workshop. The lab guide is your roadmap to success.
 
## Exploring Your Lab Resources
 
To get a better understanding of your lab resources and credentials, navigate to the **Environment** tab.
 
   ![Explore Lab Resources](images/afg3.png)
 
## Utilizing the Split Window Feature
 
For convenience, you can open the lab guide in a separate window by selecting the **Split Window** button from the top right corner.
 
 ![Use the Split Window Feature](images/afg4.png)
 
## Managing Your Virtual Machine
 
Feel free to **start, stop, or restart (2)** your virtual machine as needed from the **Resources (1)** tab. Your experience is in your hands!
 
 ![Manage Your Virtual Machine](images/afg5.png)

## Lab Validation

1. After completing the task, hit the **Validate** button under the Validation tab integrated into your lab guide. You can proceed to the next task if you receive a success message. If not, carefully read the error message and retry the step, following the instructions in the lab guide.

   ![Inline Validation](../media/u46.png)

1. If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com.


## Let's Get Started with Azure Portal

1. On your virtual machine, click on the **Azure Portal** icon as shown below:

   ![Launch Azure Portal](images/afg6.png)
   
1. You will see the **Sign in to the Microsoft Azure** tab. Here, enter your credentials:
 
   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>
 
       ![Enter Your Username](images/afg7.png)
 
1. Next, provide your password:
 
   - **Password:** <inject key="AzureAdUserPassword"></inject>
 
       ![Enter Your Password](images/afg8.png)

1. If you see the pop-up **Stay Signed in?**, click **No**.       

1. If an **Action required** pop-up window appears, click on **Ask later**.

   ![Ask Later](images/afg9.png)
    
1. If prompted to stay signed in, you can click **No**.
 
## Steps to Proceed with MFA Setup if the "Ask Later" Option is Not Visible

1. If you see the pop-up **Stay Signed in?**, click **No**.

1. If **Action required** pop-up window appears, click on **Next**.
   
   ![](images/dpg11.png)

1. On **Start by getting the app** page, click on **Next**.
1. Click on **Next** twice.
1. In **android**, go to the play store and Search for **Microsoft Authenticator** and Tap on **Install**.

   ![Install](images/dpg12.png)

   > Note: For Ios, Open the app store and repeat the steps.

   > Note: Skip if already installed.

1. Open the app and tap on **Scan a QR code**.

1. Scan the QR code visible on the screen **(1)** and click on **Next (2)**.

   ![QR code](images/dpg13.png)

1. Enter the digit displayed on the Screen in the Authenticator app on mobile and tap on **Yes**.

1. Once the notification is approved, click on **Next**.

   ![Approved](images/dpg14.png)

1. Click on **Done**.

1. If prompted to stay signed in, you can click **"No"**.

1. Tap on **Finish** in the Mobile Device.

   > NOTE: While logging in again, enter the digits displayed on the screen in the **Authenticator app** and click on Yes.

1. If a **Welcome to Microsoft Azure** pop-up window appears, simply click **"Cancel"** to skip the tour.

1. If you see the pop-up **You have free Azure Advisor recommendations!**, close the window to continue the lab.


This hands-on lab will guide you through implementing end-to-end DevOps practices using Azure DevOps and GitHub. You will deploy an Azure web app with GitHub Actions, set up CI/CD automation with YAML pipelines, and configure release gates based on app health. Additionally, you will integrate the Mend Bolt extension to detect open-source vulnerabilities, ensuring security and compliance in your pipeline.

## Support Contact

The CloudLabs support team is available 24/7, 365 days a year, via email and live chat to ensure seamless assistance anytime. We offer dedicated support channels tailored specifically for learners and instructors, ensuring that all your needs are promptly and efficiently addressed.

Learner Support Contacts:

- Email Support: cloudlabs-support@spektrasystems.com
- Live Chat Support: https://cloudlabs.ai/labs-support

Click **Next** from the lower right corner to move on to the next page.

![Launch Azure Portal](images/dev266.png)

## Happy Learning!!
