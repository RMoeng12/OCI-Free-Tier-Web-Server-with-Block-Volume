OCI instance setup, block volume configuration, and instructions
# OCI Free Tier Web Server with Block Volume

This repository documents the deployment of a secure, scalable web server on **Oracle Cloud Infrastructure (OCI) Free Tier**, with persistent storage provided by an attached **Block Volume**. It demonstrates practical cloud architecture skills including compute provisioning, storage attachment, filesystem configuration, and service persistence.

---

## 🚀 Project Overview
- **Compute Instance**: Oracle Linux VM (Always Free `VM.Standard.E2.1.Micro`)
- **Web Server**: Apache HTTP Server serving static content
- **Storage**: OCI Block Volume attached via iSCSI
- **Persistence**: Filesystem mounted and configured in `/etc/fstab` for auto-mount after reboot
- **Networking**: Public IP + VCN for external access

This setup ensures that web content is served reliably from a block volume, independent of the VM lifecycle.

---

## 🛠️ Deployment Steps

### 1. Provision Resources
- Create a **VCN** with subnets and gateways.
- Launch a **compute instance** in the same compartment and availability domain.
- Create a **block volume** in the same compartment and AD.

### 2. Attach Block Volume
- Navigate to **Instance Details → Attached Block Volumes → Attach Block Volume**.
- Select the block volume and choose **iSCSI**.
- Copy the generated **iSCSI commands**.

### 3. Connect via SSH
```bash
ssh opc@<your-instance-public-ip>

**Run iSCSI Commands**
sudo iscsiadm -m node -o new -T <IQN> -p <IP>:3260
sudo iscsiadm -m node -o update -T <IQN> -n node.startup -v automatic
sudo iscsiadm -m node -T <IQN> -p <IP>:3260 -l
sudo iscsiadm -m session

**Format and Mount**
sudo lsblk
sudo mkfs.ext4 /dev/sdb
sudo mkdir /mnt/web-data
sudo mount /dev/sdb /mnt/web-data
sudo cp /var/www/html/index.html /mnt/web-data/
sudo chown -R apache:apache /mnt/web-data

**Persist Mount**
sudo blkid /dev/sdb
sudo vi /etc/fstab

**Add**
UUID=<YOUR_UUID> /mnt/web-data ext4 defaults,_netdev 0 0

**Test**
sudo umount /mnt/web-data
sudo mount -a
