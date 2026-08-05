# Getting Started with Your AZ-800: Administering Windows Server Hybrid Core Infrastructure Workshop
 
Welcome to your AZ-800: Administering Windows Server Hybrid Core Infrastructure workshop! We've prepared a seamless environment for you to explore and learn about configuring and managing Windows Server on-premises, hybrid, and infrastructure as a service (IaaS) platform workloads. Let's begin by making the most of this experience:

## Overview

In these hands-on labs, you will develop the skills required to build, deploy, monitor, and troubleshoot AI-powered cloud solutions on Microsoft Azure. Working as an Azure Developer, you will implement Azure compute and containerization patterns, build serverless APIs with Azure Functions, and integrate services using event-driven and message-based architectures such as Azure Service Bus and Event Grid. The labs also cover Azure data services that support AI workloads, including Azure Cosmos DB for NoSQL, Azure Database for PostgreSQL with pgvector, and Azure Managed Redis for caching, streaming, and vector search. By completing these labs, you will gain the practical experience needed to connect services, orchestrate AI workflows, and build secure, scalable, and observable AI-driven applications on Azure.

## Objectives

By the end of these labs, you will be able to:

1. **Provision and configure Azure AI infrastructure:** Deploy and manage Azure AI resources, Azure Functions, Azure Container Apps, Azure Container Registry, and supporting Azure services required for AI-powered applications.

2. **Develop cloud-native AI applications:** Build serverless APIs, containerized applications, and backend services that integrate Azure AI capabilities using the Azure SDKs, REST APIs, and modern application development patterns.

3. **Implement event-driven and distributed architectures:** Design and integrate applications using Azure Service Bus, Azure Event Grid, and asynchronous messaging to enable scalable, loosely coupled AI workflows.

4. **Work with AI-ready data platforms:** Store, retrieve, and manage structured, unstructured, and vectorized data using Azure Cosmos DB for NoSQL, Azure Database for PostgreSQL with pgvector, Azure Managed Redis, and Azure Storage.

5. **Build intelligent search and retrieval solutions:** Implement vector search, semantic retrieval, and Retrieval-Augmented Generation (RAG) scenarios by combining Azure AI Search with vector-enabled databases and AI models.

6. **Deploy and manage containerized workloads:** Build, publish, deploy, and maintain container images using Azure Container Registry, Azure Container Apps, and Azure Container Registry Tasks.

7. **Secure AI applications and cloud resources:** Configure authentication, authorization, secrets management, managed identities, and network security to protect applications and Azure resources.

8. **Monitor, troubleshoot, and optimize applications:** Collect telemetry, diagnose failures, monitor application health, and improve the performance, scalability, and reliability of AI-powered cloud solutions using Azure monitoring and diagnostic tools.

9. **Integrate Azure services into end-to-end AI workflows:** Connect compute, messaging, storage, databases, and AI services to build automated, scalable, and production-ready intelligent applications.

10. **Apply cloud-native development best practices:** Build resilient, maintainable, and observable AI applications by following modern Azure development patterns, automation techniques, and operational best practices.

## Pre-requisites

- Experience with Azure development concepts.
- Proficiency in a programming language such as C# or Python is recommended.
- Familiarity with Azure compute, containerization, serverless development, event-driven architectures, data services, and REST APIs will help learners get the most from this course.

## Architecture

The lab architecture demonstrates how Azure's cloud-native services work together to build, deploy, integrate, and operate intelligent AI applications. Throughout these labs, you will provision compute resources, implement serverless and containerized workloads, connect applications using event-driven messaging, manage AI-ready data stores, and build secure, scalable, and observable AI-powered solutions.

1. **Azure AI Services and Azure OpenAI:** Provide the intelligence layer for AI-powered applications, enabling capabilities such as natural language processing, document understanding, embeddings, and generative AI experiences.

2. **Azure Compute Services:** Azure Functions, Azure Container Apps, and Azure Container Registry host and execute serverless APIs, containerized applications, and background processing workloads that power AI solutions.

3. **Azure Messaging and Integration Services:** Azure Service Bus and Azure Event Grid enable reliable, asynchronous communication between distributed services, allowing applications to respond to events and orchestrate AI workflows.

4. **Azure Data Services:** Azure Cosmos DB for NoSQL, Azure Database for PostgreSQL with pgvector, Azure Managed Redis, and Azure Storage provide persistent storage, vector search, caching, streaming, and document storage for AI-enabled applications.

5. **Azure Developer Tools:** Azure Portal, Azure CLI, Visual Studio Code, Azure SDKs, and REST APIs are used to provision infrastructure, deploy applications, manage cloud resources, monitor workloads, and troubleshoot AI solutions throughout the labs.

## Explanation of Components

1. **Azure AI Services & Azure OpenAI:** Provide prebuilt and generative AI capabilities that enable applications to understand, generate, and process text, images, documents, and other content using REST APIs and Azure SDKs.

2. **Azure Functions:** Executes event-driven, serverless code that processes requests, orchestrates AI workflows, and integrates Azure services without managing infrastructure.

3. **Azure Container Apps:** Hosts containerized AI applications and APIs, providing scalable, managed execution for microservices and background processing workloads.

4. **Azure Container Registry (ACR):** Stores and manages container images used by Azure Container Apps and other Azure compute services, supporting secure image versioning and deployment.

5. **Azure Service Bus:** Provides reliable message queues and publish/subscribe messaging that decouple application components and enable asynchronous communication between AI services.

6. **Azure Event Grid:** Delivers events from Azure resources and applications, allowing services to react automatically to changes and trigger downstream AI workflows.

7. **Azure Cosmos DB for NoSQL:** Stores application data, conversation history, metadata, and other structured information using a globally distributed NoSQL database.

8. **Azure Database for PostgreSQL with pgvector:** Stores relational data while enabling vector similarity search, supporting Retrieval-Augmented Generation (RAG) and semantic search scenarios.

9. **Azure Managed Redis:** Improves application performance through distributed caching, streaming, session management, and vector search capabilities.

10. **Azure Storage:** Provides secure storage for documents, images, datasets, application assets, and other files processed by AI applications.

11. **Azure SDKs & REST APIs:** Enable developers to integrate Azure services into applications, automate workflows, and interact programmatically with Azure resources.

12. **Azure Portal & Azure CLI:** Provide graphical and command-line tools for provisioning resources, deploying applications, monitoring services, and managing Azure infrastructure throughout the labs.

## Accessing Your Lab Environment
 
Once you're ready to dive in, your virtual machine and **Guide** will be right at your fingertips within your web browser.
 
![Access Your VM and Lab Guide](media/lab1-g1.png)

### Virtual Machine & Lab Guide
 
Your virtual machine is your workhorse throughout the workshop. The lab guide is your roadmap to success.
 
## Exploring Your Lab Resources
 
To get a better understanding of your lab resources and credentials, navigate to the **Environment** tab.
 
![Explore Lab Resources](media/env-tab.png)
 
## Utilizing the Split Window Feature
 
For convenience, you can open the lab guide in a separate window by selecting the **Split Window** button from the Top right corner.
 
![Use the Split Window Feature](media/split-tab.png)
 
## Managing Your Virtual Machine
 
1. Feel free to **Start, Stop, or Restart (2)** your virtual machine as needed from the **Resources (1)** tab. Your experience is in your hands!
 
    ![](media/AZ-800-g4.png)

2. From the **HostVM drop-down (1)** at the top of the lab environment, select the required virtual machine such as **SEA-ADM1, SEA-DC1, or SEA-SVR1 (2)**.

    ![](media/AZ-800-g5.png)

3. When logging into the Hyper-V virtual machines, if a message appears stating **"Press Ctrl+Alt+Delete to unlock"**, navigate to the **Actions** menu in the Virtual Machine Connection window and select the **Ctrl+Alt+Delete** option, as shown in the image below.

    ![Manage Your Virtual Machine](media/login.png)

4. If you face an issue while copying the content from the lab guide and pasting it into the Hyper-V virtual machines, navigate to the **Clipboard** option in the Virtual Machine Connection window and select **Type Clipboard Text (Ctrl + V)**.

    ![Manage Your Virtual Machine](media/clipboard.png)
  
## Lab Guide Zoom In/Zoom Out
 
To adjust the zoom level for the environment page, click the **A↕ : 100%** icon located next to the timer in the lab environment.

   ![](media/lab1-g2.png)

## Lab Duration Extension

1. To extend the duration of the lab, kindly click the **Hourglass** icon in the top right corner of the lab environment. 

    ![Manage Your Virtual Machine](media/gext.png)

    >**Note:** You will get the **Hourglass** icon when 10 minutes are remaining in the lab.

2. Click **OK** to extend your lab duration.
 
   ![Manage Your Virtual Machine](media/gext2.png)

3. If you have not extended the duration prior to when the lab is about to end, a pop-up will appear, giving you the option to extend. Click  **OK** to proceed. 

## Workaround for Copying PowerShell Commands

If you encounter any difficulties with copy-pasting PowerShell commands directly, you can use the following workaround:

1. Open a text editor such as Notepad on your local machine.

2. Copy the PowerShell commands from the lab guide and paste them into the text editor.

3. Ensure that the formatting of the commands remains intact.

4. Copy the commands from the text editor and paste them into your virtual machine's terminal or PowerShell window.

By following this workaround, you can ensure accurate execution of the PowerShell commands in your lab environment.

## Let's Get Started with Azure Portal
 
1. On your virtual machine, click on the **Azure Portal** icon as shown below:
 
   ![](media/AZ-800-g6.png)
    
3. You'll see the **Sign into Microsoft Azure** tab. Here, enter your credentials:
 
   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>
 
      ![Enter Your Username](media/sc900-image-1.png)
 
3. Next, provide your password:
 
   - **Temporary Access Pass:** <inject key="AzureAdUserPassword"></inject>
 
      ![Enter Your Password](media/lab2-12-1.png)

5. If prompted to **Stay signed in**, you can click **No.**
 
    ![](media/AZ-800-g7.png)
 
## Support Contact
 
The CloudLabs support team is available 24/7, 365 days a year, via email and live chat to ensure seamless assistance at any time. We offer dedicated support channels tailored specifically for both learners and instructors, ensuring that all your needs are promptly and efficiently addressed.
 
Learner Support Contacts:
 
- Email Support: cloudlabs-support@spektrasystems.com
- Live Chat Support: https://cloudlabs.ai/labs-support
 
Click "Next" from the bottom right corner to embark on your Lab journey!
 
   ![Start Your Azure Journey](media/sc900-image(3).png)
 
Now you're all set to explore the powerful world of technology. Feel free to reach out if you have any questions along the way. Enjoy your workshop!

