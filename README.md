# Active Directory Infrastructure: Setting up a Domain Controller as a DNS Server With a Client

## 📌 Environments and Technologies Used

- **Microsoft Azure** (Virtual Machines/Cloud Computing)
- **Remote Desktop**
- **Active Directory Domain Services**
- **PowerShell**

## 🖥️ Operating Systems Used

- **Windows Server 2022**
- **Windows 10 (21H2)**

## 📂 High-Level Deployment and Configuration Steps

1. **Creating the Virtual Machines**
   - Deploying the Domain Controller
   - Deploying the Client Machine
2. **Configuring the Domain Controller’s Network Interface**
3. **Disabling the Domain Controller’s Firewall**
4. **Configuring the Client Machine’s DNS Settings**
5. **Validating the Connection Between Domain Controller and Client**

---

## Let's Begin

### **Step 1: Creating the Virtual Machines**
We will create two Virtual Machines (VMs) within the same Virtual Network. One will function as the **Domain Controller (DC-1)**, and the other will serve as a **Client Machine**.

#### **Setting Up the Resource Group and Virtual Network**

- Create a **Resource Group** and name it `ActiveDirectory`.
  ![image](https://github.com/user-attachments/assets/d8a1b9c4-8190-4d0a-8486-8030774e43f6)
  
- Create a **Virtual Network (VNet)** within the same resource group and name it `ActiveDirectory-VN`.
  ![image](https://github.com/user-attachments/assets/40e9facc-e102-4c77-848a-9efeed86ba74)

#### **Deploying the Domain Controller (DC-1)**

- Create a new **Virtual Machine** and name it `DC-1`.
- Assign it to the **ActiveDirectory** Resource Group.
- Set the **Region** to `East US 2`.
- Select **Windows Server 2022 Datacenter Azure Edition** as the Image.
  ![image](https://github.com/user-attachments/assets/74660095-16ec-4324-9ec0-9b00ba06d0e6)
- Choose a **VM Size** with at least **2 vCPUs and 8 GiB RAM**.
  ![image](https://github.com/user-attachments/assets/0b6a4362-c9c6-4040-ab49-62c9820c4bfd)
- Set login credentials (Admin username and password).
- Accept licensing terms and proceed.
  ![image](https://github.com/user-attachments/assets/ec242fbc-b108-4040-a9c1-60c3388dc6b6)

- In the **Networking** tab, ensure it is placed in the `ActiveDirectory-VN`.
  ![image](https://github.com/user-attachments/assets/8cbb03ee-3d1e-4a7e-860f-64e0cb9357e0)
- Click **Review + Create** and deploy the VM.

#### **Deploying the Client Machine**

- Create another **Virtual Machine** and name it `Client`.
- Assign it to the same **ActiveDirectory** Resource Group.
- Set **Region** to `East US 2`.
- Select **Windows 10 Pro** as the Image.
  ![image](https://github.com/user-attachments/assets/0ed0a650-2d84-45cf-aeec-a6ed89774c8f)
- Use the same **VM Size** as DC-1.
- Set login credentials.
- Accept licensing terms.
- Ensure it is placed in `ActiveDirectory-VN`.
- Click **Review + Create** and deploy the VM.
  ![image](https://github.com/user-attachments/assets/861d74f5-31c9-409e-9a00-70ef92bff85e)

---

### **Step 2: Configuring the Domain Controller’s Network Interface**

To maintain a reliable environment, the **Domain Controller’s IP address** must remain **static**. If left as dynamic, the IP could change after a restart, breaking the Client's ability to locate the DC.

- Navigate to the **Virtual Machines** section in Azure.
- Open **DC-1’s settings** > **Networking** > **Network Interface**.
- Select **IP Configuration**.
  ![image](https://github.com/user-attachments/assets/d23073de-d87e-42b8-9d00-4619b9304913)
- Click on `ipconfig1`.
- Change the **IP allocation** from **Dynamic** to **Static**.
- Save and apply the changes.
  ![image](https://github.com/user-attachments/assets/17e1d344-59a5-4944-ae6e-840964d7ab7d)

---

### **Step 3: Disabling the Domain Controller’s Firewall** *(For Lab Purposes Only)*

While disabling the firewall entirely is not recommended in a real-world scenario, doing so here will allow for uninterrupted connectivity during this setup.

- **Remote into DC-1** using its **Public IP** via Remote Desktop.
  ![image](https://github.com/user-attachments/assets/73593185-7663-49d8-9d93-47ef25c6da9a)
  ![image](https://github.com/user-attachments/assets/fde8f60c-7578-419b-ab82-d94208e5f159)
- Upon logging in, **Windows Server Manager** will open.
  ![image](https://github.com/user-attachments/assets/9f1e72ff-4845-4be1-9848-1292711c74c0)
- Open **Run** (Win + R) and type `wf.msc`, then press **Enter**.
- In the **Windows Defender Firewall with Advanced Security** window:
  ![image](https://github.com/user-attachments/assets/2e98539b-f8dd-4c5d-9d73-155000361c5e)
  - Open **Windows Defender Firewall Properties**.
  - Set the **Firewall State** to **Off** for:
    - Domain Profile
    - Private Profile
    - Public Profile
      ![image](https://github.com/user-attachments/assets/0b67b132-1c24-4c47-ad53-e10f47d8c7bb)
      ![image](https://github.com/user-attachments/assets/a7292310-e80e-4d87-99a9-29c94e30f2e9)
      ![image](https://github.com/user-attachments/assets/b7b880a8-9a2b-4a19-9eba-f7c92e1b6411)
  - Click **Apply** > **OK**.
  - All set, lets **Shut down our Domain Controller** for now

---

### **Step 4: Configuring the Client Machine’s DNS Settings**

For the Client Machine to resolve requests through the Domain Controller, it must use **DC-1’s Private IP** as its DNS server.

- In **Azure Virtual Machines**, locate `DC-1` and copy its **Private IP address**.
  ![image](https://github.com/user-attachments/assets/1680bb4e-4fef-4d02-90ae-c48a6981cdfb)

- Navigate to **Client’s settings** > **Networking** > **Network Interface**.
- Open **DNS Server** settings.
  ![image](https://github.com/user-attachments/assets/233db8b7-366a-4a57-99a7-bf8b8f017d5d)
- Select **Custom DNS** and enter `DC-1’s Private IP address`.
- Click **Save** and allow the changes to take effect.
  ![image](https://github.com/user-attachments/assets/21555256-f905-476e-8785-483f30d71c44)
- Restart **both the Client and DC-1 VMs**.
  ![image](https://github.com/user-attachments/assets/f053d56f-c19f-4a9f-a876-6cf185bdb275)

---

### **Step 5: Validating the Connection Between DC-1 & Client**

- Add the `Client` VM to **Remote Desktop**.
  ![image](https://github.com/user-attachments/assets/3aa6900f-6e58-446e-8713-6ee066f078d2)

- Open **both DC-1 and Client Machines** via Remote Desktop.
  ![image](https://github.com/user-attachments/assets/d0d37a57-8093-4334-8203-dcc09ada273e)


#### **Testing Network Connectivity**

- Open **PowerShell** on the Client Machine.
- Use the following command to **ping the Domain Controller**:
  ```powershell
  ping <DC-1’s Private IP>
  ```
  - A successful response confirms network connectivity.
    ![image](https://github.com/user-attachments/assets/eb33340d-2496-4ecd-bebb-37086db1dbab)


#### **Verifying DNS Configuration**

- Open **PowerShell** on both `DC-1` and `Client`.
- Run the following command on both machines:
  ```powershell
  ipconfig /all
  ```
  - Compare **DC-1’s IPv4 Address** with **Client’s DNS Server**.
  - If they match, the configuration was successful.
    ![image](https://github.com/user-attachments/assets/0c893dd5-fa29-45ed-98d2-3de439c4c868)


---

### **Conclusion**

At this stage, we have successfully:

- **Deployed and configured a Domain Controller**.
- **Set up a Client Machine** to communicate with the DC.
- **Established the Domain Controller as the DNS Server**.
- **Validated connectivity between both systems**.

This setup serves as the foundation for building an **Active Directory environment** and allows for further domain-related configurations such as **user authentication, group policies, and domain-joined machines**. With these fundamentals in place, we are now ready to explore more advanced Active Directory implementations.


