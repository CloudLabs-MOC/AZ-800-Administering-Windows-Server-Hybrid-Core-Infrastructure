# Lab 05: Implementing and configuring virtualization in Windows Server

In this Hands-On-Lab, you will learn how to implement and configure virtualization in Windows Server. You will set up and manage Hyper-V, create and configure virtual machines, and optimize virtual environments to improve performance and security.

## Lab scenario

Contoso is a global engineering and manufacturing company with its head office in Seattle, USA. An IT office and data center are in Seattle to support the Seattle location and other locations. Contoso recently deployed a Windows Server server and client infrastructure. 

Because of many physical servers being currently underutilized, the company plans to expand virtualization to optimize the environment. Because of this, you decide to perform a proof of concept to validate how Hyper-V can be used to manage a virtual machine environment. Also, the Contoso DevOps team wants to explore container technology to determine whether they can help reduce deployment times for new applications and to simplify moving applications to the cloud. You plan to work with the team to evaluate Windows Server containers and to consider providing Internet Information Services (Web services) in a container.

## Lab objectives

In this lab, you will perform:

- **Exercise 1:** Creating and configuring VMs.
- **Exercise 2:** Installing and configuring containers.

## Estimated time: 60 Minutes

## Architecture Diagram

   ![](media/mod5art.png)  

### Note 

While performing the lab, when pasting commands, please use **Shift + Insert** to paste them into the Command Prompt in the upcoming steps.

## Exercise 1: Creating and configuring VMs
In this exercise, you will install and configure the Hyper-V role on a Windows Server. You will ensure that the required hardware and software prerequisites are met and verify that Hyper-V is running correctly.

### Task 1: Create a Hyper-V virtual switch
In this task, you will install the Hyper-V role on a Windows Server machine using Server Manager and PowerShell.

1. On the **HOSTVM dropdown menu (1)**, select **SEA-ADM1 (2)** to connect to the administrator VM.  

    ![](media/AZ-800-l1-1.png)

1. On the **SEA-ADM1 login screen**, sign in as **CONTOSO\Administrator** with the password **Pa55w.rd**.  

    >**Note:** If copy-paste is not working, please type the password manually.

    ![](media/AZ-800-l1-2.1.png)

1. On **SEA-ADM1**, select **Start (1)**, and then select **Server Manager (2)**.

   ![](media/servermanager.png) 

1. In Server Manager, select **All Servers (1)**.

   ![](media/lab5h1.png) 

1. In the Servers list, select the **SEA-SVR1 (2)** entry, display its context menu, right click and select **Hyper-V Manager (3)**.

1. In Hyper-V Manager, ensure that **SEA-SVR1.CONTOSO.COM (1)** is selected.

1. In the Actions pane, select **Virtual Switch Manager (2)**.

    ![](media/AZ-800-l5-1.png) 

1. In the **Virtual Switch Manager**, in the **Create virtual switch** pane, select **Private (1)**, and then select **Create Virtual Switch (2)**.

   ![](media/lab5h3.png) 

1. In the **Virtual Switch Properties** box, specify the following settings, and then select **OK (3)**:

   - Name: **Contoso Private Switch (1)**
   - Connection type: **Private network (2)**

      ![](media/lab5h4.png) 

### Task 2: Create a virtual hard disk
In this task, you will configure essential Hyper-V settings, including virtual switch configuration, VM storage locations, and resource allocation.

1. On **SEA-ADM1**, in Hyper-V Manager connected to **SEA-SVR1**, under **Action (1)** pane, select **New (2)**, and then select **Hard Disk (3)**. The **New Virtual Hard Disk Wizard** starts.

   ![](media/lab5-task2-1.png)

1. On the **Before You Begin** page, select **Next >**.

   ![](media/lab5-task2-2.png)

1. On the **Choose Disk Format** page, select **VHD (1)** and then select **Next > (2)**.

   ![](media/AZ-800-l5-2.png)

1. On the **Choose Disk Type** page, select **Differencing (1)**, and then select **Next > (2)**.

   ![](media/AZ-800-l5-3.png)

1. On the **Specify Name and Location** page, specify the following settings, and then select **Next > (3)**:

   - Name: **SEA-VM1 (1)**
   - Location: **C:\Base (2)**

      ![](media/AZ-800-l5-4.png)

1. On the **Configure Disk** page, in the **Location** box, enter **C:\Base\BaseImage.vhd (1)**, and then select **Next > (2)**.

   ![](media/lab5-task2-6.png)

1. On the **Summary** page, select **Finish**.

   ![](media/AZ-800-l5-5.png) 

### Task 3: Create a virtual machine
In this task, you will create a new virtual machine using Hyper-V Manager and PowerShell, selecting appropriate hardware and storage configurations.

1. On **SEA-ADM1**, in Hyper-V Manager,under **Action (1)** pane, select **New (2)**, and then select **Virtual Machine (3)**. The **New Virtual Machine Wizard** starts.

   ![](media/AZ-800-l5-6.png)

1. On the **Before You Begin** page, select **Next >**.

   ![](media/lab5-task3-2.png)

1. On the **Specify Name and Location** page, enter **SEA-VM1 (1)**, and then select the check box next to **Store the virtual machine in a different location (2)**.

1. In the **Location** box, enter **C:\Base (3)**, and then select **Next > (4)**.

   ![](media/AZ-800-l5-7.png)

1. On the **Specify Generation** page, select **Generation 1 (1)**, and then select **Next > (2)**.

   ![](media/lab5-task3-5.png)

1. On the **Assign Memory** page, enter **4096 (1)**, and then select **Next > (2)**.

   ![](media/lab5-task3-6.png)

1. On the **Configure Networking** page, select the Connection drop-down menu, select **Contoso Private Switch (1)**, and then select **Next > (2)**.

   ![](media/lab5-task3-7.png)

1. On the **Connect Virtual Hard Disk** page, select **Use an existing virtual hard disk (1)**, and then select **Browse (2)**.

   ![](media/lab5-task3-8.png)

1. Browse to **C:\Base**, select **SEA-VM1.vhd (1)**, select **Open (2)**, and then select **Next >**.

   ![](media/lab5-task3-9n.png)

   ![](media/lab5h5.png)

1. On the **Summary** page, select **Finish**. Notice that **SEA-VM1** displays in the Virtual Machines list.

   ![](media/AZ-800-l5-8.png)

   ![](media/lab5-task3-10n.png)

1. Select **SEA-VM1 (1)**, and then in the Actions pane, under **SEA-VM1**, select **Settings (2)**.

   ![](media/AZ-800-l5-9.png)

1. In the **Hardware** list, select **Memory**.

   ![](media/lab5-task3-12.png)

1. In the **Dynamic Memory** section, select the check box next to **Enable Dynamic Memory (1)**. Next to **Maximum RAM**, enter **4096 (2)**, and then select **OK (3)**.

   ![](media/AZ-800-l5-10.png)

1. Close Hyper-V Manager.

### Task 4: Manage Virtual Machines using Windows Admin Center

In this task, you will manage virtual machines using Windows Admin Center

1. On **SEA-ADM1**, start Microsoft Edge, and then browse to `https://SEA-ADM1.contoso.com`.
 
   ![](media/AZ-800-l2-1.png)
   
   >**Note:** If you get **NET::ERR_CERT_DATE_INVALID** error, select **Advanced (2)** on the Edge browser page, at the bottom of page select **Continue to sea-adm1-contoso.com (unsafe) (3)**.

   ![](media/lab7-171.png)
   
1. If prompted, in the **Windows Security** dialog box, enter the following credentials, and then select **OK (3)**:

   - Username: **CONTOSO\Administrator (1)**
   - Password: **Pa55w.rd (2)**

      ![](media/AZ-800-l5-11.png)

1. In the All connections pane, select **+ Add (1)**.

   ![](media/lab7-173.png)

1. In the **Add or create resources** pane, on the **Servers** tile, select **Add (2)**.

1. In the **Server name** text box, enter **sea-svr1.contoso.com** **(1)** and click on **Add** **(2)**.  

   ![](media/lab5h8.png)  

   > **Note:** While performing above step, if you see an error message stating, **"You can add this server to your list of connections, but we can't confirm it's available."**, select **Add**.  

   - In the **All Connections** pane, select **sea-svr1.contoso.com** **(1)** and then click on **Manage as** **(2)**.  
   - In the **Specify your credentials** dialog box:  
     - Ensure that **Use another account for this connection** **(3)** is selected.  
     - Enter the Administrator credentials:  
       - **Username**: **CONTOSO\Administrator** **(4)**  
       - **Password**: **Pa55w.rd** **(5)**  
     - Check the **Use this credential for all connections** checkbox **(6)**.  
     - Click **Continue** **(7)**.  

       ![](media/AZ-800-l5-12.png)

1. On **SEA-ADM1**, in the upper-right corner of the Microsoft Edge window displaying Windows Admin Center, select the **Settings (1)** icon (the cog wheel).

1. In the left pane, under **Gateway** section, select **Extensions (2)**. Review the available extensions.

1. In the details pane, select **Installed extensions** **(3)** and Verify that the following extensions are updated to the latest version:  
   - **Storage (4)**  
   - **Storage Migration Service (4)**  
   - **Storage Replica (4)**  
   - **Virtual machines and switches (5)**     

     ![](media/AZ-800-l5-18.png)

     > **Note:** If any extension shows **Update available (1)**, select it and click **Update (2)** to install the latest version before proceeding.

     ![](media/lab05-p2t1p1.png)

1. Select **Windows Admin Center (1)** to return to the home page, then choose **sea-svr1.contoso.com (2)** from the server list.

   ![](media/lab05-p2t1p2.png)

   ![](media/lab05-p2t1p3.png)

1. Select **sea-svr1.contoso.com**, from the left navigation menu, in the **Tools** list, select **Virtual machines (1)**, select the **Summary (2)** tab, and then review its content.

   ![](media/lab5h10.png)

1. Select the **Inventory (1)** tab and verify that it contains **SEA-VM1 (2)**.

   ![](media/AZ-800-l5-13.png)

1.  Select **SEA-VM1** and review its Properties pane.

    ![](media/lab5-task4-12.png)

1. Select **Settings**, and then select **Disks (1)**.

1. Scroll to the bottom of the pane and select **+ Add disk (2)**.

   ![](media/lab5-task4-14n.png)

1. Select **New Virtual Hard Disk (1)**.

1. On the **New Virtual Hard Disk** pane,Change the disk type to **Fixed size (2)** and in the **Size (GB)** text box, type **5** **(3)** , leave other settings with their default values, and then select **Create (4)**.

   ![](media/AZ-800-l5-14.png)

1. Select **Save disks settings**, and then select **Close**.

    ![](media/AZ-800-l5-15.png)

1. Back on the **Inventory** page, select **SEA-VM1**.

1. On the **Properties** pane of **SEA-VM1**, select **Power (1)**, and then select **Start (2)** to start **SEA-VM1**.

   ![](media/lab05-p2t1p4.png)

1. Scroll down and display the statistics for the running VM.

1. Refresh the page, select **Power (1)**, select **Shut down (2)**, and then select **Yes (3)** to confirm.

   ![](media/lab5h12.png)

   ![](media/lab5h13.png)

1. From the left navigation menu, in the **Tools** list, select **Virtual switches (1)** and identify the **existing switches (2)**.

   ![](media/lab5-task4-22.png)


## Exercise 2: Installing and configuring containers

In this Exercise, you will install and configure containers in the server machine.

### Task 1: Install Docker on Windows Server

In this task, you will install Docker on Windows Server machine

1. On **SEA-ADM1**, right-click on the **Start (1)**, and then select **Windows PowerShell (Admin) (2)**.

   ![](media/lab05-p2t1p5.png)

1. After copying the commands, please use **Shift + Insert** to paste them into the Command Prompt in the upcoming steps.

1. In the **Windows PowerShell** console, enter the following command to establish remote powershell session on SEA-SVR1:

   ```powershell
    Enter-PSSession -ComputerName SEA-SVR1
   ```

   ![](media/lab05-p2t1p6.png)

1. In the **Windows PowerShell** console, enter the following commands, and then press Enter to install the Docker CE (Community Edition) on **SEA-SVR1**:

   ```powershell
   Invoke-WebRequest -UseBasicParsing "https://raw.githubusercontent.com/microsoft/Windows-Containers/Main/helpful_tools/Install-DockerCE/install-docker-ce.ps1" -o install-docker-ce.ps1

   .\install-docker-ce.ps1
   ```
 
1. After the installation completes, enter the following command, and then press Enter to restart **SEA-SVR1**:

   ```powershell
   Restart-Computer -Force
   ```

    ![](media/lab5-12-08.png)

    > **Note:** Wait for **2–3 minutes** to **Restart Computer** before continuing with the next command

1. After **SEA-SVR1** restarts, use the **PowerShell** tool again to establish a new PowerShell Remoting session to **SEA-SVR1**.

   ```powershell
    Enter-PSSession -ComputerName SEA-SVR1
   ```  

1. After reconnecting, run the Docker installation script again to complete the setup:

   ```powershell
   .\install-docker-ce.ps1
   ```

   ![](media/lab5-12-09.png)

### Task 2: Install and run a Windows container

In this task, you will install and run windows container inside server machine.

1. In the **Windows PowerShell** console, enter the following command, and then press Enter to identify Docker images currently present on **SEA-SVR1**: 

   ```powershell
   docker images
   ```

    ![](media/lab5-12-4.1.png)

    > **Note:** For the remaining steps in this task, you will need to run interactive Docker commands that require a TTY-capable terminal. The PowerShell console in Windows Admin Center does not support TTY. Therefore, it is recommended to use the alternative method: open **Windows PowerShell** as Administrator on **SEA-ADM1** and run `Enter-PSSession -ComputerName SEA-SVR1` to establish a PowerShell Remoting session.

    > **Note:** Verify that there are no images in the local repository store.

1. Enter the following command, and then press Enter to download a Nano Server image:

   ```powershell
   docker pull mcr.microsoft.com/windows/nanoserver:ltsc2022
   ```

    > **Note:** The time it takes to complete the download will depend on the available bandwidth of the network connection from the lab VM to the Microsoft container registry.

    ![](media/lab5-12-5.png)

1. Enter the following command, and then press Enter to verify that the Docker image has been successfully downloaded:

   ```powershell
   docker images
   ```

   ![](media/lab05-p2t1p7.png)

1. Enter the following command, and then press Enter to launch a container based on the downloaded image:

   ```powershell
   docker run -d --name nano1 mcr.microsoft.com/windows/nanoserver:ltsc2022 ping -t localhost 
   ```
    ![](media/lab5-12-7.png)

    > **Note:** The docker command starts a container and connects you to the command line interface of the container. 

1. Enter the following command, and then press Enter to retrieve the IP address information of the container host:

   ```powershell
   docker exec nano1 hostname
   ```
    > **Note:** Verify this is the hostname of the container instance, not **SEA-SVR1**.

1. Enter the following command, and then press Enter to create a text file in the container:

   ```powershell
   docker exec nano1 cmd.exe /c "echo Hello World! > C:\Users\Public\Hello.txt"
   ```

    ![](media/lab5-12-8.png)

1. Enter the following command to stop the running container and return to the PowerShell prompt on **SEA-SVR1**:
   ```powershell
   docker stop nano1
   ```

1. Enter the following command, and then press Enter get the container ID for the container you just exited by running the docker ps command:

   ```powershell
   docker ps -a
   ```

    ![](media/lab5-12-9.png)

    > **Note:** The `-a` switch lists all containers, including those that are not currently running.

1. Create a new helloworld image that includes the changes in the first container you ran. To do so, run the docker commit command, replacing \<containerID\> with the ID of your container:

   ```powershell
   docker commit <containerID> helloworld
   ```

1. You now have a custom image that contains the Hello.txt file. You can use the docker images command to see the new image.

   ```powershell
   docker images
   ```

    ![](media/lab5-12-10.png)

1. Run the new container by using the docker run command with the --rm option. When you use this option, Docker automatically removes the container when the command, cmd.exe in this case, stops.

   ```powershell
   docker run --rm helloworld cmd.exe /s /c "type C:\Users\Public\Hello.txt"
   ```

   ![](media/lab05-p2t1p8.png)

   > **Note:** This command line outputs the content of the file you created earlier and stops the container again.

1. Enter the following command, and then press Enter to launch a new container instance of the original image and check if the file you created is present:

   ```powershell
   docker run --rm mcr.microsoft.com/windows/nanoserver:ltsc2022 cmd.exe /s /c "type C:\Users\Public\Hello.txt"
   ```
    
    ![](media/lab5-12-11.png)

    > **Note:** The original image was not modified by adding a file and reverted back to its original state after stopping.

### Task 3: Use Windows Admin Center to manage containers

In this task, you will use Windows Admin Center to manage containers.

1. On **SEA-ADM1**, in the Windows Admin Center, go to the **settings (1)** icon in the top left corner, and then select **Extensions (2)**.

1. In the **Extensions** pane, verify the **Containers (3)** extension is installed and updated under **Installed Extension**. If the extension is not installed, add it from the **Available Extensions** pane.

    ![](media/lab5-12-12.png)

    >**Note:** If the **Containers** extension shows an **Update available** status, update before proceeding.

    ![](media/lab5-12-14.png)

1. On **SEA-ADM1**, in the Windows Admin Center, in the Tools menu of **sea-svr1.contoso.com**, select **Containers**. 

   >**Note:** If prompted to close the **PowerShell** session, select **Continue**.

1. In the Containers pane, browse through the **Overview**, **Containers**, **Images**, **Networks**, and **Volumes** tabs.

    ![](media/lab5-12-15n.png)

### Review
In this lab, you have completed:
- Create a Hyper-V virtual switch
- Create a virtual hard disk and virtual machine
- Manage virtual machines using Windows Admin Center
- Install Docker on Windows Server and run a Windows container
- Use Windows Admin Center to manage containers

### You have successfully completed the lab
