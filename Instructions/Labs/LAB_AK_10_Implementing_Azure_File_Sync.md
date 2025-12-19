# Lab 10: Implementing Azure File Sync

## Lab scenario

To address concerns regarding Distributed File System (DFS) Replication between Contoso's London headquarters and its Seattle–based branch office, you decide to test Azure File Sync as an alternative replication mechanism between two on-premises file shares.

## Lab objectives

In this lab, you will perform:

- **Exercise 1:** Implementing Distributed File System (DFS) Replication in your on-premises environment
- **Exercise 2:** Creating and configuring a sync group
- **Exercise 3:** Replacing DFS Replication with File Sync-based replication
- **Exercise 4:** Verifying replication and enabling cloud tiering
- **Exercise 5:** Troubleshooting replication issues

## Estimated time: 60 Minutes

## Architecture Diagram

   ![](media/mod10art.png)  

## Lab setup

Virtual machines: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, **AZ-800T00A-SEA-SVR2**, and **AZ-800T00A-ADM1** must be running. 

> **Note**: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, **AZ-800T00A-SEA-SVR2**, and **AZ-800T00A-SEA-ADM1** virtual machines are hosting the installation of **SEA-DC1**, **SEA-SVR1**, **SEA-SVR2**, and **SEA-ADM1**, respectively.

### Note 

While performing the lab, when pasting commands, please use **Shift + Insert** to paste them into the Command Prompt in the upcoming steps.

## Exercise 1: Implementing Distributed File System (DFS) Replication in your on-premises environment

### Task 1: Deploy DFS

1. On the **HOSTVM (1)** dropdown menu, select **SEA-ADM1 (2)** to connect to the administrator VM.  

    ![](media/AZ-800-l1-1.png)

1. On the **SEA-ADM1** login screen, sign in as **CONTOSO\Administrator** with the password **Pa55w.rd**.  

     >**Note:** If copy-paste is not working, please type the password manually.

     ![](media/AZ-800-l1-2.1.png)

1. On **SEA-ADM1**, on the **Start (1)** menu, select **Windows PowerShell (2)**.

    ![](media/AZ-800-l7-32.png)

1. After copying the commands, please use **Shift + Insert** to paste them into the Command Prompt in the upcoming steps.

1. In the **Windows PowerShell** console, enter the following, and then press Enter to install Distributed File System (DFS) management tools:

   ```powershell
   Install-WindowsFeature -Name RSAT-DFS-Mgmt-Con -IncludeManagementTools
   ```
    ![](./media/AZ-800-l10-1.png)

1. On the taskbar, select **File Explorer**.

1. In File Explorer, browse to the **C:\Labfiles\AZ-800-Administering-Windows-Server-Hybrid-Core-Infrastructure-master\Allfiles\Labfiles\Lab10 (1)** folder.

1. In File Explorer, in the details pane, right-click on the file **L10_DeployDFS.ps1 (2)**, and then, in the menu, select **Edit (3)**.

    ![](./media/AZ-800-l10-2.png)

   >**Note:** This will automatically open the file **L10_DeployDFS.ps1** in the script pane of Windows PowerShell ISE.

1. In the **Windows PowerShell ISE** script pane, review the script, and then execute it by selecting the **Run Script** icon in the toolbar or by pressing F5. 

   ![](./media/AZ-800-l10-3.png)

   >**Note:** On the **Security warning** pop-up, select **Run once**.

### Task 2: Test DFS deployment

1. On **SEA-ADM1**, select **Start**, enter **DFS Management (1)**, and then select **DFS Management (2)**.

    ![](./media/AZ-800-l10-4.png)

1. In **DFS Management**, in the navigation pane, right-click or access the context menu for **Namespaces (1)**, and then select **Add Namespaces to Display (2)**.

     ![](./media/AZ-800-l10-5.png)

1. In the **Add Namespaces to Display** dialog box, in the list of namespaces, select **\\\contoso.com\Root (1)**, and then select **OK (2)**.

    ![](./media/AZ-800-l10-6.png)

    >**Note:** If you are unable to see the namespace, select **Show Namespaces**.

1. In the navigation pane, right-click or access the context menu for **Replication (1)**, and then select **Add Replication Groups to Display (2)**.

    ![](./media/AZ-800-l10-7.png)

1. In the **Add Replication Groups to Display** dialog box, in the **Replication groups** section, select **Branch1 (1)**, and then select **OK (2)**.

    ![](./media/AZ-800-l10-8.png)

1. In the navigation pane, expand the **Namespaces (1) \\\contoso.com\Root (2)** namespace, and then select the **Data (3)** folder.

1. In the details pane, verify that the **Data (4)** folder has two referrals to the **Data** folder on **SEA-SVR1** and **SEA-SVR2**.

   ![](./media/AZ-800-l10-9.png)

1. In the navigation pane, expand **Replication (1)** and select **Branch1 (2)**.

1. In the details pane, verify that the **S:\\Data (3)** folder on **SEA-SVR1** and on **SEA-SVR2** are members of the **Branch1** replication group.

    ![](./media/AZ-800-l10-10.png)

   >**Note:** DFS Replication replicates the content between the **S:\\Data** folders on **SEA-SVR1** and **SEA-SVR2**.

1. Open two instances of File Explorer, in the navigation pane, expand the **\\\contoso.com\Root (1)** namespace, and then select the **Data (2)** folder. 

1. Select first **SEA-SVR1 (3)**, in **Action** pane under **SEA-SVR1**, select **Open in Explorer (4)**.

    ![](./media/AZ-800-l10-11.png)

1. Select second **SEA-SVR2 (1)**, in **Action** pane under **SEA-SVR2**, select **Open in Explorer (2)**.

   ![](./media/AZ-800-l10-12.png)

1. In the first File Explorer instance, connect to **\\\\SEA-SVR1\\Data**, and then in the second File Explorer instance, connect to **\\\\SEA-SVR2\\Data**.

1. Create a new file with your name in **\\\\SEA-SVR1\\Data**.

1. Verify that the file with your name replicates to **\\\\SEA-SVR2\\Data** after a few seconds. This confirms that DFS Replication is working.

   >**Note:** Wait until the files are replicated and both the File Explorer windows record the same content.
   
   >**Note:** Kindly try to close and reopen two file explorer instances to see replicated files.

## Exercise 2: Creating and configuring a sync group

### Task 1: Create an Azure file share

1. On **SEA-ADM1**, start Microsoft Edge, browse to the **[Azure portal](https://portal.azure.com)**, and sign in by using the credentials of a user account with the Owner role in the subscription you'll be using in this lab.

1. In the Azure portal, in the **Search resources, services, and docs** text box in the toolbar, search for **Storage accounts (1)** and select the **Storage accounts (2)** from Services.

   ![](./media/AZ-800l10-1.png)

1. On the **Storage center | Storage accounts (Blobs)** page, select **+ Create**.

   ![](./media/AZ-800l10-2.png)

1. On the **Basics** tab of the **Create a storage account** page, specify the following settings:

   - Subscription: **Leave the deafult Subscription (1)**
   - Resource group: Select **AZ800-L1001-RG (2)** resource group.
   - Storage account name: **storage<inject key="DeploymentID" enableCopy="false"/> (3)**
   - Region: Any Azure region in your geographical area in which you can create storage accounts **(4)**.

     >**Note:** Use the same region for deploying all resources in this lab.

   - Performance	**Standard (general-purpose v2 account) (5)**

   - Redundancy: **Locally-redundant storage (LRS) (6)**

1. Accept the default values for all other settings, select **Review + Create (7)**, and then select **Create**.

   ![](./media/AZ-800-l10-13.png)

1. After the storage account is created, on the **Deployment** page, select **Go to resource**.

    ![](./media/AZ-800-l10-15.png)

1. On the **storage account** page, select **File shares (2)** under **Data storage (1)**, and then select **+ File share (3)**.

    ![](./media/AZ-800-l10-14.png)

1. On the **New file share** tab, enter **share1 (1)** in the **Name** text box, and then select **Review + Create (2)** and then select **Create**.

    ![](./media/AZ-800-l10-16.png)

### Task 2: Use an Azure file share

1. On **SEA-ADM1**, in the Azure portal, in the details pane, select **share1**.

1. In the details pane, select **Upload**.

    ![](./media/AZ-800-l10-17.png)

1. On the **Upload files** tab, browse to **C:\Labfiles\AZ-800-Administering-Windows-Server-Hybrid-Core-Infrastructure-master\Allfiles\Labfiles\Lab10 (1)**, select **File1.txt (2)** and select **Open (3)**, and then **Upload**. When the upload is complete, close the **Upload files** tab.

    ![](./media/AZ-800-l10-18.png)

1. On the **share1** page, select **Snapshots (2)** Under **Operations (1)**,and select **Add snapshot (3)**, and then select **OK (4)**.
   
    ![](./media/AZ-800-l10-19.png)

1. On the **share1** page, select **Overview**, select **Connect**.

    ![](./media/AZ-800-l10-20.png)

1. select **Show Script**, use the **Copy to clipboard** button to copy the script, and then close the **Connect** tab.

    ![](./media/AZ-800-l10-21.png)

1. On **SEA-ADM1**, switch to the **Windows PowerShell ISE** window, open another tab in the script pane, and paste the copied script into it.

1. Review the content of the script, and then execute it by selecting the **Run Script** icon in the toolbar or by pressing F5. 

   >**Note:** The script mounted the Azure file share to drive letter **Z**.

    ![](./media/AZ-800-l10-22.png)

1. On the taskbar, right-click or access the context menu for File Explorer, select **File Explorer**, and then, in the **Quick Access** text box, type Z:\ and then press Enter.

1. Verify that the file **File1.txt** appears in the details pane. This is the file that you uploaded to the Azure file share.

   ![](./media/AZ-800-l10-23.png)

1. Double-click or select **File1.txt**, and then press Enter to open the file in Notepad. 

1. Use Notepad to modify the file content by appending your name in the last line, save the change, and close Notepad.

1. Right-click or access the context menu for **File1 (1)**, select **Properties (2)**, and then, in the **File1 Properties** window, select the **Previous Versions (1)** tab.

    ![](./media/AZ-800-l10-24.png)

1. Verify that one previous file version is available. Select that version (**File1.txt**), select **Restore (2)** twice, and then select **OK (3)** twice.

    ![](./media/AZ-800-l10-25.png)

1. Double-click or select **File1.txt**, select Enter, and then confirm that it doesn't include your name. This is because you restored the snapshot created before you modified the file.

1. Close **Notepad**.

### Task 3: Deploy Storage Sync Service and a File Sync group

1. On **SEA-ADM1**, in the Azure portal, in the **Search resources, services, and docs** text box in the toolbar, search for **Azure File Sync (1)** and select **Azure File Sync (2)**.

    ![](./media/AZ-800-l10-26.png)

1. On the **Basics** tab of the **Deploy File Sync** page, select the deafult **Subscription (1)**, in the **Resource Group** drop-down list, select **AZ800-L1001-RG (2)**. 

1. In the **Storage Sync Service name** text box, enter **FileSync1 (3)**. 

1. In the **Region (4)** drop-down list, select the same region in which you created the storage account. 

1. On the **Basics** tab of the **Deploy File Sync** page, select **Review + Create (5)** and **Create**.

    ![](./media/AZ-800-l10-27.png)

1. On the **Deployment** blade, once the File Sync is provisioned, select **Go to resource**.

1. On the **FileSync1** **Storage Sync Service** page, select **Sync groups**, and then select **+ Create a Sync group** to create a new File Sync group.

    ![](./media/AZ-800-l10-28.png)

1. On the **Sync group** page, enter **Sync1 (1)** in the **Sync group name** text box and select the deafult **Subscription (2)**

1. Select **Select storage account**, and then, on the **Choose storage account (3)** page, select the storage account that you created. 

   >**Note:** If you can't find the storage account, it was probably deployed to a different Azure region. You need to ensure that the storage account resides in the same region as the Storage Sync Service.

1. In the **Azure File Share** drop-down list, select **share1 (4)**, and then select **Create (5)**.

   ![](./media/sygrp1.png)

1. In the Azure portal, in the **Search resources, services, and docs** text box in the toolbar, search for **Storage Sync Services (1)** and select the **Storage Sync Services (2)** from Services. 

    ![](./media/AZ-800-l10-29.png)

1. On the **Storage Sync Services** page, select **FileSync1** under **Sync** section, select **Registered servers**, and verify that there are no currently registered servers.

    <validation step="f75e9fe1-a77f-4ece-b64e-f02e20b24fde" />

## Exercise 3: Replacing DFS Replication with File Sync-based replication

### Task 1: Add SEA-SVR1 as a server endpoint

1. On **SEA-ADM1**, in the Azure portal, on the **FileSync1 \| Registered servers** page, select the **Azure File Sync agent** link to go to the **Azure File Sync Agent** Microsoft Downloads page.  

    ![](./media/AZ-800-l10-31.png)

1. On the **Azure File Sync Agent** Microsoft Downloads page, select **Download**.

    ![](./media/AZ-800-l10-32.png)

1. Select the checkbox next to the entry for File Sync agent for Windows Server 2025 (**StorageSyncAgent_WS2025.msi (1)**), and select **Next (2)** to start the download. After the download is complete, close the Microsoft Edge tab that opened for the download.

    ![](./media/AZ-800-l10-33.png)

1. Use File Explorer to copy the downloaded file to the **C:\Labfiles\AZ-800-Administering-Windows-Server-Hybrid-Core-Infrastructure-master\Allfiles\Labfiles\Lab10** folder.
   
   >**Note:** If you cannot copy StorageSyncAgent_WS2025.msi from the Downloads folder to C:\Labfiles\AZ-800-Administering-Windows-Server-Hybrid-Core-Infrastructure-master\Allfiles\Labfiles\Lab10, please follow these steps:

      * 1.Open two File Explorer windows.

      * 2.In one window, navigate to the Downloads folder where **StorageSyncAgent_WS2025.msi** is located.

      * 3.In the other window, navigate to **C:\Labfiles\AZ-800-Administering-Windows-Server-Hybrid-Core-Infrastructure-master\Allfiles\Labfiles\Lab10.**

      * 4.Drag and drop the **StorageSyncAgent_WS2025.msi** file from the Downloads folder to the destination folder.

1. In File Explorer displaying the content of the **C:\Labfiles\AZ-800-Administering-Windows-Server-Hybrid-Core-Infrastructure-master\Allfiles\Labfiles\Lab10** folder, in the details pane, select the file **Install-FileSyncServerCore.ps1 (1)**, display its context-sensitive menu, and right click, in the menu, select **Edit (2)**.

    ![](./media/AZ-800-l10-34.png)

    >**Note:** This will automatically open the file **Install-FileSyncServerCore.ps1** in the script pane of Windows PowerShell ISE.

1. In the **Windows PowerShell ISE** script pane, clear the existing script and add the below script.

    ```
      $srvName = 'SEA-SVR1'
      $rgName = 'AZ800-L1001-RG'
      $fsName = 'FileSync1'
      
      New-Item -Type Directory -Path "\\$srvName\c$\Temp" -Force
      Copy-Item -Path C:\Labfiles\AZ-800-Administering-Windows-Server-Hybrid-Core-Infrastructure-master\Allfiles\Labfiles\Lab10\StorageSyncAgent_WS2025.msi -Destination "\\$srvName\c$\Temp\" -PassThru
      
      Invoke-Command -ComputerName $srvName -ArgumentList ($rgName, $fsName) {
      	param($rgName, $fsName)
      	Install-PackageProvider -Name NuGet -Force; 
      	Install-Module az.StorageSync -Force;
      	Start-Process -FilePath "C:\Temp\StorageSyncAgent_WS2025.msi" -ArgumentList "/quiet" -Wait;
      	Connect-AzAccount -UseDeviceAuthentication; 
      	Register-AzStorageSyncServer -ResourceGroupName $rgName -StorageSyncServiceName $fsName | Out-Null;
      	Write-Output "Script finished"
   }
   ```

    ![](./media/AZ-800-l10-35.png)

1. Review the script, and then execute it by selecting the **Run Script** icon in the toolbar or by pressing F5. 

   >**Note:** Monitor the script execution. This should take about 3 minutes.

1. After running the script, you will see a warning message. At the last line, you'll find a prompt saying:
  "To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code."
  you should ignore the first 2 characters of the code and enter only the next 9 characters.

     >**Note**:For example, if the code displayed is **4mAQ843GPMH**, you should enter **AQ843GPMH**. copy the nine-character code to the Clipboard.

    ![](./media/poss.png)

1. Switch to the Microsoft Edge window displaying the Azure portal, open a new tab by selecting **+**, and then, on the new tab, browse to **https://microsoft.com/devicelogin**.

1. In Microsoft Edge, in the **Enter code** dialog box, paste the code you copied into Clipboard, and then, if needed, sign in with your Azure credentials, on the page displaying the message.

    ![](./media/AZ-800-l10-37.png)
   
   >**Note**: Are you trying to sign in to Microsoft Azure PowerShell?, select **Continue**, and then close the Microsoft Edge tab you opened in the previous step.
   
1. Switch to the **Windows PowerShell ISE** window and ensure that the script completed successfully. 

1. Switch back to the Microsoft Edge window displaying the Azure portal, and then, on the **FileSync1 \| Registered servers** page, select **Refresh** to display the current list of registered servers.

1. Verify that the **SEA-SVR1.Contoso.com** server appears on the list of registered servers of the **FileSync1** Storage Sync Service.

    ![](./media/AZ-800-l10-38.png)

1. On **SEA-ADM1**, switch to the File Explorer window, in **Quick access** browse to the **\\\\SEA-SVR1\\Data** share, and verify that the folder doesn't currently contain **File1.txt**.

1. Switch to the Microsoft Edge window displaying the Azure portal, on the **FileSync1 \| Registered servers** page, under **Sync** section, select **Sync Groups**, select **Sync1**, and then, on the **Sync1** page, select **+ Add server endpoint**.

    ![](./media/AZ-800-l10-39.png)

1. On the **Add server endpoint** tab, select **SEA-SVR1.Contoso.com (1)** in the **Registered servers** list.

1. In the **Path** text box, enter **S:\\Data (2)**, and then select **Create (3)**.

   ![](./media/E3S171.png)

1. Switch to the File Explorer window and verify that the **\\\\SEA-SVR1\\Data** folder now contains **File1.txt**.

    ![](./media/AZ-800-l10-40.png)

   >**Note:** if you not able to see **File1.txt**, kindly close and open **File Explorer** window, browse for **\\\\SEA-SVR1\\Data** folder in **Quick access**.
   >**Note:** You uploaded **File1.txt** to the Azure file share, from where it was synced to **SEA-SVR1** by File Sync.

### Task 2: Register SEA-SVR2 with File Sync

1. On **SEA-ADM1**, switch to the **Windows PowerShell ISE** window to the tab of the script pane displaying the content of the **Install-FileSyncServerCore.ps1** file.

1. In the **Windows PowerShell ISE** script pane, in the first line, replace `SEA-SVR1` with `SEA-SVR2`, save the change, and execute the script by selecting the **Run Script** icon in the toolbar or by pressing F5. 

   ![](./media/AZ-800-l10-41.png)

   >**Note:** Monitor the script execution. This should take about 3 minutes.

1. After running the script, you will see a warning message. At the last line, you'll find a prompt saying:
   "To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code."
   you should ignore the first 2 characters of the code and enter only the next 9 characters.

   >**Note**:For example, if the code displayed is **4mAQ843GPMH**, you should enter **AQ843GPMH**. copy the nine-character code to the Clipboard.

   ![](./media/poss.png)

1. Switch to the Microsoft Edge window displaying the Azure portal, open a new tab by selecting **+**, and then, on the new tab, browse to **https://microsoft.com/devicelogin**.

1. In Microsoft Edge, in the **Enter code** dialog box, paste the code you copied into Clipboard, and then, if needed, sign in with your Azure credentials, on the page displaying the message.

   ![](./media/AZ-800-l10-37.png)
   
   >**Note**: Are you trying to sign in to Microsoft Azure PowerShell?, select **Continue**, and then close the Microsoft Edge tab you opened in the previous step.
   
1. Switch to the **Windows PowerShell ISE** window and ensure that the script completed successfully. 

1. When the script completes, switch to the Microsoft Edge window displaying the Azure portal and browse back to the **FileSync1 \| Registered servers** page.

1. Confirm that **SEA-SVR1.Contoso.com** and **SEA-SVR2.Contoso.com** are now both listed as registered servers with the **FileSync1** Storage Sync Service.

   ![](./media/AZ-800-l10-42.png)

### Task 3: Remove DFS Replication and add SEA-SVR2 as a server endpoint

1. On **SEA-ADM1**, select **DFS Management** on the taskbar.

1. In **DFS Management**, in the navigation pane, right-click or access the context menu for **Branch1 (1)**, select **Delete (2)**, select the **Yes, delete the replication group, stop replicating all associated replicated folders, and delete all members of the replication group (1)** option, and then select **OK (2)**.

    ![](./media/AZ-800-l10-43.png)

    ![](./media/AZ-800-l10-44.png)

1. Switch to the Microsoft Edge window displaying the Azure portal, browse back to the **FileSync1** **Storage Sync Service** page, in the list of sync groups, select **Sync1**, and then, on the **Sync1** page, select **+ Add server endpoint**.

     ![](./media/AZ-800-l10-45.png)

1. In the Add server endpoint pane, select **SEA-SVR2.Contoso.com (1)** in the **Registered servers** list, enter **S:\\Data (2)** in the **Path** text box, and then select **Create (3)**.

   ![](./media/SS2.png)

## Exercise 4: Verifying replication and enabling cloud tiering

### Task 1: Verify File Sync

1. On **SEA-ADM1**, switch to the File Explorer window displaying the content of the **\\\\SEA-SVR1\\Data** share. 

1. Create another arbitrarily named file in the **\\\\SEA-SVR1\\Data** folder.

1. On **SEA-ADM1**, switch to the File Explorer window displaying the content of the **\\\\SEA-SVR2\\Data** share and verify that, shortly afterwards, the file with the same name also appears in the **\\\\SEA-SVR2\\Data** folder.

   >**Note** You removed DFS Replication in the previous exercise, which means that File Sync replicated the newly created file.

### Task 2: Enable cloud tiering

1. On **SEA-ADM1**, in the Azure portal, on the **Sync1** sync group page, select **SEA-SVR2.Contoso.com** in the **server endpoints** section.

1. In the Server Endpoint Properties pane, Under **settings (1)** section, select **Cloud Tiering Settings (2)**. 

1. Select **Enable cloud tiering (3)**. In Tier cool files to the cloud when less than of free space is available on your local disk text box, enter **90 (4)**, and select **Enable date policy (5)**. In Tier cool files to the cloud if they haven’t been accessed for more than text box, enter **14 (6)** days, and then select **Save (7)**.  

    ![](./media/AZ-800-l10-46.png)

   >**Note:** After some time, files on **SEA-SVR2** would be automatically tiered. You will trigger this process by using PowerShell.

1. On **SEA-ADM1**, switch to the **Windows PowerShell ISE** window:

1. In the **Windows PowerShell ISE**, in the console pane, trigger tiering immediately by entering the following commands and pressing Enter after each:

   ```powershell
   Enter-PSSession -computername SEA-SVR2
   fsutil file createnew S:\Data\report1.docx 254321098
   fsutil file createnew S:\Data\report2.docx 254321098
   fsutil file createnew S:\Data\report3.docx 254321098
   fsutil file createnew S:\Data\report4.docx 254321098
   Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
   Invoke-StorageSyncCloudTiering -Path S:\Data 
   ```
1. On **SEA-ADM1**, switch to the File Explorer window displaying the content of the **\\\\SEA-SVR2\\Data** folder.

      ![](./media/AZ-800-l10-47.png)

1. In the File Explorer window, add the **Attributes** column in the details pane by right-clicking or accessing the context menu for the **Title** column in the details pane; for example, in the **Name** column, select **More**, select the **Attributes** checkbox, and then select **OK**.

      ![](./media/dataimg.png)

1. Drag the **Attributes** column to be next to the **Name** column, and then note the file dates and their attributes.

1. Identify files with the attribute **L**, **M**, and **O**, which indicate that the tiering took place. 

## Exercise 5: Troubleshooting replication issues

### Task 1: Monitor File Sync replication

1. On **SEA-ADM1**, use File Explorer to copy the **C:\\Windows\\INF** folder to **\\\\SEA-SVR2\\Data\\**. The folder will sync to the cloud endpoint, which will cause sync traffic.

1. On **SEA-ADM1**, switch to the Azure portal displaying the **Sync1** sync group page of the **FileSync1** Storage Sync Service.

1. In the **server endpoints** section, verify that the **Health** of both endpoints has green check marks.

    ![](./media/AZ-800-l10-48.png)

1. Select the **SEA-SVR2.Contoso.com** endpoint in the Server Endpoint Properties pane, review **Sync Activity**, and then close the pane.

1. Select the **Files Synced** graph, and then explore how you can customize the graph by using a filter.

1. Switch to the File Explorer window displaying the content of drive **Z** mapped to the Azure File share and verify that the drive contains the content of the **INF** folder synchronized from **\\\\SEA-SVR2\\Data**.

1. Switch to the Azure portal displaying **Sync1** under **Monitoring (1)** section, select **status (2)** and verify that the **INF** sync traffic is reflected in the **Files Synced** and **Bytes Synced** graphs. The **INF** folder has more than 800 files, and its size is more than 40 MB.

    ![](./media/AZ-800-l10-49.png)

   >**Note:** You might need to refresh the page displaying the Azure portal to see the updated statistics.

### Task 2: Test replication conflict resolution

1. On **SEA-ADM1**, position the File Explorer windows displaying the content of **\\\\SEA-SVR1\Data\\** and **\\\\SEA-SVR2\Data\\** side-by-side.

1. In the File Explorer window displaying the content of **\\\\SEA-SVR2\Data\\**, create a file named **Demo.txt**. 

1. In the File Explorer window displaying the content of **\\\\SEA-SVR1\Data\\**, create a file named **Demo.txt**. 

1. Add an arbitrary text to the first **Demo.txt** file and save the change.

1. Immediately afterwards, add an arbitrary text (different from the one you used in the previous step) to the second **Demo.txt** file and save the change.

   >**Note:** Make sure to save the change to the second file as soon as possible. You're creating files with the same name but different content to intentionally trigger a sync conflict.

1. In each File Explorer window, review their content and verify what they contain, in addition to the **Demo.txt** file, also check for **Demo-SEA-SVR2.txt** (and potentially **Demo-Cloud.txt**). 

   >**Note:** This is because File Sync detected a sync conflict and added a suffix representing the endpoint name (**SEA-SVR2**) or **Cloud** to the file that caused the conflict.

   >**Note:** You might need to wait a few minutes for the sync conflict to occur.

### Review
In this lab, you have completed:
- Deployed and tested DFS deployments.
- Created and used an Azure file share.
- Deployed Storage Sync Service and a File Sync group.
- Added SEA-SVR1 as a server endpoint and registered SEA-SVR2 with File Sync.
- Removed DFS Replication and add SEA-SVR2 as a server endpoint.
- Verified File Sync and enable cloud tiering.
- Monitored File Sync replication and test replication conflict resolution.

### You have successfully completed the lab
