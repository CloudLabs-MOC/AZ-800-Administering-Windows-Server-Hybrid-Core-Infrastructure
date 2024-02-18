# Lab 6: Deploying and configuring Windows Server on Azure VMs

## Lab scenario

You need to address concerns regarding your current infrastructure. You have an outdated operational model, a limited use of automation, and Information Security team concerns regarding additional controls that should be applied to Azure VMs running Windows Server-based workloads. You have decided to develop and implement an automated deployment and configuration process for Azure VMs running Windows Server.

The process will involve Azure Resource Manager (ARM) templates and OS configuration through Azure VM extensions. It will also incorporate additional security protection mechanisms beyond those already applied to on-premises systems, such as application allow lists through AppLocker, file integrity checks, and adaptive network/DDoS protection. You will also leverage JIT functionality to restrict administrative access to Azure VMs to public IP address ranges associated with the London headquarters.

Your goal is to deploy and configure Azure VMs running Windows Server in the manner that satisfies manageability and security requirements.

**Note:** An **[interactive lab simulation](https://mslabs.cloudguides.com/guides/AZ-800%20Lab%20Simulation%20-%20Deploying%20and%20configuring%20Windows%20Server%20on%20Azure%20VMs)** is available that allows you to click through this lab at your own pace. You may find slight differences between the interactive simulation and the hosted lab, but the core concepts and ideas being demonstrated are the same. 

## Lab objectives

In this lab, you will perform:

- Author ARM templates for an Azure VM deployment
- Modify ARM templates to include VM extension-based configuration
- Deploy Azure VMs running Windows Server by using ARM templates
- Configure administrative access to Azure VMs running Windows Server
- Configure Windows Server security in Azure VMs

## Estimated time: 90 minutes

## Architecture Diagram

   ![](media/mod6art.png)  

## Lab setup

Virtual machines: **AZ-800T00A-SEA-DC1** and **AZ-800T00A-ADM1** must be running. Other VMs can be running, but they aren't required for this lab.

> **Note**: **AZ-800T00A-SEA-DC1** and **AZ-800T00A-SEA-ADM1** virtual machines are hosting the installation of **SEA-DC1** and **SEA-ADM1**.

1. Select **SEA-ADM1**.

1. Sign in using the following credentials:

   - Username: **Administrator**
   - Password: **Pa55w.rd**
   - Domain: **CONTOSO**

For this lab, you'll use the available VM environment and an Azure subscription. Before you begin the lab, ensure that you have an Azure subscription and a user account with the Owner or Contributor role in that subscription.

## Exercise 1: Authoring Azure Resource Manager (ARM) templates for Azure VM deployment

#### Task 1: Connect to your Azure subscription and enable enhanced security of Microsoft Defender for Cloud

In this task, you will connect to your Azure subscription and enable enhanced security features of Microsoft Defender for Cloud.

1. Connect to **SEA-ADM1**, and then, if needed, sign in as **CONTOSO\Administrator** with a password of **Pa55w.rd**.

1. On **SEA-ADM1**, start Microsoft Edge, go to the [Azure portal](https://portal.azure.com).

1. In the **Sign in** dialog box, copy and paste * Email/Username: <inject key="AzureAdUserEmail"></inject> and then select Next.

1. In the **Enter password** dialog box, copy and paste * Password: <inject key="AzureAdUserPassword"></inject> and then select **Sign in**.

1. On the **Stay signed in?** dialog box, select the Don’t show this again check box and then select **No**.

   >**Note**: Skip the remaining steps in this task and proceed directly to the next one if you have already enabled Microsoft Defender for Cloud in your Azure subscription.

3. In the Azure portal, in the **Search resources, services, and docs** text box, on the toolbar, search for and select **Microsoft Defender for Cloud**.

4. On the **Microsoft Defender for Cloud \| Getting started** page, select **Upgrade**, and then select **Install agents**.

#### Task 2: Generate an ARM template and parameters files by using the Azure portal

In this task, you will use the Azure portal to create resource groups and create a disk in the resource group.

1. On **SEA-ADM1**, in the Azure portal, in the **Search resources, services, and docs** text box, on the toolbar, search for and select **Virtual machines**. In the **Virtual machines** page, select **+ Create**, and then select **Azure virtual machine**.

1. In the **Create a virtual machine** page, on the **Basics** tab, specify the following settings and leave all other settings with their default values, but do not deploy it:

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you will be using in this lab.|
   |Resource group|the name of a new resource group **AZ800-L0601-RG**|
   |Virtual machine name|**az800l06-vm0**|
   |Region|Use the name of an Azure region in which you can provision Azure virtual machines|
   |Availability options|No infrastructure redundancy required|
   |Image|**Windows Server 2022 Datacenter: Azure Edition - Gen2**|
   |Run with Azure Spot discount|No|
   |Size|**Standard_D2s_v3**|
   |Username|**Student**|
   |Password|**Pa55w.rd1234**|
   |Confirm Password|**Pa55w.rd1234**|
   |Public inbound ports|None|
   |Would you like to use an existing Windows Server license|Off|

1. Select **Next: Disks >**, and then on the **Create a virtual machine** page, on the **Disks** tab, specify the following settings, leaving all other settings with their default values:

   |Setting|Value|
   |---|---|
   |OS disk type|**Standard HDD**|

1. Select **Next: Networking >**, and in the **Create a virtual machine** page, on the **Networking** tab, select the **Create new** hyperlink that follows the **Virtual network** text box.
1. On the **Create virtual network** page, specify the following settings, leaving all other settings with their default values, and then select **OK**:

   |Setting|Value|
   |---|---|
   |Name|**az800l06-vnet**|
   |Address range|**10.60.0.0/20**|
   |Subnet name|**subnet0**|
   |Subnet range|**10.60.0.0/24**|

1. Back on the **Create a virtual machine** page, on the **Networking** tab, specify the following settings, leaving all other settings with their default values:

   |Setting|Value|
   |---|---|
   |Public IP|None|
   |NIC network security group|None|
   |Enable accelerated networking|Off|
   |Load balancing options|None|

2. Select **Next: Management >**, leaving all settings with their default values.
   
3. Select **Next: Monitoring >**, and on the **Create a virtual machine** page, on the **Monitoring** tab, specify the following settings, leaving all other settings with their default values:

   |Setting|Value|
   |---|---|
   |Boot diagnostics|**Enable with managed storage account (recommended)**|
   
4. Select **Next: Advanced >**, on the **Advanced** tab of the **Create a virtual machine** page, review the available settings without modifying any of them, and then select **Review + Create**.

   >**Note**: Do not create the virtual machine. You will use for this purpose the autogenerated template.

#### Task 3: Download the ARM template and parameters files from the Azure portal

1. In the Azure portal, on the **Create a virtual machine** page, select **Download a template for automation**.

1. On the **Template** page, select **Download**.

1. Select the ellipsis button next to the **template.zip**, and then in the pop-up menu, select **Show in folder**. This will automatically open File Explorer displaying the content of the **Downloads** folder.

1. In File Explorer, copy **template.zip** to the **C:\Labfiles\AZ-800-Administering-Windows-Server-Hybrid-Core-Infrastructure-master\Allfiles\Labfiles\Lab06** folder on **SEA-ADM1** (create a new folder if needed).

1. From the **Template** page, browse back to the **Create a virtual machine** page, and close it without completing the deployment.

## Exercise 2: Modifying ARM templates to include VM extension-based configuration

#### Task 1: Review the ARM template and parameters files for Azure VM deployment

1. On **SEA-ADM1**, start File Explorer, and then browse to the **C:\Labfiles\AZ-800-Administering-Windows-Server-Hybrid-Core-Infrastructure-master\Allfiles\Labfiles\Lab06** folder.

1. Extract the content of the **template.zip** file into the same folder.

1. Open the **template.json** file in Notepad, and review its content. Keep the Notepad window open.

1. From File Explorer, open the **C:\Labfiles\AZ-800-Administering-Windows-Server-Hybrid-Core-Infrastructure-master\Allfiles\Labfiles\Lab06\\template\\parameters.json** file in Notepad and review its content.

1. Close the Notepad window displaying the **parameters.json** file.

#### Task 2: Add an Azure VM extension section to the existing template

1. On **SEA-ADM1**, in the Notepad window displaying the content of the **template.json** file, insert the following code directly after the `    "resources": [` line):
   >**Note**: If you are using a tool that pastes the code in line by line, intellisense may add extra brackets causing validation errors. You may want to paste the code into notepad first and then paste it into the JSON file.

   ```json
        {
           "type": "Microsoft.Compute/virtualMachines/extensions",
           "name": "[concat(parameters('virtualMachineName'), '/customScriptExtension')]",
           "apiVersion": "2018-06-01",
           "location": "[resourceGroup().location]",
           "dependsOn": [
               "[concat('Microsoft.Compute/virtualMachines/', parameters('virtualMachineName'))]"
           ],
           "properties": {
               "publisher": "Microsoft.Compute",
               "type": "CustomScriptExtension",
               "typeHandlerVersion": "1.7",
               "autoUpgradeMinorVersion": true,
               "settings": {
                   "commandToExecute": "powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)"
              }
           }
        },
   ```

1. Save the change and close the file.

## Exercise 3: Deploying Azure VMs running Windows Server by using ARM templates

#### Task 1: Deploy an Azure VM by using an ARM template

1. On **SEA-ADM1**, switch to the browser window displaying the Azure portal.

1. In the Azure portal, on the toolbar, in the **Search resources, services, and docs** text box, search for and select **Deploy a Custom Template**.

1. In the **Custom deployment** page, select **Build your own template in the editor**.

1. On the **Edit template** page, select **Load file**, upload the template file **template.json** that you edited in the previous exercise, and then select **Save**.

1. On the **Custom deployment** page, select **Edit parameters**.

1. On the **Edit parameters** page, select **Load file**, upload the parameters file **parameters.json** that you reviewed in the previous exercise, and then select **Save**.

1. Back on the **Custom deployment** page, specify the following settings, and leave the other settings with their default values:

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|**AZ800-L0601-RG**|
   |Region|the name of the Azure region into which you can provision Azure VMs|
   |Admin Password|**Pa55w.rd1234**|

1. Select **Review + create**, and then select **Create**.

   >**Note**: The deployment might take about 10 minutes.

1. Verify that the deployment completed successfully.

#### Task 2: Review results of the Azure VM deployment

1. In the Azure portal, on the toolbar, in the **Search resources, services, and docs** text box, search for and select **Resource groups**.

1. On the **Resource groups** page, select the **AZ800-L0601-RG** entry.

1. On the **AZ800-L0601-RG** page, on the **Overview** page, review the list of resources, including the Azure VM **az800l06-vm0**.

1. Within the list of resources, select the Azure VM **az800l06-vm0** entry. 

1. On the **az800l06-vm0** page, under **Settings** section, select **Extensions + applications**, and on the list of extensions, verify that the **customScriptExtension** has been **provisioned successfully**.

1. Browse back to the **AZ800-L0601-RG** page, and in the **Settings** section, select **Deployments**.

1. On the **AZ800-L0601-RG \| Deployments** page, select the **Microsoft.Template** link.

   ![](media/template.png)

1. On the **Microsoft.Template \| Overview** page, select **Template**, and note that this is the same template you used for deployment.
   

 > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - Navigate to the Lab Validation Page, from the upper right corner in the lab guide section.
   > - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.


## Exercise 4: Configuring administrative access to Azure VMs running Windows Server

#### Task 1: Verify the status of Azure Microsoft Defender for Cloud

1. In the Azure portal, on the toolbar, in the **Search resources, services, and docs** text box, search for and select **Microsoft Defender for Cloud**.

1. On the **Overview** page of Microsoft Defender for Cloud, on the vertical menu on the left side, in the **Management** section, select **Environment settings**. 

1. On the **Environment settings** page, expand and select the entry representing your Azure subscription.

   ![](media/sub.png)

1. On the **Settings \| Defender plans** page, select **Settings & monitoring**.

1. On the **Settings & monitoring** page, in the list of extensions, to the right side of the **Log Analytics agent/Azure Monitor agent** entry, select the **Edit configuration** link.

   ![](media/configuration.png)

1. On the **Auto-provisioning configuration**, in the **Workspace selection** ensure that the **Default workspace(s)** entry is selected, select **Apply**, and back on the **Settings & monitoring** page, select **Continue**.

1. On the **Settings \| Defender plans** page, select **Save**.

#### Task 2: Review the Just-in-time VM access settings

1. Browse back to the **Overview** page of Microsoft Defender for Cloud, and then, in the **Cloud Security** section, select **Workload protections**.

1. On the **Microsoft Defender for Cloud \| Workload protections** page, select **Just-in-time VM access**.

   ![](media/time.png)

1. On the **Just-in-time VM access** page, review the **Configured**, **Not Configured**, and **Unsupported** tabs.

   ![](media/vm.png)

   >**Note**: It might take up to 24 hours for the newly deployed VM to appear on the **Unsupported** tab. Rather than wait, continue to the next exercise.

## Exercise 5: Configuring Windows Server security in Azure VMs

#### Task 1: Create and configure an NSG

1. In the Azure portal, on the toolbar, in the **Search resources, services, and docs** text box, search for and select **Network security groups**.

1. On the **Network security groups** page, select **+ Create**.

1. On the **Basics** tab of the **Create network security group** page, specify the following settings (leave others with their default values):

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|**AZ800-L0601-RG**|
   |Name|**az800l06-vm0-nsg1**|
   |Region|the name of the Azure region into which you provisioned the Azure VM **az800l06-vm0**|

1. On the **Create network security group** page, on the **Basics** tab, select **Review + create**, and then select **Create**.

1. In the Azure portal, browse back to the **AZ800-L0601-RG** page, and then in the list of resources, select the entry representing the newly created network security group **az800l06-vm0-nsg1**.

1. On the **az800l06-vm0-nsg1** page, review the listing of the default inbound and outbound security rules, and then in the **Settings** section, select **Inbound
security rules**.

1. On the **az800l06-vm0-nsg1 \| Inbound security rules** page, select **+ Add**.

1. On the **Add inbound security rule** page, specify the following settings, leaving all others with their default values, and then select **Add**:

   |Setting|Value|
   |---|---|
   |Source|**Any**|
   |Source port ranges|*|
   |Destination|**Any**|
   |Service|**HTTP**|
   |Action|**Allow**|
   |Priority|**300**|
   |Name|**AllowHTTPInBound**|

#### Task 2: Configure Inbound HTTP access to an Azure VM

1. In the Azure portal, browse back to the **AZ800-L0601-RG** page, and then in the list of resources, select the entry representing the Azure VM **az800l06-vm0**.

1. On the **az800l06-vm0** page, under **Settings** section select **Networking**.

1. On the **az800l06-vm0 \| Network settings** page, select the link designating the network interface attached to **az800l06-vm0*****.

1. On the page displaying the network interface properties, in the vertical menu on the left side, in the **Settings** section, select **Network security group**. 

1. On the **Network security group** page, in the drop-down list, select **az800l06-vm0-nsg1**, and then select **Save**.

1. In the Azure portal, in the **Search resources, services, and docs** text box, on the toolbar, search for and select **Public IP address**, click **Create**.

1. On Basis tab of create public ip address specify the following and select **Review + Create** and **Create**.
     
   |Setting|Value|
   |---|---|
   |Resource group|**AZ800-L0601-RG**|
   |Name|**az800l06-vm0-pip1**|
   |SKU|**Standard**|
      
1. In the Azure portal, browse back to the **AZ800-L0601-RG** page, and then in the list of resources, select **az800106-vm0-pip1**, click on **Associate**, select **Resource Type** as **Network interface** and select **Network interface** as **az800106-vm0*****, click **OK**.

1. In the Azure portal, browse back to the **AZ800-L0601-RG** page, and then in the list of resources, select the entry representing the network interface, select **IP configurations**, and then select the **ipconfig1** entry.

1. On the **Edit ip configuration** page, in the **Public IP address settings** section, select **Associate public ip address**, and then, below the **Public IP address** drop-down list, select **az800l06-vm0-pip1** and click **Save**.

1. Browse back to the page displaying the network interface properties and select **Overview**. Note the value of the public IP address assigned to the interface.

1. Open another browser tab, browse to that IP address, and verify that a webpage opens, displaying **Hello World from az800l06-vm0**.

   ![](media/hello.png)

1. From the lab computer, start the Remote Desktop app, and try connecting to the same IP address. Verify that the connection fails.

   >**Note**: This is expected because the Azure VM is currently not accessible from the Internet via TCP port 3389. It is accessible only via TCP port 80.

#### Task 3: Trigger re-evaluation of the JIT status of an Azure VM

>**Note**: This task is necessary to trigger re-evaluation of the JIT status of the Azure VM. By default, this might take up to 24 hours.

1. In the Azure portal, browse back to the **AZ800-L0601-RG** page, and then in the list of resources, select the entry representing the Azure VM **az800l06-vm0**.

1. On the **az800l06-vm0** page, under **Settings** section select **Configuration**. 

1. On the **az800l06-vm0 \| Configuration** page, select **Enable just-in-time** VM access and select the **Open Microsoft Defender for Cloud** link.

   ![](media/just.png)

1. On the **Just-in-time VM access** page, verify that the entry representing the **az800l06-vm0** Azure VM appears on the **Configured** tab.

#### Task 4: Connect to the Azure VM via JIT VM access

1. Browse back to the **az800l06-vm0** page, select **Connect**, and

1. On **az800l06-vm0 \| Connect** page, scroll down and click **Select** option in **Native RDP** under **Most Common** section.

1. Select the check box under the **Configure prerequisites for Native RDP** and click on **Configured** and wait for the configuration to be completed.

   ![](media/check-box.png)

1. On **Native RDP**, select **Download RDP File**,  follow prompts to connect to the target Azure VM.

1. When **Enter your credentials** window prompted, click **More Choices** and select **Use a different acount**, specify the following values, and then select **OK**:

   |Setting|Value|
   |---|---|
   |Username|**.\Student**|
   |Password|**Pa55w.rd1234**|

1. Verify that you can successfully access via Remote Desktop the operating system running in the Azure VM and close the Remote Desktop session.

 > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - Navigate to the Lab Validation Page, from the upper right corner in the lab guide section.
   > - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.


### Review
In this lab, you have completed:
- Connect to your Azure subscription and enable enhanced security of Microsoft Defender for Cloud.
- Generate and download an ARM template and parameters files by using the Azure portal.
- Review the ARM template and parameters files for Azure VM deployment.
- Add an Azure VM extension section to the existing template.
- Deploy an Azure VM by using an ARM template.
- Verify the status of Azure Microsoft Defender for Cloud.
- Review Just in time VM access settings.
- Create and configure an NSG and inbound HTTP access to an Azure VM.
  Trigger re-evaluation of the JIT status of an Azure VM.
- Connect to the Azure VM via JIT VM access.

## You have successfully completed this lab.
