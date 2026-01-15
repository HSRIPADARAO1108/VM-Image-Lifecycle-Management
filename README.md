# Azure Cloud Infrastructure Lab: Marketplace vs. Custom Image Automation

## 📌 Project Overview
This project demonstrates a professional cloud deployment lifecycle in Microsoft Azure. I explored the transition from manual server configuration using **Community Marketplace Images** to automated, scalable deployments using a **Custom "Golden" Image** stored in an **Azure Compute Gallery**.

---

## 🛠️ Deployment Process Step-by-Step

### Part 3: Community VM Image (Manual Setup)
* **Objective:** Deploy a base Fedora 40 image and configure it for a web application.
* **Process:** 1. Provisioned a Fedora 40 VM from the Azure Marketplace.
    2. Connected via SSH to perform manual software installation.
    3. Installed Nginx using `sudo dnf install nginx -y`.
* **Challenge:** Every new deployment requires manual intervention, which is slow and prone to configuration drift.

### Part 4: Custom VM Image (Automated Setup)
* **Objective:** Create a reusable template to eliminate manual installation steps.
* **Process:**
    1. **Baking:** Configured the source VM and ran `sudo systemctl enable nginx` to ensure the service starts automatically on boot.
    2. **Generalization:** Prepared the OS using `sudo waagent -deprovision+user -force` to remove unique identifiers.
    3. **Capture:** Deallocated the VM and captured it into an **Azure Compute Gallery** as Version `1.1.02`.
    4. **Verification:** Deployed a "Final-Clone" VM from this image, which functioned immediately upon boot without manual setup.

---

## 🌐 Resource Configurations

### 1. Networking Configuration
* **Virtual Network (VNet):** All resources were isolated within a specific VNet to manage traffic flow.
* **Public IP Addressing:** Dynamic Public IPs were used for both the source and cloned VMs to verify web accessibility.
* **Connectivity Verification:** Verified end-to-end connectivity using `curl -I localhost` (internal) and browser-based HTTP requests (external).

### 2. Security (Network Security Group)
A **Network Security Group (NSG)** was implemented with the following Inbound Rules to secure the environment:
| Priority | Name | Port | Protocol | Action | Purpose |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1000 | AllowSSH | 22 | TCP | Allow | Remote Management |
| 1010 | AllowHTTP | 80 | TCP | Allow | Web Server Traffic |

### 3. Monitoring & Diagnostics
* **Boot Diagnostics:** Used to monitor the "Generalization" process and ensure the cloned VM booted correctly from the custom image.
* **Activity Logs:** Utilized to verify the successful creation of the Image Definition and its replication across the gallery.

---

## 🖼️ Screenshots & Evidence

### Source Configuration
> ![Source VM Status](./images/01-source-vm-active.png)
> *Source VM (Fedora01) in a running state.*

> ![Source Nginx Verify](./images/03-source-curl-verify.png)
> *Terminal confirmation of successful Nginx installation (HTTP 200 OK).*

### Image Preparation
> ![VM Deallocated](./images/04-vm-deallocated.png)
> *VM Stopped and Deallocated, a mandatory state for successful image capture.*

### Automated Success
> ![Clone Web Verify](./images/06-clone-web-verify.png)
> *The cloned VM displaying the Fedora/Nginx test page on its own Public IP instantly.*

---

## 💡 Insights & Challenges (For Presentation)

### Challenges Faced:
* **The "Dead Service" Bug:** During initial cloning, Nginx was installed but not running.
* **Solution:** I discovered that for a "Golden Image," services must be **Enabled** (`systemctl enable`) before capture. This ensures the OS starts the service automatically upon deployment.

### Key Insights:
* **Community Images:** Great for flexibility and one-off testing but inefficient for production.
* **Custom Images:** Provide 100% consistency, faster deployment times, and are essential for auto-scaling and disaster recovery.

---

## 🎓 Conclusion
By moving from manual installations to an **Azure Compute Gallery** workflow, I reduced deployment time by approximately 80%. This project proves that "baking" configurations into images is a superior method for maintaining reliable cloud infrastructure.
