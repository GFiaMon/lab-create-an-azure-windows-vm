<!-- # LAB: Creating and Connecting to an Azure Windows Virtual Machine -->

<!-- # Lab: Create and Connect to an Azure Windows Virtual Machine -->

In this hands-on lab, you will learn how to **create a Windows Virtual Machine (VM)** on **Microsoft Azure** and **connect** to it using Remote Desktop Protocol (RDP). By the end of this lab, you'll have a fully functional Windows VM up and running in the cloud, accessible from your local machine. Let’s get started!

## Prerequisites

Before you begin, ensure you have the following:

1. **Azure Account**: If you don't have one, [sign up for a free Azure account](https://azure.microsoft.com/free/).
2. **Azure Portal Access**: Familiarity with navigating the [Azure Portal](https://portal.azure.com/).
3. **Remote Desktop Client**:
   - **Windows**: Built-in Remote Desktop Connection.
   - **macOS**: [Microsoft Remote Desktop](https://apps.apple.com/us/app/microsoft-remote-desktop/id1295203466).
   - **Linux**: `rdesktop` or similar clients.
4. **Internet Connection**: Stable internet access to connect to Azure and your VM.


## Step 1: Create a Windows Virtual Machine

You can create an Azure Windows VM using the **Azure Portal** or the **Azure CLI**. This lab covers both methods, but the **Azure Portal** approach is more beginner-friendly.

### Using the Azure Portal

1. **Log In to Azure Portal**:
   - Navigate to [https://portal.azure.com](https://portal.azure.com) and sign in with your Azure account credentials.

   ![devops/azure/images-azure-1/azure-portal-after-login](https://education-team-2020.s3.eu-west-1.amazonaws.com/devops/azure/images-azure-1/azure-portal-after-login.png)

2. **Navigate to Virtual Machines**:
   - In the left-hand sidebar, click on **"Virtual Machines"**. If you don't see it, use the search bar at the top.

3. **Create a New Virtual Machine**:
   - Click the **"Create"** button and select **"Azure virtual machine"** from the dropdown.

4. **Basics Tab**:
   
   Fill in the required information:
   
   - **Subscription**: Choose your Azure subscription.
   - **Resource Group**: 
     - Select an existing resource group or create a new one by clicking **"Create new"** and providing a name (e.g., `MyResourceGroup`).
   - **Virtual Machine Name**: Enter a name for your VM (e.g., `WinVM-Lab`).
   - **Region**: Choose the Azure region closest to you or your users (e.g., `East US`).
   - **Availability Options**: For simplicity, select **"No infrastructure redundancy required"**.
   - **Image**: Choose a Windows Server image (e.g., **"Windows Server 2019 Datacenter"**).
   - **Size**: Click **"See all sizes"** to choose a VM size. For beginners, **"Standard B1s"** is cost-effective.
   - **Administrator Account**:
     - **Username**: Enter a username (e.g., `azureuser`).
     - **Password**: Enter a strong password and confirm it.
     - Alternatively, you can use SSH public key authentication, but for Windows VMs, password-based authentication is typical.
   - **Inbound Port Rules**:
     - Ensure **"RDP (3389)"** is selected to allow remote connections.

5. **Disks Tab**:

   - **OS Disk Type**: Choose **"Standard SSD"** for a balance between cost and performance.
   - **Encryption Type**: Default is acceptable for most cases.

6. **Networking Tab**:

   - **Virtual Network**: Use the default or select an existing VNet.
   - **Subnet**: Choose an existing subnet or create a new one.
   - **Public IP**: Ensure **"Enable"** is selected to assign a public IP for RDP access.
   - **NIC Network Security Group**: Ensure that **"RDP (3389)"** is allowed. If not, add a rule to permit inbound traffic on port 3389.

7. **Management Tab**:

   - Configure monitoring and management settings as needed. For beginners, the defaults are sufficient.

8. **Review + Create Tab**:

   - Azure will validate your configuration. If there are no errors, click **"Create"** to start the deployment.




9. **Deployment**:

   - Wait for the deployment to complete. You can monitor progress in the **"Notifications"** area (bell icon).

   - Once deployment is successful, click **"Go to resource"** to view your new VM.

### Optional: Using Azure CLI

If you prefer using the command line, follow these steps. Ensure you have the [Azure CLI installed](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

1. **Log In to Azure**:

   ```bash
   az login
   ```

2. **Set Variables**:

   ```bash
   RESOURCE_GROUP="MyResourceGroup"
   VM_NAME="WinVM-Lab"
   LOCATION="eastus"
   IMAGE="Win2019Datacenter"
   SIZE="Standard_B1s"
   USERNAME="azureuser"
   PASSWORD="YourP@ssw0rd!"
   ```

3. **Create Resource Group**:

   ```bash
   az group create --name $RESOURCE_GROUP --location $LOCATION
   ```

4. **Create Virtual Machine**:

   ```bash
   az vm create \
     --resource-group $RESOURCE_GROUP \
     --name $VM_NAME \
     --image $IMAGE \
     --size $SIZE \
     --admin-username $USERNAME \
     --admin-password $PASSWORD \
     --public-ip-address-allocation static \
     --open-port 3389
   ```

5. **Retrieve Public IP**:

   ```bash
   az vm list-ip-addresses --resource-group $RESOURCE_GROUP --name $VM_NAME --output table
   ```


## Step 2: Configure Networking and Security

Ensuring your VM is securely accessible is crucial. Here's how to verify and configure networking settings.

### Verify Network Security Group (NSG) Rules

1. **Navigate to NSG**:

   - In the Azure Portal, go to **"Virtual Machines"**, select your VM, and under the **"Settings"** section, click **"Networking"**.
   - Here, you'll see the **"Network interface"** linked to your VM. Click on it to view associated **NSGs**.

2. **Check Inbound Port Rules**:

   - Ensure there's a rule allowing inbound traffic on **port 3389** (RDP).
   - If not, add a new rule:
     - **Destination Port Ranges**: `3389`
     - **Protocol**: `TCP`
     - **Action**: `Allow`
     - **Priority**: Assign a priority number (lower numbers have higher priority; e.g., `1000`).
     - **Name**: `Allow-RDP`

3. **Restrict RDP Access (Recommended)**:

   - To enhance security, restrict RDP access to specific IP addresses (e.g., your home or office IP).
   - Edit the existing RDP rule or create a new one with:
     - **Source**: `IP Addresses`
     - **Source IP Addresses/CIDR Ranges**: Enter your IP (e.g., `203.0.113.0/24`).


## Step 3: Connect to the Windows VM via RDP

Now that your VM is set up and network security is configured, you can connect to it.

### 3.1 Obtain the Public IP Address

1. **Azure Portal**:

   - Navigate to your VM's **Overview** page.
   - Note the **"Public IP address"** listed.


### 3.2 Download the RDP File

1. **Azure Portal**:

   - On the VM's **Overview** page, click the **"Connect"** button at the top.
   - In the **"Connect to virtual machine"** pane, select **"RDP"**.
   - Click **"Download RDP File"**. This downloads a `.rdp` file configured to connect to your VM.

### 3.3 Open the RDP File

1. **Windows**:

   - Locate the downloaded `.rdp` file and double-click it.
   - A **Remote Desktop Connection** window will open, pre-filled with your VM's IP.

2. **macOS**:

   - Open **Microsoft Remote Desktop**.
   - Click **"Add PC"** and import the `.rdp` file or manually enter the VM's IP.

3. **Linux**:

   - Use an RDP client like `rdesktop` or `Remmina`.
   - Example with `rdesktop`:
     ```bash
     rdesktop <Public-IP>
     ```

### 3.4 Authenticate

1. **Login Credentials**:

   - **Username**: The admin username you set during VM creation (e.g., `azureuser`).
   - **Password**: The password you created.

2. **Trust the Certificate**:

   - On first connection, you may receive a warning about the certificate. Trust it to proceed.

3. **Access the Desktop**:

   - Upon successful authentication, you'll see the Windows desktop of your Azure VM.


## Step 4: Verify the Connection

Now that you're connected, perform some basic verification to ensure everything is working correctly.

1. **Check System Information**:
   - Right-click on **"This PC"** > **"Properties"** to view system details like Windows version, system type, and more.

2. **Internet Connectivity**:
   - Open **Edge** or **Internet Explorer** and navigate to a website (e.g., `https://www.microsoft.com`) to confirm internet access.

3. **Install Updates (Optional)**:
   - It's a good practice to update your VM.
   - Go to **"Settings"** > **"Update & Security"** > **"Windows Update"** and click **"Check for updates"**.

4. **Install Necessary Software (Optional)**:
   - Install any software or tools you need for your projects or testing.


## Step 5: Cleanup (Optional)

To avoid unnecessary charges, it's recommended to delete resources you no longer need.

### 5.1 Delete the Virtual Machine

1. **Azure Portal**:

   - Navigate to **"Virtual Machines"**.
   - Select your VM.
   - Click **"Delete"** at the top.
   - Confirm the deletion by typing the VM name and clicking **"Delete"** again.

2. **Using Azure CLI**:

   ```bash
   az vm delete --resource-group MyResourceGroup --name WinVM-Lab --yes --no-wait
   ```

### 5.2 Delete the Resource Group (Optional)

If you no longer need any resources within the resource group:

1. **Azure Portal**:

   - Navigate to **"Resource groups"**.
   - Select the resource group (e.g., `MyResourceGroup`).
   - Click **"Delete resource group"**.
   - Type the resource group name to confirm and click **"Delete"**.

2. **Using Azure CLI**:

   ```bash
   az group delete --name MyResourceGroup --yes --no-wait
   ```

*Deleting the resource group will remove all resources contained within it, including the VM, storage accounts, networking components, etc.*


## Best Practices

1. **Use Strong Passwords**:
   - Ensure your VM admin accounts have strong, unique passwords to prevent unauthorized access.

2. **Enable Network Security**:
   - Restrict RDP access to specific IP ranges using NSGs to enhance security.

3. **Keep the OS Updated**:
   - Regularly install Windows updates to protect against vulnerabilities.

4. **Use Azure Bastion**:
   - For enhanced security, consider using [Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) to connect to your VMs without exposing RDP ports to the internet.

5. **Monitor and Audit**:
   - Use Azure Monitor and Azure Security Center to keep an eye on your VM's performance and security posture.

6. **Backup Important Data**:
   - Implement regular backups using Azure Backup to safeguard your data.


## **Deliverables**

* **Azure VM evidence**

  * Screenshot from **Azure Portal → VM Overview** showing the Windows VM (name, region, status **Running**, public IP).
  * Screenshot of **Networking/NSG** inbound rule allowing **RDP (TCP 3389)** (rule name, priority, source, destination).

* **Connection evidence (RDP)**

  * Screenshot of the **RDP connection dialog** or the downloaded `.rdp` file properties.
  * Screenshot of a **successful RDP session** on the VM desktop showing:

    * Start menu/taskbar visible, and
    * A proof command, e.g., **Command Prompt** with `whoami` and `ipconfig` outputs.

* **Verification evidence**

  * Screenshot of **System Information** (This PC → Properties or Settings → System → About).
  * Screenshot of **internet connectivity** (browser open to microsoft.com or similar).

* **If you used Azure CLI (optional)**

  * The **exact commands** used (`az group create`, `az vm create`, `az vm list-ip-addresses`, `az vm open-port`).
  * Screenshot/snippet of **CLI output** confirming VM creation and public IP.

* **Cleanup (optional but recommended)**

  * Screenshot or command output confirming deletion:

    * `az vm delete ...` and/or `az group delete ...`, or
    * Portal screenshots showing the VM/resource group removed.

## **Submission**

Upon completion, add your deliverables to git. Then commit git and push your branch to the remote. Make a pull request and paste the PR link in the submission field in the Student Portal.

## Summary

In this lab, you successfully:

1. **Created** a Windows Virtual Machine on Azure using the Azure Portal and optionally via the Azure CLI.
2. **Configured** networking and security settings to allow secure RDP access.
3. **Connected** to your Windows VM using Remote Desktop Protocol (RDP) from your local machine.
4. **Verified** the VM’s functionality by checking system information and internet connectivity.
5. **Cleaned up** resources to prevent unnecessary costs.

**Congratulations!** You’ve mastered the basics of creating and connecting to an Azure Windows Virtual Machine. This foundational skill enables you to deploy a wide range of applications, host services, and manage remote desktops in the Azure cloud. Continue exploring Azure’s rich ecosystem to leverage more advanced features and services—*and don’t forget to celebrate your new skills with a bit of confetti!*


*Happy Cloud Computing! :tada:*