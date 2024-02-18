# Lab 5: Implementing and configuring virtualization in Windows Server

## Lab scenario

Contoso is a global engineering and manufacturing company with its head office in Seattle, USA. An IT office and data center are in Seattle to support the Seattle location and other locations. Contoso recently deployed a Windows Server server and client infrastructure. 

Because of many physical servers being currently underutilized, the company plans to expand virtualization to optimize the environment. Because of this, you decide to perform a proof of concept to validate how Hyper-V can be used to manage a virtual machine environment. Also, the Contoso DevOps team wants to explore container technology to determine whether they can help reduce deployment times for new applications and to simplify moving applications to the cloud. You plan to work with the team to evaluate Windows Server containers and to consider providing Internet Information Services (Web services) in a container.

**Note:** An **[interactive lab simulation](https://mslabs.cloudguides.com/guides/AZ-800%20Lab%20Simulation%20-%20Implementing%20and%20configuring%20virtualization%20in%20Windows%20Server)** is available that allows you to click through this lab at your own pace. You may find slight differences between the interactive simulation and the hosted lab, but the core concepts and ideas being demonstrated are the same. 

## Lab objectives
In this lab, you will perform:

- Create and configure VMs.
- Install and configure containers.

## Estimated time: 60 minutes

## Architecture Diagram

   ![](media/mod5art.png)  

## Lab Setup

Virtual machines: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, and **AZ-800T00A-ADM1** must be running. Other VMs can be running, but they aren't required for this lab.

> **Note**: **AZ-800T00A-SEA-DC1**, **AZ-800T00A-SEA-SVR1**, and **AZ-800T00A-SEA-ADM1** virtual machines are hosting the installation of **SEA-DC1**, **SEA-SVR1** and **SEA-ADM1**

1. Select **SEA-ADM1**.

1. Sign in using the following credentials:

   - User name: **Administrator**
   - Password: **Pa55w.rd**
   - Domain: **CONTOSO**

For this lab, you'll use the available VM environment.


### Exercise 1: Creating and configuring VMs

#### Task 1: Create a Hyper-V virtual switch

1. Connect to **SEA-ADM1** and, if needed, sign in as **CONTOSO\Administrator** with a password of **Pa55w.rd**.

1. On **SEA-ADM1**, select **Start (1)**, and then select **Server Manager (2)**.

   ![](media/servermanager.png) 

1. In Server Manager, select **All Servers**.

1. In the Servers list, select the **SEA-SVR1** entry, display its context menu, right click and select **Hyper-V Manager**.

1. In Hyper-V Manager, ensure that **SEA-SVR1.CONTOSO.COM** is selected.

   ![](media/hyper-v-manager.png) 

1. In the Actions pane, select **Virtual Switch Manager**.

   ![](media/hypervmanager1.png) 

1. In the **Virtual Switch Manager**, in the **Create virtual switch** pane, select **Private (1)**, and then select **Create Virtual Switch (2)**.

   ![](media/privateswitch1.png) 

1. In the **Virtual Switch Properties** box, specify the following settings, and then select **OK (3)**:

   - Name: **Contoso Private Switch (1)**
   - Connection type: **Private network (2)**

      ![](media/privateswitch.png) 

#### Task 2: Create a virtual hard disk

1. On **SEA-ADM1**, in Hyper-V Manager connected to **SEA-SVR1**, under **Action** pane, select **New**, and then select **Hard Disk**. The **New Virtual Hard Disk Wizard** starts.

1. On the **Before You Begin** page, select **Next >**.

1. On the **Choose Disk Format** page, select **VHD** and then select **Next >**.

1. On the **Choose Disk Type** page, select **Differencing**, and then select **Next >**.

1. On the **Specify Name and Location** page, specify the following settings, and then select **Next >**:

   - Name: **SEA-VM1**
   - Location: **C:\Base**

1. On the **Configure Disk** page, in the **Location** box, enter **C:\Base\BaseImage.vhd**, and then select **Next >**.

1. On the **Summary** page, select **Finish**.

   ![](media/finish.png) 

#### Task 3: Create a virtual machine

1. On **SEA-ADM1**, in Hyper-V Manager, select **New**, and then select **Virtual Machine**. The **New Virtual Machine Wizard** starts.

1. On the **Before You Begin** page, select **Next >**.

   ![](media/next1.png) 

1. On the **Specify Name and Location** page, enter **SEA-VM1 (1)**, and then select the check box next to **Store the virtual machine in a different location (2)**.

1. In the **Location** box, enter **C:\Base (3)**, and then select **Next > (4)**.

   ![](media/next2.png)

1. On the **Specify Generation** page, select **Generation 1**, and then select **Next >**.

1. On the **Assign Memory** page, enter **4096**, and then select **Next >**.

1. On the **Configure Networking** page, select the Connection drop-down menu, select **Contoso Private Switch**, and then select **Next >**.

1. On the **Connect Virtual Hard Disk** page, select **Use an existing virtual hard disk**, and then select **Browse**.

1. Browse to **C:\Base**, select **SEA-VM1.vhd**, select **Open**, and then select **Next >**.

1. On the **Summary** page, select **Finish**. Notice that **SEA-VM1** displays in the Virtual Machines list.

   ![](media/seavm1.png)

1. Select **SEA-VM1**, and then in the Actions pane, under **SEA-VM1**, select **Settings**.

1. In the **Hardware** list, select **Memory**.

1. In the **Dynamic Memory** section, select the check box next to **Enable Dynamic Memory**.

1. Next to **Maximum RAM**, enter **4096**, and then select **OK**.

1. Close Hyper-V Manager.

#### Task 4: Manage Virtual Machines using Windows Admin Center

1. On **SEA-ADM1**, select **Start**, and then select **Windows PowerShell (Admin)**.

   >**Note**: Perform the next two steps in case you have not already installed Windows Admin Center on **SEA-ADM1**.

1. In the **Windows PowerShell** console, enter the following command. and then press Enter to download the latest version of Windows Admin Center:
	
   ```powershell
   Start-BitsTransfer -Source https://aka.ms/WACDownload -Destination "$env:USERPROFILE\Downloads\WindowsAdminCenter.msi"
   ```
1. Enter the following command, and then press Enter to install Windows Admin Center:
	
   ```powershell
   Start-Process msiexec.exe -Wait -ArgumentList "/i $env:USERPROFILE\Downloads\WindowsAdminCenter.msi /qn /L*v log.txt REGISTRY_REDIRECT_PORT_80=1 SME_PORT=443 SSL_CERTIFICATE_OPTION=generate"
   ```

   > **Note**: Wait until the installation completes. This should take about 2 minutes. If the web page does not respond, open **services.msc** and verify that the Windows Admin Center Service is **Started**.

1. On **SEA-ADM1**, start Microsoft Edge, and then go to `https://SEA-ADM1.contoso.com`. 
   
   >**Note**: If the link does not work, on **SEA-ADM1**, open File Explorer, select Downloads folder, in the Downloads folder select **WindowsAdminCenter.msi** file and install manually. After the install completes, refresh Microsoft Edge.

   >**Note**: If you get **NET::ERR_CERT_DATE_INVALID** error, select **Advanced** on the Edge browser page, at the bottom of page select **Continue to sea-adm1-contoso.com (unsafe)**.
   
1. If prompted, in the **Windows Security** dialog box, enter the following credentials, and then select **OK**:

   - Username: **CONTOSO\Administrator**
   - Password: **Pa55w.rd**

   >**Note:** Close **Successfully installed version 2311** pop-up.

   >**Note:** Select **OK**, on the **Successfully updated your extensions** pop-up.

1. On the **All connections** pane, select **+ Add**.

   ![](media/add1.png)

1. On the **Add or create resources** pane, on the **Servers** tile, select **Add**.

1. In the **Server name** text box, enter **sea-svr1.contoso.com**.

1. Ensure that the **Use another account for this connection** option is selected, enter the following credentials, and then select **Add with credentials**:

   - Username: **CONTOSO\Administrator**
   - Password: **Pa55w.rd**

   > **Note**: After performing step 8, if an error message that says **You can add this server to your list of connections, but we can't confirm it's available.** appears, select **Add**. In the All Connections pane,  select **sea-svr1.contoso.com**, and then select **Manage as**. In the **Specify your credentials** dialog box, ensure that the **Use another account for this connection** option is selected, enter the Administrator credentials, and then select **Continue**.

   > **Note**: To perform single sign-on, you would need to set up Kerberos constrained delegation.

1. Select **sea-svr1.contoso.com**, from the left navigation menu, in the **Tools** list, select **Virtual machines (1)**, select the **Summary (2)** tab, and then review its content.

   ![](media/virtualmachines1.png)

1. Select the **Inventory** tab and verify that it contains SEA-VM1.

1.  Select **SEA-VM1** and review its Properties pane.

1. Select **Settings**, and then select **Disks**.

1. Scroll to the bottom of the pane and select **+ Add disk**.

1. Select **New Virtual Hard Disk**.

1. On the **New Virtual Hard Disk** pane, in the **Size (GB)** text box, type **5**, leave other settings with their default values, and then select **Create**.

1. Select **Save disks settings**, and then select **Close**.

1. Back on the **Inventory** page, select **SEA-VM1**.

1. On the **Properties** pane of **SEA-VM1**, select **Power**, and then select **Start** to start **SEA-VM1**.

1. Scroll down and display the statistics for the running VM.

1. Refresh the page, select **Power**, select **Shut down**, and then select **Yes** to confirm.

1. From the lefy navigation menu, in the **Tools** list, select **Virtual switches (1)** and identify the **existing switches (2)**.

   ![](media/virtualswitch.png)

### Exercise 1 results

After this exercise, you should have used Hyper-V Manager and Windows Admin Center to create a virtual switch, a virtual hard disk, a virtual machine, and then manage the virtual machine.

### Exercise 2: Installing and configuring containers

#### Task 1: Install Docker on Windows Server

1. On **SEA-ADM1**, select **Start**, and then select **Windows PowerShell (Admin)**.

1. In the **Windows PowerShell** console, enter the following command to establish remote powershell session on SEA-SVR1:
   ```powershell
    Enter-PSSession -ComputerName SEA-SVR1
   ```
1. In the **Windows PowerShell** console, enter the following commands, and then press Enter to force the use of TLS 1.2 and install the PowerShellGet module:

   ```powershell
   [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12
   Install-PackageProvider -Name NuGet -Force
   Install-Module PowerShellGet -RequiredVersion 2.2.4 -SkipPublisherCheck
   ```
   **Note:** Coopy and execute the commands one by one.

1. When prompted to install NuGet, press the **Y** key, and then press Enter.

1. When prompted to confirm the installation of modules from an untrusted repository, press the **A** key, and then press Enter.

1. After the installation completes, enter the following command, and then press Enter to restart **SEA-SVR1**:

   ```powershell
   Restart-Computer -Force
   ```
1. After **SEA-SVR1** restarts, use the **PowerShell** tool again to establish a new PowerShell Remoting session to **SEA-SVR1**.

   ```powershell
    Enter-PSSession -ComputerName SEA-SVR1
   ```  
   
1. In the **Windows PowerShell** console, enter the following command, and then press Enter to install the Docker Microsoft PackageManagement provider on **SEA-SVR1**:

   ```powershell
   Install-Module -Name DockerProvider -Repository PSGallery -Force
   ```
1. At the NuGet Provider prompt, press the **Y** key, and then press Enter.
1. In the **Windows PowerShell** console, enter the following command, and then press Enter to install the Docker runtime on **SEA-SVR1**:

   ```powershell
   Install-Package -Name docker -ProviderName DockerProvider
   ```
1. When prompted to confirm, press the **A** key, and then press Enter.
1. After the installation completes, enter the following commands, and then press Enter to restart **SEA-SVR1**:

   ```powershell
   Restart-Computer -Force
   ```
#### Task 2: Install and run a Windows container

1. After **SEA-SVR1** restarts, use the below PowerShell command again to establish a new PowerShell Remoting session to **SEA-SVR1**.
   
   ```powershell
    Enter-PSSession -ComputerName SEA-SVR1
   ```

1. In the **Windows PowerShell** console, enter the following command, and then press Enter to verify the installed version of Docker:

   ```powershell
   Get-Package -Name Docker -ProviderName DockerProvider
   ```
1. Enter the following command, and then press Enter to identify Docker images currently present on **SEA-SVR1**: 

   ```powershell
   docker images
   ```
   > **Note**: Verify that there are no images in the local repository store.

1. Enter the following command, and then press Enter to download a Nano Server image containing an Internet Information Services (IIS) installation:

   ```powershell
   docker pull nanoserver/iis
   ```

   > **Note**: The time it takes to complete the download will depend on the available bandwidth of the network connection from the lab VM to the Microsoft container registry.

1. Enter the following command, and then press Enter to verify that the Docker image has been successfully downloaded:

   ```powershell
   docker images
   ```
1. Enter the following command, and then press Enter to launch a container based on the downloaded image:

   ```powershell
   docker run --isolation=hyperv -d -t --name nano -p 80:80 nanoserver/iis 
   ```

   > **Note**: The docker command starts a container in the Hyper-V isolation mode (which addresses any host operating system incompatibility issues) as a background service (`-d`) and configures networking such that port 80 of the container host maps to port 80 of the container. 

1. Enter the following command, and then press Enter to retrieve the IP address information of the container host:

   ```powershell
   ipconfig
   ```

   > **Note**: Identify the IPv4 address of the Ethernet adapter named vEthernet (nat). This is the address of the new container. Next, identify the IPv4 address of the Ethernet adapter named **Ethernet**. This is the IP address of the host (**SEA-SVR1**) and is set to **172.16.10.12**.

1. On **SEA-ADM1**, switch to the Microsoft Edge window, open another tab and go to **http://172.16.10.12**. Verify that the browser displays the default IIS page.

   ![](media/IPaddress.png)

1. On **SEA-ADM1**, switch back to the PowerShell Remoting session to **SEA-SVR1**, and then, in the **Windows PowerShell** console, enter the following command, and then press Enter to list running containers:

   ```powershell
   docker ps
   ```
   > **Note**: This command provides information on the container that is currently running on **SEA-SVR1**. Record the container ID because you will use it to stop the container. 

1. Enter the following command, and then press Enter to stop the running container (replace the `<ContainerID>` placeholder with the container ID you identified in the previous step): 

   ```powershell
   docker stop <ContainerID>
   ```
1. Enter the following command, and then press Enter to verify that the container has stopped:

   ```powershell
   docker ps
   ```

#### Task 3: Use Windows Admin Center to manage containers

1. On **SEA-ADM1**, in the Windows Admin Center, in the Tools menu of **sea-svr1.contoso.com**, select **Containers**. 

   >**Note:** If prompted to close the **PowerShell** session, select **Continue**.

1. In the Containers pane, browse through the **Overview**, **Containers**, **Images**, **Networks**, and **Volumes** tabs.

### Exercise 2 results

After this exercise, you should have installed Docker on Windows Server, downloaded a Windows container image containing web services, and verified its functionality.

### Review
In this lab, you have completed:
- Create a Hyper-V virtual switch
- Create a virtual hard disk and virtual machine
- Manage virtual machines using Windows Admin Center
- Install Docker on Windows Server and run a Windows container
- Use Windows Admin Center to manage containers

### You have successfully completed the lab
