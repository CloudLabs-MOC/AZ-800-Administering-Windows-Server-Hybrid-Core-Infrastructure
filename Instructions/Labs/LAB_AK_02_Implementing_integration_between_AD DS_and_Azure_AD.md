# Lab 2: Implementing integration between AD DS and Microsoft Entra ID

## Lab scenario

To address concerns regarding management and monitoring overhead resulting from using Microsoft Microsoft Entra ID (Azure AD) to authenticate and authorize access to Azure resources, you decide to test integration between on-premises Active Directory Domain Services (AD DS) and Azure AD to verify that this will address business concerns about managing multiple user accounts by using a mix of on-premises and cloud resources.

Additionally, you want to make sure that your approach addresses the Information Security team's concerns and preserves existing controls applied to Active Directory users, such as sign-in hours and password policies. Finally, you want to identify Azure AD integration features that allow you to further enhance on-premises Active Directory security and minimize its management overhead, including Azure AD Password Protection for Windows Server Active Directory and Self-Service Password Reset (SSPR) with password writeback.

Your goal is to implement pass-through authentication between on-premises AD DS and Azure AD.

**Note:** An **[interactive lab simulation](https://mslabs.cloudguides.com/guides/AZ-800%20Lab%20Simulation%20-%20Implementing%20integration%20between%20AD%20DS%20and%20Azure%20AD)** is available that allows you to click through this lab at your own pace. You may find slight differences between the interactive simulation and the hosted lab, but the core concepts and ideas being demonstrated are the same. 

## Lab objectives

In this lab, you will perform:

- Exercise 1: Prepare Entra ID for integration with on-premises AD DS, including adding and verifying a custom domain.
- Exercise 2: Prepare on-premises AD DS for integration with Entra ID, including running IdFix DirSync Error Remediation Tool.
- Exercise 3: Install and configure Microsoft Entra Connect.
- Exercise 4: Verify integration between AD DS and Entra ID by testing the synchronization process.
- Exercise 5: Implementing Entra ID integration features in Active Directory, including Entra ID Password Protection for Windows Server Active Directory and SSPR with password writeback.

## Estimated time: 60 minutes

## Architecture Diagram

   ![](media/mod2art.png)  

## Lab setup

Virtual machines: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, and **AZ-800T00A-ADM1** must be running. Other VMs can be running, but they aren't required for this lab. 

> **Note**: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, and **AZ-800T00A-SEA-ADM1** virtual machines are hosting the installation of **SEA-DC1**, **SEA-SVR1**, and **SEA-ADM1**

1. Select **SEA-ADM1**.

1. Sign in using the following credentials:

   - Username: **Administrator**
   - Password: **Pa55w.rd**
   - Domain: **CONTOSO**

## Exercise 1: Preparing Microsoft Entra ID for AD DS integration

#### Task 1: Create a custom domain in Azure

1. Connect to **SEA-ADM1** and, if needed, sign in as **CONTOSO\Administrator** with a password of **Pa55w.rd**.

1. On **SEA-ADM1**, double-click on the Azure portal, and authenticate with your Azure credentials.

1. On the Azure portal, from the **Search resources, Services, and docs(G+/)** blade, search for and select **Microsoft Entra ID**.

1. On the **Microsoft Entra ID** page, from the left-hand navigation pane, select **Custom domain names**.

1. On the **Custom domain names** page, select **+ Add custom domain**.

1. In the **Custom domain name** pane, in the **Custom domain name** text box, enter **contoso.com**, and then select **Add domain**.

1. On the `contoso.com` custom domain name page, review the Domain Name System (DNS) record types that you would use to verify the domain.

1. Close the pane without **verifying** the domain name.

   > **Note**: While, in general, you would use DNS records to verify a domain, this lab doesn't require the use of a verified domain.

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - Navigate to the Lab Validation Page, from the upper right corner in the lab guide section.
   > - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

#### Task 2: Create a user with the Global Administrator role

1. On **SEA-ADM1**, navigate back to the **Microsoft Entra ID** page in the Azure portal, from the left-hand navigation pane select **Users**.

1. On the **Users** page, select **+ New User** and in drop down select **Create new user**

1. On the **Create new User** page, under **Basics**, in the **User principal name** and **Display Name** text boxes, enter **admin1**.

   > **Note**: Ensure the domain name drop-down menu for the **User name** lists the default domain name ending with `onmicrosoft.com`.

1. Under **Password**, select the **Auto generate** checkbox. Record the user name and password as you'll use it later in this lab.

1. Select **Next: Properties>**

1. Under **settings** in the **Usage location** drop-down list, select **United States**.

1. Select **Next: Assignments>**

1. Under **Assignments** tab , select **+ Add role** and on **Directory roles** page, from the list of roles, select **Global administrator**, and then select **Select**.

1. On the **Create new user** page, select **Next: Review + Create** and **Create**.

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - Navigate to the Lab Validation Page, from the upper right corner in the lab guide section.
   > - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

#### Task 3: Change the password for the user with the Global Administrator role

1. On the Azure portal, select your user account, and then select **Sign out**.

   ![](media/signout.png) 

1. On the **Pick an account** page, select **+ Use another account**.

1. On the **Sign in** page, enter the fully-qualified username of the user account you previously created, and then select **Next**.

1. For the current password, use the password that you copied in the previous step.

1. It asks you to change current password, enter a complex password twice, and then select **Sign in**.

   > **Note**: Record the complex password you used as you'll use it later in this lab.

1. On the **Reduce the risk of attack**, select **Ask later**.

## Exercise 2: Preparing on-premises AD DS for Microsoft Entra ID integration

#### Task 1: Install IdFix

1. On **SEA-ADM1**, open a new tab in Microsoft Edge, and then browse to **https://github.com/microsoft/idfix**.

1. On the **Github** page, you have to scroll down, and under **ClickOnce Launch**, select **launch**.

1. On the status bar, select **Open file**.

1. In the **Application Install - Security Warning** dialog box, select **Install**.

1. In the **IdFix Privacy Statement** dialog box, review the disclaimer, and then select **OK**.

#### Task 2: Run IdFix

1. In the **IdFix** window, select **Query**.

1. If presented with the **Schema Warning** dialog box, select **Yes**.

1. Review the list of objects from Microsoft Entra ID, and observe the **ERROR** and **ATTRIBUTE** columns. In this scenario, the value of **displayName** for **ContosoAdmin** is blank, and the tool's recommended new value appears in the **UPDATE** column.

1. In the **IdFix** window, from the **ACTION** drop-down menu, select **Edit**, and then select **Apply** to automatically implement the recommended changes.

1. In the **Apply Pending** dialog box, select **Yes**.

1. Close the IdFix tool.

## Exercise 3: Downloading, installing, and configuring Microsoft Entra Connect

#### Task 1: Install and configure Microsoft Entra Connect

1. On **SEA-ADM1**, in the Microsoft Edge window displaying the Azure portal, browse to **Microsoft Entra ID**.

1. On the **Microsoft Entra ID** page, from the left-hand navigation pane, select **Microsoft Entra Connect**.

1. On the  **Microsoft Entra Connect | Get started** Get Started page, select **Manage** tab and under **Manage from on-premises: Connect Sync**, select **Download Connect Sync Agent**.

1. On the **Microsoft Entra Connect Agent** page, select **Accept terms & download**

1. On the status bar, select **Open file**.

1. On the **Microsoft Microsoft Entra ID Connect** page, select the **I agree to the license terms and privacy notice** checkbox, and then select **Continue**.

1. On the **Express Settings** page, select **Use express settings**.

1. On the **Connect to Azure AD** page, enter the username and password of the Azure AD Global Administrator user account you created in exercise 1, and then select **Next**.

1. On the **Connect to AD DS** page, enter the following credentials, and then select **Next**:

   - Username: **CONTOSO\Administrator**
   - Password: **Pa55w.rd**

1. On the **Azure AD sign-in configuration** page, note that the new domain you added is in the list of Active Directory UPN Suffixes, but its status is listed as **Not verified**.

   > **Note**: The domain name provided does not have to be a verified domain. While you typically would verify a domain prior to installing Microsoft Entra Connect, this lab doesn't require that verification step.

1. Select the **Continue without matching all UPN suffixes to verified domains** checkbox, and then select **Next**.

1. On the **Ready to configure** page, review the list of actions, and then select **Install**.

   >**Note:** Wait for the installation to get completed.

1. On the **Configuration complete** page, select **Exit**.

## Exercise 4: Verifying integration between AD DS and Microsoft Entra ID

#### Task 1: Verify synchronization in the Azure portal

1. On **SEA-ADM1**, switch to the Microsoft Edge window displaying the Azure portal. 

1. On the **Microsoft Entra ID** page, select **Users**.

1. Note that the user list includes users synced from Microsoft Entra ID.

   > **Note**: After the directory synchronization starts, it can take 15 minutes for Microsoft Entra ID objects to appear in the Microsoft Entra ID portal.

1. In Microsoft Edge, go back to the **Microsoft Entra ID** page.

1. On the **Microsoft Entra ID** page, select **Groups**.

1. Note the list of groups synced from Microsoft Entra ID. 

#### Task 2: Verify synchronization in the Synchronization Service Manager

1. On **SEA-ADM1**, on the **Start** menu, expand **Azure AD Connect**, and then select **Synchronization Service**.

1. In the **Synchronization Service Manager** window, under the **Operations** tab, observe the tasks that were performed to sync the Microsoft Entra ID objects.

1. Select the **Connectors** tab and note the two connectors.

   > **Note**: One connector is for AD DS and the other is for the Microsoft Entra ID tenant. 

1. Close the **Synchronization Service Manager** window.

#### Task 3: Update a user account in Active Directory

1. On **SEA-ADM1**, search and select **Server Manager**, click on the **Tools** menu from top right, select **Active Directory Users and Computers**.

   ![](media/server.png) 

1. In **Active Directory Users and Computers**, expand the **Sales**, and then open the properties for **Sumesh Rajan** by right clicking on the user.

1. In the properties of the user, select the **Organization** tab.

1. In the **Job Title** text box, enter **Manager**, and then select **OK**.

#### Task 4: Create a user account in Active Directory

1. In **Active Directory Users and Computers**, right-click or access the context menu for the **Sales**, select **New**, and then select **User**.

1. In the **New Object - User** window, enter the following user details for each field, and then select **Next >**:

   - First name: **Jordan**
   - Last name: **Mitchell**
   - User logon name: **Jordan**

1. In the **Password** and **Confirm password** fields, enter **Pa55w.rd**, and then select **Next >**.

1. Select **Finish**.

#### Task 5: Sync changes to Azure AD

1. On **SEA-ADM1**, on the **Start** menu, select **Windows PowerShell**.

1. In the **Windows PowerShell** console, enter the following command, and then press Enter to trigger synchronization:

   ```powershell
   Start-ADSyncSyncCycle
   ```

   > **Note**: Once the synchronization cycle starts, it can take 15 minutes for Microsoft Entra ID objects to appear in the Microsoft Entra ID portal.

#### Task 6: Verify changes in Microsoft Entra ID

1. On **SEA-ADM1**, switch to the Microsoft Edge window displaying the Azure portal and go back to the **Microsoft Entra ID** page.

1. On the **Microsoft Entra ID** page, select **Users**.

1. On the **All Users** page, search for the user **Sumesh** and select it.

1. Select the **Edit properties**, select **All**, tab and then verify that the **Job title** attribute has been synced from Microsoft Entra ID.

1. In Microsoft Edge, go back to the **All Users** page.

1. On the **All Users** page, search for the user **Jordan** and select it.

1. Select **Edit properties**, select **All** tab, and review the attributes of the user account that was synced from Microsoft Entra ID.

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - Navigate to the Lab Validation Page, from the upper right corner in the lab guide section.
   > - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.
   
## Exercise 5: Implementing Azure AD integration features in AD DS

#### Task 1: Enable self-service password reset in Azure

1. On **SEA-ADM1**, in the Microsoft Edge window displaying the Azure portal, browse to the **Microsoft Entra ID** page.

1. On the **Microsoft Entra ID** page, from the left-hand navigation pane, select **Licenses**.

1. On the **Licenses** page, select **All products** under **Manage**.

1. Browse to the **All products** page and select **Microsoft Entra ID Premium P2** and click on **+ Assign**.

1. On the **Assign license** page, select **+ Add users and groups**.

1. On the **Add users and groups** page, search for **admin1**, select the **admin1** account from the list of results, and then select **Select**.

1. Back on the **Assign license** page, select **Review + assign**, and then select **Assign**.

   > **Note**: This is necessary in order to implement Azure AD password protection later in this lab.

1. Go back to the **Microsoft Entra ID** page.

1. On the **Microsoft Entra ID** page, select **Password reset**.

1. On the **Password reset** page, note that you can select the scope of users to which to apply the configuration.

   > **Note**: Don't enable the password reset feature because it will break the configuration steps that are required later in this lab.

#### Task 2: Enable password writeback in Microsoft Entra Connect

1. On **SEA-ADM1**, on the **Start** menu, expand **Azure AD Connect**, and then select **Azure AD Connect**.

1. In the **Azure AD Connect** window, select **Configure**.

1. On the **Additional tasks** page, select **Customize synchronization options**, and then select **Next**.

1. On the **Connect to Azure AD** page, enter the username and password of the Azure AD Global Administrator user account you created in exercise 1, and then select **Next**.

1. On the **Connect your directories** page, select **Next**.

1. On the **Domain and OU filtering** page, select **Next**.

1. On the **Optional features** page, select **Password writeback**, and then select **Next**.

   > **Note**: Password writeback is required for self-service password reset of Active Directory users. This allows passwords changed by users in Azure AD to sync to the Active Directory.

1. On the **Ready to configure** page, review the list of actions to be performed, and then select **Configure**.

   >**Note:** Wait for the configurations to get completed.

1. On the **Configuration complete** page, select **Exit**.

#### Task 3: Enable pass-through authentication in Microsoft Entra Connect

1. On **SEA-ADM1**, on the **Start** menu, expand **Azure AD Connect**, and then select **Azure AD Connect**.

1. In the **Azure AD Connect** window, select **Configure**.

1. On the **Additional tasks** page, select **Change user sign-in**, then select **Next**.

1. On the **Connect to Azure AD** page, enter the username and password of the Azure AD Global Administrator user account you created in exercise 1, and then select **Next**.

1. On the **User sign-in** page, select **Pass-through authentication**.

1. Verify that the **Enable single sign-on** checkbox is selected, and then select **Next**.

1. On the **Enable single sign-on** page, select **Enter credentials**.

1. In the **Forest credentials** dialog box, enter the following credentials, and then select **OK**:

   - Username: **Administrator**
   - Password: **Pa55w.rd**

1. On the **Enable single sign-on** page, verify that there's a green check mark next to **Enter credentials**, and then select **Next**.

1. On the **Ready to configure** page, review the list of actions to be performed, and then select **Configure**.

   >**Note:** Wait for the configurations to get completed.

1. On the **Configuration complete** page, select **Exit**.

#### Task 4: Verify pass-through authentication in Azure

1. On **SEA-ADM1**, switch to the Microsoft Edge window displaying the Azure portal and go back to the **Microsoft Entra ID** page.

1. On the **Microsoft Entra ID** page in the Azure portal, select **Microsoft Entra Connect**.

1. On the **Microsoft Entra Connect** page, in left navigation pane select **Connect Sync** and review the information under **User Sign-In**.

1. Under **User Sign-In**, select **Seamless single sign-on**.

1. On the **Seamless single sign-on** page, note the on-premises domain name.

1. In Microsoft Edge, go back to the **Microsoft Entra Connect** page.

1. On the **Microsoft Entra Connect** page, select **Connect sync**, under **User Sign-In**, select **Pass-through authentication**.

1. On the **Passthrough Authentication** page, note the **SEA-ADM1** server name under **Authentication Agent**.

   > **Note**: If you're not able see on-premises domain name and **SEA-ADM1** server name, kindly sign-in to azure portal in private window and perform above steps.
   > **Note**: To install the Azure AD Authentication Agent on multiple servers in your environment, you can download its binaries from the **Pass-through authentication** page in the Azure portal. 

#### Task 5: Install and register the Azure AD Password Protection proxy service and DC agent

1. On **SEA-ADM1**, start Microsoft Edge, go to the Microsoft Downloads website, browse to the **[Azure AD Password Protection for Windows Server Active Directory](https://www.microsoft.com/en-us/download/details.aspx?id=57071)** page where you can download installers, and then select **Download**.

1. On the **Choose the download you want** page, select the **AzureADPasswordProtectionProxySetup.exe** and the **AzureADPasswordProtectionDCAgentSetup.msi** files, and then select **Download**.

1. In the **Download multiple files** dialog box, select **Allow**.

   > **Note**: We recommend installing the proxy service on a server that isn't a domain controller. In addition, the proxy service should not be installed on the same server as the Microsoft Entra Connect agent. You will install the proxy service on **SEA-SVR1** and the Password Protection DC Agent on **SEA-DC1**.

1. On **SEA-ADM1**, switch to the **Windows PowerShell** console window.

1. In the **Windows PowerShell** console, enter the following command, and then press Enter to remove the Zone.Identifier alternate data stream indicating that files have been downloaded from internet:

   ```powershell
   Get-ChildItem -Path "$env:USERPROFILE\Downloads" -File | Unblock-File
   ```
1. Run the following commands to create the **C:\Temp** directory on **SEA-SVR1**, copy the **AzureADPasswordProtectionProxySetup.exe** installer to that directory, and then invoke the installation:

   ```powershell
   New-Item -Type Directory -Path '\\SEA-SVR1.contoso.com\C$\Temp' -Force
   Copy-Item -Path "$env:USERPROFILE\Downloads\AzureADPasswordProtectionProxySetup.exe" -Destination '\\SEA-SVR1.contoso.com\C$\Temp\'
   Invoke-Command -ComputerName SEA-SVR1.contoso.com -ScriptBlock { Start-Process -FilePath C:\Temp\AzureADPasswordProtectionProxySetup.exe -ArgumentList '/quiet /log C:\Temp\AzureADPPProxyInstall.log' -Wait }
   ```

   >**Note:** If it shows errors then wait for sometime and again perform the step-6.

1. Run the following commands to create the **C:\Temp** directory on **SEA-DC1**, copy the **AzureADPasswordProtectionDCAgentSetup.msi** installer to that directory, invoke the installation, and restart the domain controller after the installation completes:

   ```powershell
   New-Item -Type Directory -Path '\\SEA-DC1.contoso.com\C$\Temp' -Force
   Copy-Item -Path "$env:USERPROFILE\Downloads\AzureADPasswordProtectionDCAgentSetup.msi" -Destination '\\SEA-DC1.contoso.com\C$\Temp\'
   Invoke-Command -ComputerName SEA-DC1.contoso.com -ScriptBlock { Start-Process msiexec.exe -ArgumentList '/i C:\Temp\AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart /log C:\Temp\AzureADPPInstall.log' -Wait }
   Restart-Computer -ComputerName SEA-DC1.contoso.com -Force
   ```
1. Run the following commands to validate that the installations resulted in the creation of services necessary to implement Azure AD Password Protection:

   ```powershell
   Get-Service -Computer SEA-SVR1 -Name AzureADPasswordProtectionProxy | fl
   Get-Service -Computer SEA-DC1 -Name AzureADPasswordProtectionDCAgent | fl
   ```

   > **Note**: Verify that each service has the **Running** status.

1. In the **Windows PowerShell** console, enter the following command and press Enter to start a PowerShell Remoting session to **SEA-SVR1**:

   ```powershell
   Enter-PSSession -ComputerName SEA-SVR1
   ```

1. From the **[SEA-SVR1]** prompt, enter the following command and press Enter to register the proxy service with Active Directory (replace the `<Azure_AD_Global_Admin>` placeholder with the fully-qualified user principal name of the AD Global Administrator account you created in exercise 1):

   ```powershell
   Register-AzureADPasswordProtectionProxy -AccountUpn <Azure_AD_Global_Admin> -AuthenticateUsingDeviceCode
   ```

1. As instructed, open another Microsoft Edge window, browse to **https://microsoft.com/devicelogin** and when prompted, enter the code included in the message displayed in the PowerShell Remoting session. 

1. When prompted, authenticate by using the Azure AD Global Administrator user account you created in exercise 1, and then select **Continue**.

1. Switch back to the PowerShell Remoting session, enter the following command and press Enter to exit the PowerShell Remoting session to **SEA-SVR1**:

   ```powershell
   Exit-PSsession
   ```

1. In the **Windows PowerShell** console, enter the following command and press Enter to start a PowerShell Remoting session to **SEA-DC1**:

   ```powershell
   Enter-PSSession -ComputerName SEA-DC1
   ```

1. From the **[SEA-DC1]** prompt, enter the following command and press Enter to register the proxy service with Active Directory (replace the `<Azure_AD_Global_Admin>` placeholder with the fully-qualified user principal name of the Azure AD Global Administrator user account you created in exercise 1):

   ```powershell
   Register-AzureADPasswordProtectionForest -AccountUpn <Azure_AD_Global_Admin> -AuthenticateUsingDeviceCode
   ```

1. As instructed, open another Microsoft Edge window, browse to **https://microsoft.com/devicelogin** and when prompted, enter the code included in the message displayed in the PowerShell Remoting session. 

1. When prompted, authenticate by using the Azure AD Global Administrator user account you created in exercise 1, and then select **Continue**.

1. Switch back to the PowerShell Remoting session, enter the following command, and then press Enter to exit the PowerShell Remoting session to **SEA-DC1**:

   ```powershell
   Exit-PSsession
   ```

#### Task 6: Enable password protection in Azure

1. On **SEA-ADM1**, switch to the Microsoft Edge window displaying the Azure portal, go back to the **Microsoft Entra ID** page, and then, on the **Microsoft Entra ID** page, under **Manage** section, select **Security**.

1. On the **Security** page, under **Manage** section, select **Authentication methods**.

1. On the **Authentication methods** page, under **Manage** section, select **Password protection**.

1. On the **Password protection** page, change the slider for **Enforce custom list** to **Yes**.

1. In the **Custom banned password list** text box, enter the following words (one per line):
 
   - **Contoso**
   - **London**

   > **Note**: The list of banned passwords should be words that are relevant to your organization.

1. Verify that the slider for **Enable password protection on Windows Server Active Directory** is set to **Yes**.

1. Verify that the slider for **Mode** is set to **Audit**, and then select **Save**.


### Review
In this lab, you have completed:
- Preparing Microsoft Entra ID for AD DS integration
- Preparing on-premises AD DS for Microsoft Entra ID integration
- Downloading, installing, and configuring Microsoft Entra Connect
- Verifying integration between AD DS and Microsoft Entra ID
- Implementing Azure AD integration features in AD DS

## You have successfully completed this lab.
